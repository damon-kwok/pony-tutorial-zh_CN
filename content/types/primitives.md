---
title: "基元类（Primitives）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 30
toc: true
---

<!-- A __primitive__ is similar to a __class__, but there are two critical differences: -->
__primitive（基元类）__ 和 __class（类）__ 有两点主要区别：

<!-- 1. A __primitive__ has no fields. -->
1. __基元类__ 没有字段。
<!-- 2. There is only one instance of a user-defined __primitive__. -->
2. 一个 __基元类__ 只会产生一个实例。

<!-- Having no fields means primitives are never mutable. Having a single instance means that if your code calls a constructor on a __primitive__ type, it always gets the same result back (except for built-in "machine word" primitives, covered below). -->
没有字段就天然具备不可变性不会产生副作用。只有一个实例的意味着 __primitive__ 的构造函数总是会返回相同的实例对象（下面介绍的内置内省"machine word" primitives除外）。

<!-- ## What can you use a __primitive__ for?__primitives__ -->
## __基元类__ 有什么用途？

<!-- There are three main uses of primitives (four, if you count built-in "machine word" primitives). -->
基元类有三种主要用途（如果算上内置的"machine word" primitives则有四种）。

<!-- 1. As a "marker value". For example, Pony often uses the __primitive__ `None` to indicate that something has "no value". Of course, it _does_ have a value, so that you can check what it is, and the value is the single instance of `None`. -->
<!-- 2. As an "enumeration" type. By having a __union__ of __primitive__ types, you can have a type-safe enumeration. We'll cover __union__ types later. -->
<!-- 3. As a "collection of functions". Since primitives can have functions, you can group functions together in a primitive type. You can see this in the standard library, where path handling functions are grouped in the __primitive__ `Path`, for example. -->
1. 作为"标记值"。例如，Pony经常使用`None`基元类来表示某事物没有值。当然，它其实具有一个值，并且该值就是`None`的实例。
2. 作为"枚举"类型。通过创建 __primitive__ 类型的 __union__ ，可以使用类型安全的枚举。稍后我们将介绍 __union__ 类型。
3. 作为"函数集合"。由于基元类也可以有成员函数，因此可以用基元类来做函数分类。例如，标准库中的路径处理相关的函数都被放在`Path`中（Path就是一个基元类）。

```pony
// 2 "marker values"
primitive OpenedDoor
primitive ClosedDoor

// An "enumeration" type
type DoorState is (OpenedDoor | ClosedDoor)

// A collection of functions
primitive BasicMath
  fun add(a: U64, b: U64): U64 =>
    a + b

  fun multiply(a: U64, b: U64): U64 =>
    a * b

actor Main
  new create(env: Env) =>
    let doorState : DoorState = ClosedDoor
    let isDoorOpen : Bool = match doorState
      | OpenedDoor => true
      | ClosedDoor => false
    end
    env.out.print("Is door open? " + isDoorOpen.string())
    env.out.print("2 + 3 = " + BasicMath.add(2,3).string())
```

<!-- Primitives are quite powerful, particularly as enumerations. Unlike enumerations in other languages, each "value" in the enumeration is a complete type, which makes attaching data and functionality to enumeration values easy. -->
基元类非常强大，尤其是作为枚举使用时。与其他语言中的枚举不同，Pony中枚举的每个`值`都是一个完整类型，因此可以将数据和函数附加到枚举值上。

<!-- ## Built-in primitive types -->
## 内置基元类

<!-- The __primitive__ keyword is also used to introduce certain built-in "machine word" types. Other than having a value associated with them, these work like user-defined primitives. These are: -->
_primitive_ 关键字还用于引入某些内置的"machine word"类型。除了具有与之关联的值外，这些还类似于用户定义的原语。这些是：

<!-- * __Bool__. This is a 1-bit value that is either `true` or `false`. -->
<!-- * __ISize, ILong, I8, I16, I32, I64, I128__. Signed integers of various widths. -->
<!-- * __USize, ULong, U8, U16, U32, U64, U128__. Unsigned integers of various widths. -->
<!-- * __F32, F64__. Floating point numbers of various widths. -->
* __Bool__ 。这是一个1位值，true或false。
* __ISize，ILong，I8，I16，I32，I6​​4，I128__ 。有符号整数。
* __USize，ULong，U8，U16，U32，U64，U128__ 。无符号整数。
* __F32，F64__ 。浮点数。

<!-- __ISize/USize__ correspond to the bit width of the native type `size_t`, which varies by platform. __ILong/ULong__ similarly correspond to the bit width of the native type `long`, which also varies by platform. The bit width of a native `int` is the same across all the platforms that Pony supports, and you can use __I32/U32__ for this. -->
__ISize / USize__ 对应于本机类型`size_t`的位宽，该位宽因平台而异。 __ILong / ULong__ 类似地对应于本机类型` long`的位宽，该位宽也因平台而异。在所有Pony支持的平台上，本机`int`的位宽均相同，您可以使用 __I32 / U32__ 来实现。

<!-- ## Primitive initialisation and finalisation  -->
## 基元类的初始化和销毁

<!-- Primitives can have two special functions, `_init` and `_final`. `_init` is called before any actor starts. `_final` is called after all actors have terminated. The two functions take no parameter. The `_init` and `_final` functions for different primitives always run sequentially. -->
基元类有两个特殊的成员函数，`_init`和`_final`。在任何actor开始之前都会调用`_init`。在所有参与者都终止之后，将调用`_final`。这两个函数不带参数。不同的基元类的`_init`和`_final`函数始终按顺序执行。

<!-- A common use case for this is initialising and cleaning up C libraries without risking untimely use by an actor. -->
一个常见的用例是初始化和清理C语言的库，而不用承担actor不当使用的风险。
