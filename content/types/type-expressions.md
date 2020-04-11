---
title: "类型表达式（Type Expressions）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 70
toc: true
---

<!-- The types we've talked about so far can also be combined in __type expressions__. If you're used to object-oriented programming, you may not have seen these before, but they are common in functional programming. A __type expression__ is also called an __algebraic data type__. -->
到目前为止，我们已经学习过的类型都可以应用在在 __类型表达式（type expressions）__ 中。如果您熟悉面向对象的编程，那你可能会觉得这个叫法很奇怪，但是它们在函数式编程中很常见。 __类型表达式__ 也称为 __代数数据类型__ 。

<!-- There are three kinds of type expression: __tuples__, __unions__, and __intersections__. -->
有三种类型表达式：__（元组）tuples__ ， __（类型联合体）unions__ 和 __（集合）intersections__ 。

<!-- ## Tuples -->
## 元组（Tuples）

<!-- A __tuple__ type is a sequence of types. For example, if we wanted something that was a `String` followed by a `U64`, we would write this: -->
一个 __元组__ 是一个类型序列。列入，如果想要一个`字符串`后面跟着一个`U64整数`，可以这样写：

```pony
var x: (String, U64)
x = ("hi", 3)
x = ("bye", 7)
```

<!-- All type expressions are written in parentheses, and the elements of a tuple are separated by a comma. We can also destructure a tuple using assignment: -->
所有的类型表达式都包裹在一对小括号中，元组的元素分隔符是逗号。我们这样对一个元组进行解析：

```pony
(var y, var z) = x
```

<!-- Or we can access the elements of a tuple directly: -->
或这样用这样的方式访问元组中的某个元素：

```pony
var y = x._1
var z = x._2
```

<!-- Note that there's no way to assign to an element of a tuple. Instead, you can just reassign the entire tuple, like this: -->
需要注意的是，不能对元组的某一个元素进行单独赋值。正确的做法是重新赋值整个元组，就像这样：

```pony
x = ("wombat", x._2)
```

<!-- __Why use a tuple instead of a class?__ Tuples are a way to express a collection of values that doesn't have any associated code or expected behaviour. Basically, if you just need a quick collection of things, maybe to return more than one value from a function, for example, you can use a tuple. -->
__为什么要用元组而不是类？__ 元组是一种集合表达式，集合中包含了具有预期行为的值，不含任何方法或行为。如果您只需要快速收集事物，例如从一个函数返回多个值，则可以使用一个元组。

<!-- ## Unions -->
## Unions（类型联合体）

<!-- A __union__ type is written like a __tuple__, but it uses a `|` (pronounced "or" when reading the type) instead of a `,` between its elements. Where a tuple represents a collection of values, a union represents a _single_ value that can be any of the specified types. -->
__类型联合体__ 的定义方式类似 __元组__ ，元组的元素使用`,`分隔符，类型联合体使用`|`分隔符。元组表示一堆值的集合，类型联合体表示一个值，该值的类型可以是元素列表中定义的其中一个类型。

<!-- Unions can be used for tons of stuff that require multiple concepts in other languages. For example, optional values, enumerations, marker values, and more. -->
在与其他语言交互时，类型联合体可以用于描述很多其他语言中的概念。例如，可选值，枚举，标记值等。

```pony
var x: (String | None)
```

<!-- Here we have an example of using a union to express an optional type, where `x` might be a `String`, but it also might be `None`. -->
上面的例子中，我们顶一个了一个变量`x`，它的值可以是`String`类型，也可以为`None`。

<!-- ## Intersections -->
## Intersections（类型集合）

<!-- An __intersection__ uses a `&` (pronounced "and" when reading the type) between its elements. It represents the exact opposite of a union: it is a _single_ value that is _all_ of the specified types, at the same time! -->
__类型集合__ 使用`&`作为元素分隔符。它与类型联合体正好相反：该值的类型是元素列表中定义的 _所有_ 类型的组合。

<!-- This can be very useful for combining traits or interfaces, for example. Here's something from the standard library: -->
类型集合的特性可以让我们很容易的将特征和接口组合在一起。例如，标准库中的Map的定义方式：

```pony
type Map[K: (Hashable box & Comparable[K] box), V] is HashMap[K, V, HashEq[K]]
```

<!-- That's a fairly complex type alias, but let's look at the constraint of `K`. It's `(Hashable box & Comparable[K] box)`, which means `K` is `Hashable` _and_ it is `Comparable[K]`, at the same time. -->
这是一个相当复杂的类型别名。让我们看一下`K`的定义：`(Hashable box & Comparable[K] box)`，这表示`K`是一个`Hashable` _同时_ 它也具有`Comparable[K]`的特征。

<!-- ## Combining type expressions -->
## 类型表达式组合（Combining type expressions）

<!-- Type expressions can be combined into more complex types. Here's another example from the standard library: -->
类型表达式可以组合出更复杂的类型。这是标准库中的另一个示例：

```pony
var _array: Array[((K, V) | _MapEmpty | _MapDeleted)]
```

<!-- Here we have an array where each element is either a tuple of `(K, V)` or a `_MapEmpty` or a `_MapDeleted`. -->
在这里，我们定义了一个数组类型的变量，数组的元素可以是`(K, V)`类型的元组，也可以是`_MapEmpty` 或 `_MapDeleted`。

<!-- Because every type expression has parentheses around it, they are actually easy to read once you get the hang of it. However, if you use a complex type expression often, it can be nice to provide a type alias for it. -->
每个类型表达式都有括号，所以了解了这一点，实际上还是很容易阅读的。如果遇到需要高频率使用复杂的类型表达式，最好为它提供一个类型别名。

```pony
type Number is (Signed | Unsigned | Float)

type Signed is (I8 | I16 | I32 | I64 | I128)

type Unsigned is (U8 | U16 | U32 | U64 | U128)

type Float is (F32 | F64)
```

<!-- Those are all type aliases used by the standard library. -->
上面的类型别名定义都来自于标准库。

<!-- __Is `Number` a type alias for a type expression that contains other type aliases?__ Yes! Fun, and convenient. -->
__`Number`是一个类型别名，它定义中中还可以包含其他类型别名？__ 没错，这是个非常有趣和方便的特性。
