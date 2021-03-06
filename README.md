# sanctuary-type-classes

The [Fantasy Land Specification][FL] "specifies interoperability of common
algebraic structures" by defining a number of type classes. For each type
class, it states laws which every member of a type must obey in order for
the type to be a member of the type class. In order for the Maybe type to
be considered a [Functor][], for example, every `Maybe a` value must have
a `fantasy-land/map` method which obeys the identity and composition laws.

This project provides:

  - [`TypeClass`](#TypeClass), a function for defining type classes;
  - one `TypeClass` value for each Fantasy Land type class;
  - lawful Fantasy Land methods for JavaScript's built-in types;
  - one function for each Fantasy Land method; and
  - several functions derived from these functions.

## Type-class hierarchy

<pre>
 <a href="#Setoid">Setoid</a>   <a href="#Semigroupoid">Semigroupoid</a>  <a href="#Semigroup">Semigroup</a>   <a href="#Foldable">Foldable</a>        <a href="#Functor">Functor</a>      <a href="#Contravariant">Contravariant</a>  <a href="#Filterable">Filterable</a>
(<a href="#equals">equals</a>)    (<a href="#compose">compose</a>)    (<a href="#concat">concat</a>)   (<a href="#reduce">reduce</a>)         (<a href="#map">map</a>)        (<a href="#contramap">contramap</a>)    (<a href="#filter">filter</a>)
    |           |           |           \         / | | | | \
    |           |           |            \       /  | | | |  \
    |           |           |             \     /   | | | |   \
    |           |           |              \   /    | | | |    \
    |           |           |               \ /     | | | |     \
   <a href="#Ord">Ord</a>      <a href="#Category">Category</a>     <a href="#Monoid">Monoid</a>         <a href="#Traversable">Traversable</a> | | | |      \
  (<a href="#lte">lte</a>)       (<a href="#id">id</a>)       (<a href="#empty">empty</a>)        (<a href="#traverse">traverse</a>)  / | | \       \
                            |                      /  | |  \       \
                            |                     /   / \   \       \
                            |             <a href="#Profunctor">Profunctor</a> /   \ <a href="#Bifunctor">Bifunctor</a> \
                            |              (<a href="#promap">promap</a>) /     \ (<a href="#bimap">bimap</a>)   \
                            |                      /       \           \
                          <a href="#Group">Group</a>                   /         \           \
                         (<a href="#invert">invert</a>)               <a href="#Alt">Alt</a>        <a href="#Apply">Apply</a>      <a href="#Extend">Extend</a>
                                               (<a href="#alt">alt</a>)        (<a href="#ap">ap</a>)     (<a href="#extend">extend</a>)
                                                /           / \           \
                                               /           /   \           \
                                              /           /     \           \
                                             /           /       \           \
                                            /           /         \           \
                                          <a href="#Plus">Plus</a>    <a href="#Applicative">Applicative</a>    <a href="#Chain">Chain</a>      <a href="#Comonad">Comonad</a>
                                         (<a href="#zero">zero</a>)       (<a href="#of">of</a>)      (<a href="#chain">chain</a>)    (<a href="#extract">extract</a>)
                                            \         / \         / \
                                             \       /   \       /   \
                                              \     /     \     /     \
                                               \   /       \   /       \
                                                \ /         \ /         \
                                            <a href="#Alternative">Alternative</a>    <a href="#Monad">Monad</a>     <a href="#ChainRec">ChainRec</a>
                                                                    (<a href="#chainRec">chainRec</a>)
</pre>

## API

<h4 name="TypeClass"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L150">TypeClass :: (String, String, Array TypeClass, a -⁠> Boolean) -⁠> TypeClass</a></code></h4>

The arguments are:

  - the name of the type class, prefixed by its npm package name;
  - the documentation URL of the type class;
  - an array of dependencies; and
  - a predicate which accepts any JavaScript value and returns `true`
    if the value satisfies the requirements of the type class; `false`
    otherwise.

Example:

```javascript
//    hasMethod :: String -> a -> Boolean
const hasMethod = name => x => x != null && typeof x[name] == 'function';

//    Foo :: TypeClass
const Foo = Z.TypeClass(
  'my-package/Foo',
  'http://example.com/my-package#Foo',
  [],
  hasMethod('foo')
);

//    Bar :: TypeClass
const Bar = Z.TypeClass(
  'my-package/Bar',
  'http://example.com/my-package#Bar',
  [Foo],
  hasMethod('bar')
);
```

Types whose values have a `foo` method are members of the Foo type class.
Members of the Foo type class whose values have a `bar` method are also
members of the Bar type class.

Each `TypeClass` value has a `test` field: a function which accepts
any JavaScript value and returns `true` if the value satisfies the
type class's predicate and the predicates of all the type class's
dependencies; `false` otherwise.

`TypeClass` values may be used with [sanctuary-def][type-classes]
to define parametrically polymorphic functions which verify their
type-class constraints at run time.

<h4 name="Setoid"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L293">Setoid :: TypeClass</a></code></h4>

`TypeClass` value for [Setoid][].

```javascript
> Setoid.test(null)
true
```

<h4 name="Ord"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L303">Ord :: TypeClass</a></code></h4>

`TypeClass` value for [Ord][].

```javascript
> Ord.test(0)
true

> Ord.test(Math.sqrt)
false
```

<h4 name="Semigroupoid"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L316">Semigroupoid :: TypeClass</a></code></h4>

`TypeClass` value for [Semigroupoid][].

```javascript
> Semigroupoid.test(Math.sqrt)
true

> Semigroupoid.test(0)
false
```

<h4 name="Category"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L329">Category :: TypeClass</a></code></h4>

`TypeClass` value for [Category][].

```javascript
> Category.test(Math.sqrt)
true

> Category.test(0)
false
```

<h4 name="Semigroup"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L342">Semigroup :: TypeClass</a></code></h4>

`TypeClass` value for [Semigroup][].

```javascript
> Semigroup.test('')
true

> Semigroup.test(0)
false
```

<h4 name="Monoid"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L355">Monoid :: TypeClass</a></code></h4>

`TypeClass` value for [Monoid][].

```javascript
> Monoid.test('')
true

> Monoid.test(0)
false
```

<h4 name="Group"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L368">Group :: TypeClass</a></code></h4>

`TypeClass` value for [Group][].

```javascript
> Group.test(Sum(0))
true

> Group.test('')
false
```

<h4 name="Filterable"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L381">Filterable :: TypeClass</a></code></h4>

`TypeClass` value for [Filterable][].

```javascript
> Filterable.test({})
true

> Filterable.test('')
false
```

<h4 name="Functor"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L394">Functor :: TypeClass</a></code></h4>

`TypeClass` value for [Functor][].

```javascript
> Functor.test([])
true

> Functor.test('')
false
```

<h4 name="Bifunctor"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L407">Bifunctor :: TypeClass</a></code></h4>

`TypeClass` value for [Bifunctor][].

```javascript
> Bifunctor.test(Tuple('foo', 64))
true

> Bifunctor.test([])
false
```

<h4 name="Profunctor"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L420">Profunctor :: TypeClass</a></code></h4>

`TypeClass` value for [Profunctor][].

```javascript
> Profunctor.test(Math.sqrt)
true

> Profunctor.test([])
false
```

<h4 name="Apply"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L433">Apply :: TypeClass</a></code></h4>

`TypeClass` value for [Apply][].

```javascript
> Apply.test([])
true

> Apply.test('')
false
```

<h4 name="Applicative"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L446">Applicative :: TypeClass</a></code></h4>

`TypeClass` value for [Applicative][].

```javascript
> Applicative.test([])
true

> Applicative.test({})
false
```

<h4 name="Chain"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L459">Chain :: TypeClass</a></code></h4>

`TypeClass` value for [Chain][].

```javascript
> Chain.test([])
true

> Chain.test({})
false
```

<h4 name="ChainRec"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L472">ChainRec :: TypeClass</a></code></h4>

`TypeClass` value for [ChainRec][].

```javascript
> ChainRec.test([])
true

> ChainRec.test({})
false
```

<h4 name="Monad"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L485">Monad :: TypeClass</a></code></h4>

`TypeClass` value for [Monad][].

```javascript
> Monad.test([])
true

> Monad.test({})
false
```

<h4 name="Alt"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L498">Alt :: TypeClass</a></code></h4>

`TypeClass` value for [Alt][].

```javascript
> Alt.test({})
true

> Alt.test('')
false
```

<h4 name="Plus"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L511">Plus :: TypeClass</a></code></h4>

`TypeClass` value for [Plus][].

```javascript
> Plus.test({})
true

> Plus.test('')
false
```

<h4 name="Alternative"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L524">Alternative :: TypeClass</a></code></h4>

`TypeClass` value for [Alternative][].

```javascript
> Alternative.test([])
true

> Alternative.test({})
false
```

<h4 name="Foldable"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L537">Foldable :: TypeClass</a></code></h4>

`TypeClass` value for [Foldable][].

```javascript
> Foldable.test({})
true

> Foldable.test('')
false
```

<h4 name="Traversable"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L550">Traversable :: TypeClass</a></code></h4>

`TypeClass` value for [Traversable][].

```javascript
> Traversable.test([])
true

> Traversable.test('')
false
```

<h4 name="Extend"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L563">Extend :: TypeClass</a></code></h4>

`TypeClass` value for [Extend][].

```javascript
> Extend.test([])
true

> Extend.test({})
false
```

<h4 name="Comonad"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L576">Comonad :: TypeClass</a></code></h4>

`TypeClass` value for [Comonad][].

```javascript
> Comonad.test(Identity(0))
true

> Comonad.test([])
false
```

<h4 name="Contravariant"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L589">Contravariant :: TypeClass</a></code></h4>

`TypeClass` value for [Contravariant][].

```javascript
> Contravariant.test(Math.sqrt)
true

> Contravariant.test([])
false
```

<h4 name="toString"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1170">toString :: a -⁠> String</a></code></h4>

Returns a useful string representation of its argument.

Dispatches to the argument's `toString` method if appropriate.

Where practical, `equals(eval(toString(x)), x) = true`.

`toString` implementations are provided for the following built-in types:
Null, Undefined, Boolean, Number, Date, String, Array, Arguments, Error,
and Object.

```javascript
> toString(-0)
'-0'

> toString(['foo', 'bar', 'baz'])
'["foo", "bar", "baz"]'

> toString({x: 1, y: 2, z: 3})
'{"x": 1, "y": 2, "z": 3}'

> toString(Cons(1, Cons(2, Cons(3, Nil))))
'Cons(1, Cons(2, Cons(3, Nil)))'
```

<h4 name="equals"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1218">equals :: (a, b) -⁠> Boolean</a></code></h4>

Returns `true` if its arguments are of the same type and equal according
to the type's [`fantasy-land/equals`][] method; `false` otherwise.

`fantasy-land/equals` implementations are provided for the following
built-in types: Null, Undefined, Boolean, Number, Date, RegExp, String,
Array, Arguments, Error, Object, and Function.

The algorithm supports circular data structures. Two arrays are equal
if they have the same index paths and for each path have equal values.
Two arrays which represent `[1, [1, [1, [1, [1, ...]]]]]`, for example,
are equal even if their internal structures differ. Two objects are equal
if they have the same property paths and for each path have equal values.

```javascript
> equals(0, -0)
true

> equals(NaN, NaN)
true

> equals(Cons('foo', Cons('bar', Nil)), Cons('foo', Cons('bar', Nil)))
true

> equals(Cons('foo', Cons('bar', Nil)), Cons('bar', Cons('foo', Nil)))
false
```

<h4 name="lt"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1268">lt :: (a, b) -⁠> Boolean</a></code></h4>

Returns `true` if its arguments are of the same type and the first is
less than the second according to the type's [`fantasy-land/lte`][]
method; `false` otherwise.

This function is derived from [`lte`](#lte).

See also [`gt`](#gt) and [`gte`](#gte).

```javascript
> lt(0, 0)
false

> lt(0, 1)
true

> lt(1, 0)
false
```

<h4 name="lte"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1292">lte :: (a, b) -⁠> Boolean</a></code></h4>

Returns `true` if its arguments are of the same type and the first
is less than or equal to the second according to the type's
[`fantasy-land/lte`][] method; `false` otherwise.

`fantasy-land/lte` implementations are provided for the following
built-in types: Null, Undefined, Boolean, Number, Date, String, Array,
Arguments, and Object.

The algorithm supports circular data structures in the same manner as
[`equals`](#equals).

See also [`lt`](#lt), [`gt`](#gt), and [`gte`](#gte).

```javascript
> lte(0, 0)
true

> lte(0, 1)
true

> lte(1, 0)
false
```

<h4 name="gt"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1339">gt :: (a, b) -⁠> Boolean</a></code></h4>

Returns `true` if its arguments are of the same type and the first is
greater than the second according to the type's [`fantasy-land/lte`][]
method; `false` otherwise.

This function is derived from [`lte`](#lte).

See also [`lt`](#lt) and [`gte`](#gte).

```javascript
> gt(0, 0)
false

> gt(0, 1)
false

> gt(1, 0)
true
```

<h4 name="gte"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1363">gte :: (a, b) -⁠> Boolean</a></code></h4>

Returns `true` if its arguments are of the same type and the first
is greater than or equal to the second according to the type's
[`fantasy-land/lte`][] method; `false` otherwise.

This function is derived from [`lte`](#lte).

See also [`lt`](#lt) and [`gt`](#gt).

```javascript
> gte(0, 0)
true

> gte(0, 1)
false

> gte(1, 0)
true
```

<h4 name="min"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1387">min :: Ord a => (a, a) -⁠> a</a></code></h4>

Returns the smaller of its two arguments.

This function is derived from [`lte`](#lte).

See also [`max`](#max).

```javascript
> min(10, 2)
2

> min(new Date('1999-12-31'), new Date('2000-01-01'))
new Date('1999-12-31')

> min('10', '2')
'10'
```

<h4 name="max"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1409">max :: Ord a => (a, a) -⁠> a</a></code></h4>

Returns the larger of its two arguments.

This function is derived from [`lte`](#lte).

See also [`min`](#min).

```javascript
> max(10, 2)
10

> max(new Date('1999-12-31'), new Date('2000-01-01'))
new Date('2000-01-01')

> max('10', '2')
'2'
```

<h4 name="compose"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1431">compose :: Semigroupoid c => (c j k, c i j) -⁠> c i k</a></code></h4>

Function wrapper for [`fantasy-land/compose`][].

`fantasy-land/compose` implementations are provided for the following
built-in types: Function.

```javascript
> compose(Math.sqrt, x => x + 1)(99)
10
```

<h4 name="id"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1446">id :: Category c => TypeRep c -⁠> c</a></code></h4>

Function wrapper for [`fantasy-land/id`][].

`fantasy-land/id` implementations are provided for the following
built-in types: Function.

```javascript
> id(Function)('foo')
'foo'
```

<h4 name="concat"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1461">concat :: Semigroup a => (a, a) -⁠> a</a></code></h4>

Function wrapper for [`fantasy-land/concat`][].

`fantasy-land/concat` implementations are provided for the following
built-in types: String, Array, and Object.

```javascript
> concat('abc', 'def')
'abcdef'

> concat([1, 2, 3], [4, 5, 6])
[1, 2, 3, 4, 5, 6]

> concat({x: 1, y: 2}, {y: 3, z: 4})
{x: 1, y: 3, z: 4}

> concat(Cons('foo', Cons('bar', Cons('baz', Nil))), Cons('quux', Nil))
Cons('foo', Cons('bar', Cons('baz', Cons('quux', Nil))))
```

<h4 name="empty"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1485">empty :: Monoid m => TypeRep m -⁠> m</a></code></h4>

Function wrapper for [`fantasy-land/empty`][].

`fantasy-land/empty` implementations are provided for the following
built-in types: String, Array, and Object.

```javascript
> empty(String)
''

> empty(Array)
[]

> empty(Object)
{}

> empty(List)
Nil
```

<h4 name="invert"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1509">invert :: Group g => g -⁠> g</a></code></h4>

Function wrapper for [`fantasy-land/invert`][].

```javascript
> invert(Sum(5))
Sum(-5)
```

<h4 name="filter"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1521">filter :: Filterable f => (a -⁠> Boolean, f a) -⁠> f a</a></code></h4>

Function wrapper for [`fantasy-land/filter`][]. Discards every element
which does not satisfy the predicate.

`fantasy-land/filter` implementations are provided for the following
built-in types: Array and Object.

See also [`reject`](#reject).

```javascript
> filter(x => x % 2 == 1, [1, 2, 3])
[1, 3]

> filter(x => x % 2 == 1, {x: 1, y: 2, z: 3})
{x: 1, z: 3}

> filter(x => x % 2 == 1, Cons(1, Cons(2, Cons(3, Nil))))
Cons(1, Cons(3, Nil))

> filter(x => x % 2 == 1, Nothing)
Nothing

> filter(x => x % 2 == 1, Just(0))
Nothing

> filter(x => x % 2 == 1, Just(1))
Just(1)
```

<h4 name="reject"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1554">reject :: Filterable f => (a -⁠> Boolean, f a) -⁠> f a</a></code></h4>

Discards every element which satisfies the predicate.

This function is derived from [`filter`](#filter).

```javascript
> reject(x => x % 2 == 1, [1, 2, 3])
[2]

> reject(x => x % 2 == 1, {x: 1, y: 2, z: 3})
{y: 2}

> reject(x => x % 2 == 1, Cons(1, Cons(2, Cons(3, Nil))))
Cons(2, Nil)

> reject(x => x % 2 == 1, Nothing)
Nothing

> reject(x => x % 2 == 1, Just(0))
Just(0)

> reject(x => x % 2 == 1, Just(1))
Nothing
```

<h4 name="takeWhile"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1583">takeWhile :: Filterable f => (a -⁠> Boolean, f a) -⁠> f a</a></code></h4>

Discards the first element which does not satisfy the predicate, and all
subsequent elements.

This function is derived from [`filter`](#filter).

See also [`dropWhile`](#dropWhile).

```javascript
> takeWhile(s => /x/.test(s), ['xy', 'xz', 'yx', 'yz', 'zx', 'zy'])
['xy', 'xz', 'yx']

> takeWhile(s => /y/.test(s), ['xy', 'xz', 'yx', 'yz', 'zx', 'zy'])
['xy']

> takeWhile(s => /z/.test(s), ['xy', 'xz', 'yx', 'yz', 'zx', 'zy'])
[]
```

<h4 name="dropWhile"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1607">dropWhile :: Filterable f => (a -⁠> Boolean, f a) -⁠> f a</a></code></h4>

Retains the first element which does not satisfy the predicate, and all
subsequent elements.

This function is derived from [`filter`](#filter).

See also [`takeWhile`](#takeWhile).

```javascript
> dropWhile(s => /x/.test(s), ['xy', 'xz', 'yx', 'yz', 'zx', 'zy'])
['yz', 'zx', 'zy']

> dropWhile(s => /y/.test(s), ['xy', 'xz', 'yx', 'yz', 'zx', 'zy'])
['xz', 'yx', 'yz', 'zx', 'zy']

> dropWhile(s => /z/.test(s), ['xy', 'xz', 'yx', 'yz', 'zx', 'zy'])
['xy', 'xz', 'yx', 'yz', 'zx', 'zy']
```

<h4 name="map"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1631">map :: Functor f => (a -⁠> b, f a) -⁠> f b</a></code></h4>

Function wrapper for [`fantasy-land/map`][].

`fantasy-land/map` implementations are provided for the following
built-in types: Array, Object, and Function.

```javascript
> map(Math.sqrt, [1, 4, 9])
[1, 2, 3]

> map(Math.sqrt, {x: 1, y: 4, z: 9})
{x: 1, y: 2, z: 3}

> map(Math.sqrt, s => s.length)('Sanctuary')
3

> map(Math.sqrt, Tuple('foo', 64))
Tuple('foo', 8)

> map(Math.sqrt, Nil)
Nil

> map(Math.sqrt, Cons(1, Cons(4, Cons(9, Nil))))
Cons(1, Cons(2, Cons(3, Nil)))
```

<h4 name="bimap"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1661">bimap :: Bifunctor f => (a -⁠> b, c -⁠> d, f a c) -⁠> f b d</a></code></h4>

Function wrapper for [`fantasy-land/bimap`][].

```javascript
> bimap(s => s.toUpperCase(), Math.sqrt, Tuple('foo', 64))
Tuple('FOO', 8)
```

<h4 name="mapLeft"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1673">mapLeft :: Bifunctor f => (a -⁠> b, f a c) -⁠> f b c</a></code></h4>

Maps the given function over the left side of a Bifunctor.

```javascript
> mapLeft(Math.sqrt, Tuple(64, 9))
Tuple(8, 9)
```

<h4 name="promap"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1685">promap :: Profunctor p => (a -⁠> b, c -⁠> d, p b c) -⁠> p a d</a></code></h4>

Function wrapper for [`fantasy-land/promap`][].

`fantasy-land/promap` implementations are provided for the following
built-in types: Function.

```javascript
> promap(Math.abs, x => x + 1, Math.sqrt)(-100)
11
```

<h4 name="ap"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1700">ap :: Apply f => (f (a -⁠> b), f a) -⁠> f b</a></code></h4>

Function wrapper for [`fantasy-land/ap`][].

`fantasy-land/ap` implementations are provided for the following
built-in types: Array, Object, and Function.

```javascript
> ap([Math.sqrt, x => x * x], [1, 4, 9, 16, 25])
[1, 2, 3, 4, 5, 1, 16, 81, 256, 625]

> ap({a: Math.sqrt, b: x => x * x}, {a: 16, b: 10, c: 1})
{a: 4, b: 100}

> ap(s => n => s.slice(0, n), s => Math.ceil(s.length / 2))('Haskell')
'Hask'

> ap(Identity(Math.sqrt), Identity(64))
Identity(8)

> ap(Cons(Math.sqrt, Cons(x => x * x, Nil)), Cons(16, Cons(100, Nil)))
Cons(4, Cons(10, Cons(256, Cons(10000, Nil))))
```

<h4 name="lift2"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1727">lift2 :: Apply f => (a -⁠> b -⁠> c, f a, f b) -⁠> f c</a></code></h4>

Lifts `a -> b -> c` to `Apply f => f a -> f b -> f c` and returns the
result of applying this to the given arguments.

This function is derived from [`map`](#map) and [`ap`](#ap).

See also [`lift3`](#lift3).

```javascript
> lift2(x => y => Math.pow(x, y), [10], [1, 2, 3])
[10, 100, 1000]

> lift2(x => y => Math.pow(x, y), Identity(10), Identity(3))
Identity(1000)
```

<h4 name="lift3"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1747">lift3 :: Apply f => (a -⁠> b -⁠> c -⁠> d, f a, f b, f c) -⁠> f d</a></code></h4>

Lifts `a -> b -> c -> d` to `Apply f => f a -> f b -> f c -> f d` and
returns the result of applying this to the given arguments.

This function is derived from [`map`](#map) and [`ap`](#ap).

See also [`lift2`](#lift2).

```javascript
> lift3(x => y => z => x + z + y, ['<'], ['>'], ['foo', 'bar', 'baz'])
['<foo>', '<bar>', '<baz>']

> lift3(x => y => z => x + z + y, Identity('<'), Identity('>'), Identity('baz'))
Identity('<baz>')
```

<h4 name="apFirst"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1767">apFirst :: Apply f => (f a, f b) -⁠> f a</a></code></h4>

Combines two effectful actions, keeping only the result of the first.
Equivalent to Haskell's `(<*)` function.

This function is derived from [`lift2`](#lift2).

See also [`apSecond`](#apSecond).

```javascript
> apFirst([1, 2], [3, 4])
[1, 1, 2, 2]

> apFirst(Identity(1), Identity(2))
Identity(1)
```

<h4 name="apSecond"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1787">apSecond :: Apply f => (f a, f b) -⁠> f b</a></code></h4>

Combines two effectful actions, keeping only the result of the second.
Equivalent to Haskell's `(*>)` function.

This function is derived from [`lift2`](#lift2).

See also [`apFirst`](#apFirst).

```javascript
> apSecond([1, 2], [3, 4])
[3, 4, 3, 4]

> apSecond(Identity(1), Identity(2))
Identity(2)
```

<h4 name="of"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1807">of :: Applicative f => (TypeRep f, a) -⁠> f a</a></code></h4>

Function wrapper for [`fantasy-land/of`][].

`fantasy-land/of` implementations are provided for the following
built-in types: Array and Function.

```javascript
> of(Array, 42)
[42]

> of(Function, 42)(null)
42

> of(List, 42)
Cons(42, Nil)
```

<h4 name="append"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1828">append :: (Applicative f, Semigroup (f a)) => (a, f a) -⁠> f a</a></code></h4>

Returns the result of appending the first argument to the second.

This function is derived from [`concat`](#concat) and [`of`](#of).

See also [`prepend`](#prepend).

```javascript
> append(3, [1, 2])
[1, 2, 3]

> append(3, Cons(1, Cons(2, Nil)))
Cons(1, Cons(2, Cons(3, Nil)))
```

<h4 name="prepend"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1847">prepend :: (Applicative f, Semigroup (f a)) => (a, f a) -⁠> f a</a></code></h4>

Returns the result of prepending the first argument to the second.

This function is derived from [`concat`](#concat) and [`of`](#of).

See also [`append`](#append).

```javascript
> prepend(1, [2, 3])
[1, 2, 3]

> prepend(1, Cons(2, Cons(3, Nil)))
Cons(1, Cons(2, Cons(3, Nil)))
```

<h4 name="chain"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1866">chain :: Chain m => (a -⁠> m b, m a) -⁠> m b</a></code></h4>

Function wrapper for [`fantasy-land/chain`][].

`fantasy-land/chain` implementations are provided for the following
built-in types: Array and Function.

```javascript
> chain(x => [x, x], [1, 2, 3])
[1, 1, 2, 2, 3, 3]

> chain(x => x % 2 == 1 ? of(List, x) : Nil, Cons(1, Cons(2, Cons(3, Nil))))
Cons(1, Cons(3, Nil))

> chain(n => s => s.slice(0, n), s => Math.ceil(s.length / 2))('Haskell')
'Hask'
```

<h4 name="join"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1887">join :: Chain m => m (m a) -⁠> m a</a></code></h4>

Removes one level of nesting from a nested monadic structure.

This function is derived from [`chain`](#chain).

```javascript
> join([[1], [2], [3]])
[1, 2, 3]

> join([[[1, 2, 3]]])
[[1, 2, 3]]

> join(Identity(Identity(1)))
Identity(1)
```

<h4 name="chainRec"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1907">chainRec :: ChainRec m => (TypeRep m, (a -⁠> c, b -⁠> c, a) -⁠> m c, a) -⁠> m b</a></code></h4>

Function wrapper for [`fantasy-land/chainRec`][].

`fantasy-land/chainRec` implementations are provided for the following
built-in types: Array.

```javascript
> chainRec(
.   Array,
.   (next, done, s) => s.length == 2 ? [s + '!', s + '?'].map(done)
.                                    : [s + 'o', s + 'n'].map(next),
.   ''
. )
['oo!', 'oo?', 'on!', 'on?', 'no!', 'no?', 'nn!', 'nn?']
```

<h4 name="alt"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1927">alt :: Alt f => (f a, f a) -⁠> f a</a></code></h4>

Function wrapper for [`fantasy-land/alt`][].

`fantasy-land/alt` implementations are provided for the following
built-in types: Array and Object.

```javascript
> alt([1, 2, 3], [4, 5, 6])
[1, 2, 3, 4, 5, 6]

> alt(Nothing, Nothing)
Nothing

> alt(Nothing, Just(1))
Just(1)

> alt(Just(2), Just(3))
Just(2)
```

<h4 name="zero"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1951">zero :: Plus f => TypeRep f -⁠> f a</a></code></h4>

Function wrapper for [`fantasy-land/zero`][].

`fantasy-land/zero` implementations are provided for the following
built-in types: Array and Object.

```javascript
> zero(Array)
[]

> zero(Object)
{}

> zero(Maybe)
Nothing
```

<h4 name="reduce"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1972">reduce :: Foldable f => ((b, a) -⁠> b, b, f a) -⁠> b</a></code></h4>

Function wrapper for [`fantasy-land/reduce`][].

`fantasy-land/reduce` implementations are provided for the following
built-in types: Array and Object.

```javascript
> reduce((xs, x) => [x].concat(xs), [], [1, 2, 3])
[3, 2, 1]

> reduce(concat, '', Cons('foo', Cons('bar', Cons('baz', Nil))))
'foobarbaz'
```

<h4 name="size"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L1990">size :: Foldable f => f a -⁠> Integer</a></code></h4>

Returns the number of elements of the given structure.

This function is derived from [`reduce`](#reduce).

```javascript
> size([])
0

> size(['foo', 'bar', 'baz'])
3

> size(Nil)
0

> size(Cons('foo', Cons('bar', Cons('baz', Nil))))
3
```

<h4 name="elem"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2015">elem :: (Setoid a, Foldable f) => (a, f a) -⁠> Boolean</a></code></h4>

Takes a value and a structure and returns `true` if the
value is an element of the structure; `false` otherwise.

This function is derived from [`equals`](#equals) and
[`reduce`](#reduce).

```javascript
> elem('c', ['a', 'b', 'c'])
true

> elem('x', ['a', 'b', 'c'])
false

> elem(3, {x: 1, y: 2, z: 3})
true

> elem(8, {x: 1, y: 2, z: 3})
false

> elem(0, Just(0))
true

> elem(0, Just(1))
false

> elem(0, Nothing)
false
```

<h4 name="reverse"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2051">reverse :: (Applicative f, Foldable f, Monoid (f a)) => f a -⁠> f a</a></code></h4>

Reverses the elements of the given structure.

This function is derived from [`concat`](#concat), [`empty`](#empty),
[`of`](#of), and [`reduce`](#reduce).

```javascript
> reverse([1, 2, 3])
[3, 2, 1]

> reverse(Cons(1, Cons(2, Cons(3, Nil))))
Cons(3, Cons(2, Cons(1, Nil)))
```

<h4 name="sort"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2074">sort :: (Ord a, Applicative f, Foldable f, Monoid (f a)) => f a -⁠> f a</a></code></h4>

Performs a [stable sort][] of the elements of the given structure,
using [`lte`](#lte) for comparisons.

This function is derived from [`lte`](#lte), [`concat`](#concat),
[`empty`](#empty), [`of`](#of), and [`reduce`](#reduce).

See also [`sortBy`](#sortBy).

```javascript
> sort(['foo', 'bar', 'baz'])
['bar', 'baz', 'foo']

> sort([Just(2), Nothing, Just(1)])
[Nothing, Just(1), Just(2)]

> sort(Cons('foo', Cons('bar', Cons('baz', Nil))))
Cons('bar', Cons('baz', Cons('foo', Nil)))
```

<h4 name="sortBy"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2098">sortBy :: (Ord b, Applicative f, Foldable f, Monoid (f a)) => (a -⁠> b, f a) -⁠> f a</a></code></h4>

Performs a [stable sort][] of the elements of the given structure,
using [`lte`](#lte) to compare the values produced by applying the
given function to each element of the structure.

This function is derived from [`lte`](#lte), [`concat`](#concat),
[`empty`](#empty), [`of`](#of), and [`reduce`](#reduce).

See also [`sort`](#sort).

```javascript
> sortBy(s => s.length, ['red', 'green', 'blue'])
['red', 'blue', 'green']

> sortBy(s => s.length, ['black', 'white'])
['black', 'white']

> sortBy(s => s.length, ['white', 'black'])
['white', 'black']

> sortBy(s => s.length, Cons('red', Cons('green', Cons('blue', Nil))))
Cons('red', Cons('blue', Cons('green', Nil)))
```

<h4 name="traverse"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2143">traverse :: (Applicative f, Traversable t) => (TypeRep f, a -⁠> f b, t a) -⁠> f (t b)</a></code></h4>

Function wrapper for [`fantasy-land/traverse`][].

`fantasy-land/traverse` implementations are provided for the following
built-in types: Array and Object.

See also [`sequence`](#sequence).

```javascript
> traverse(Array, x => x, [[1, 2, 3], [4, 5]])
[[1, 4], [1, 5], [2, 4], [2, 5], [3, 4], [3, 5]]

> traverse(Identity, x => Identity(x + 1), [1, 2, 3])
Identity([2, 3, 4])
```

<h4 name="sequence"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2163">sequence :: (Applicative f, Traversable t) => (TypeRep f, t (f a)) -⁠> f (t a)</a></code></h4>

Inverts the given `t (f a)` to produce an `f (t a)`.

This function is derived from [`traverse`](#traverse).

```javascript
> sequence(Array, Identity([1, 2, 3]))
[Identity(1), Identity(2), Identity(3)]

> sequence(Identity, [Identity(1), Identity(2), Identity(3)])
Identity([1, 2, 3])
```

<h4 name="extend"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2180">extend :: Extend w => (w a -⁠> b, w a) -⁠> w b</a></code></h4>

Function wrapper for [`fantasy-land/extend`][].

`fantasy-land/extend` implementations are provided for the following
built-in types: Array and Function.

```javascript
> extend(ss => ss.join(''), ['x', 'y', 'z'])
['xyz', 'yz', 'z']

> extend(f => f([3, 4]), reverse)([1, 2])
[4, 3, 2, 1]
```

<h4 name="duplicate"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2198">duplicate :: Extend w => w a -⁠> w (w a)</a></code></h4>

Adds one level of nesting to a comonadic structure.

This function is derived from [`extend`](#extend).

```javascript
> duplicate(Identity(1))
Identity(Identity(1))

> duplicate([1])
[[1]]

> duplicate([1, 2, 3])
[[1, 2, 3], [2, 3], [3]]

> duplicate(reverse)([1, 2])([3, 4])
[4, 3, 2, 1]
```

<h4 name="extract"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2221">extract :: Comonad w => w a -⁠> a</a></code></h4>

Function wrapper for [`fantasy-land/extract`][].

```javascript
> extract(Identity(42))
42
```

<h4 name="contramap"><code><a href="https://github.com/sanctuary-js/sanctuary-type-classes/blob/v8.1.1/index.js#L2233">contramap :: Contravariant f => (b -⁠> a, f a) -⁠> f b</a></code></h4>

Function wrapper for [`fantasy-land/contramap`][].

`fantasy-land/contramap` implementations are provided for the following
built-in types: Function.

```javascript
> contramap(s => s.length, Math.sqrt)('Sanctuary')
3
```

[Alt]:                      https://github.com/fantasyland/fantasy-land/tree/v3.5.0#alt
[Alternative]:              https://github.com/fantasyland/fantasy-land/tree/v3.5.0#alternative
[Applicative]:              https://github.com/fantasyland/fantasy-land/tree/v3.5.0#applicative
[Apply]:                    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#apply
[Bifunctor]:                https://github.com/fantasyland/fantasy-land/tree/v3.5.0#bifunctor
[Category]:                 https://github.com/fantasyland/fantasy-land/tree/v3.5.0#category
[Chain]:                    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#chain
[ChainRec]:                 https://github.com/fantasyland/fantasy-land/tree/v3.5.0#chainrec
[Comonad]:                  https://github.com/fantasyland/fantasy-land/tree/v3.5.0#comonad
[Contravariant]:            https://github.com/fantasyland/fantasy-land/tree/v3.5.0#contravariant
[Extend]:                   https://github.com/fantasyland/fantasy-land/tree/v3.5.0#extend
[FL]:                       https://github.com/fantasyland/fantasy-land/tree/v3.5.0
[Filterable]:               https://github.com/fantasyland/fantasy-land/tree/v3.5.0#filterable
[Foldable]:                 https://github.com/fantasyland/fantasy-land/tree/v3.5.0#foldable
[Functor]:                  https://github.com/fantasyland/fantasy-land/tree/v3.5.0#functor
[Group]:                    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#group
[Monad]:                    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#monad
[Monoid]:                   https://github.com/fantasyland/fantasy-land/tree/v3.5.0#monoid
[Ord]:                      https://github.com/fantasyland/fantasy-land/tree/v3.5.0#ord
[Plus]:                     https://github.com/fantasyland/fantasy-land/tree/v3.5.0#plus
[Profunctor]:               https://github.com/fantasyland/fantasy-land/tree/v3.5.0#profunctor
[Semigroup]:                https://github.com/fantasyland/fantasy-land/tree/v3.5.0#semigroup
[Semigroupoid]:             https://github.com/fantasyland/fantasy-land/tree/v3.5.0#semigroupoid
[Setoid]:                   https://github.com/fantasyland/fantasy-land/tree/v3.5.0#setoid
[Traversable]:              https://github.com/fantasyland/fantasy-land/tree/v3.5.0#traversable
[`fantasy-land/alt`]:       https://github.com/fantasyland/fantasy-land/tree/v3.5.0#alt-method
[`fantasy-land/ap`]:        https://github.com/fantasyland/fantasy-land/tree/v3.5.0#ap-method
[`fantasy-land/bimap`]:     https://github.com/fantasyland/fantasy-land/tree/v3.5.0#bimap-method
[`fantasy-land/chain`]:     https://github.com/fantasyland/fantasy-land/tree/v3.5.0#chain-method
[`fantasy-land/chainRec`]:  https://github.com/fantasyland/fantasy-land/tree/v3.5.0#chainrec-method
[`fantasy-land/compose`]:   https://github.com/fantasyland/fantasy-land/tree/v3.5.0#compose-method
[`fantasy-land/concat`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#concat-method
[`fantasy-land/contramap`]: https://github.com/fantasyland/fantasy-land/tree/v3.5.0#contramap-method
[`fantasy-land/empty`]:     https://github.com/fantasyland/fantasy-land/tree/v3.5.0#empty-method
[`fantasy-land/equals`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#equals-method
[`fantasy-land/extend`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#extend-method
[`fantasy-land/extract`]:   https://github.com/fantasyland/fantasy-land/tree/v3.5.0#extract-method
[`fantasy-land/filter`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#filter-method
[`fantasy-land/id`]:        https://github.com/fantasyland/fantasy-land/tree/v3.5.0#id-method
[`fantasy-land/invert`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#invert-method
[`fantasy-land/lte`]:       https://github.com/fantasyland/fantasy-land/tree/v3.5.0#lte-method
[`fantasy-land/map`]:       https://github.com/fantasyland/fantasy-land/tree/v3.5.0#map-method
[`fantasy-land/of`]:        https://github.com/fantasyland/fantasy-land/tree/v3.5.0#of-method
[`fantasy-land/promap`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#promap-method
[`fantasy-land/reduce`]:    https://github.com/fantasyland/fantasy-land/tree/v3.5.0#reduce-method
[`fantasy-land/traverse`]:  https://github.com/fantasyland/fantasy-land/tree/v3.5.0#traverse-method
[`fantasy-land/zero`]:      https://github.com/fantasyland/fantasy-land/tree/v3.5.0#zero-method
[stable sort]:              https://en.wikipedia.org/wiki/Sorting_algorithm#Stability
[type-classes]:             https://github.com/sanctuary-js/sanctuary-def#type-classes
