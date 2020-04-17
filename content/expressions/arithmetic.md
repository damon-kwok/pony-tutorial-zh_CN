---
title: "算术运算符（Arithmetic）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 40
toc: true
---

<!-- Arithmetic is about the stuff you learn to do with numbers in primary school: Addition, Subtraction, Multiplication, Division and so on. Piece of cake. We all know that stuff. We nonetheless want to spend a whole section on this topic, because when it comes to computers the devil is in the details. -->
算术运算就是你在小学中学到的与数字有关的概念：加法，减法，乘法，除法等。小菜一碟。人人都知道这个东西。尽管如此，还是希望花点时间阅读下这一节教程，因为牵扯到计算机，魔鬼总是隐藏在细节中。

<!-- As introduced in [Primitives]({{< relref "types/primitives.md#built-in-primitive-types" >}}) numeric types in Pony are represented as a special kind of primitive that maps to machine words. Both integer types and floating point types support a rich set of arithmetic and bit-level operations. These are expressed as [Infix Operators]({{< relref "expressions/ops.md#infix-operators" >}}) that are implemented as plain functions on the numeric primitive types. -->
在[基元类]({{< relref "types/primitives.md#内置的基元类型（Built-in primitive types）" >}})章节中介绍过，Pony中的数字类型是一种映射到机器码的特殊原语。整数类型和浮点类型都支持一组丰富的算术运算和位运算。[中缀运算符]({{< relref "expressions/ops.md#中缀运算符（Infix Operators）" >}})，在数字基元类型上作为普通函数实现。

<!-- Pony focuses on two goals, performance and safety. From time to time, these two goals collide. This is true especially for arithmetic on integers and floating point numbers. Safe code should check for overflow, division by zero and other error conditions on each operation where it can happen. Pony tries to enforce as many safety invariants at compile time as it possibly can, but checks on arithmetic operations can only happen at runtime. Performant code should execute integer arithmetic as fast and with as few CPU cycles as possible. Checking for overflow is expensive, doing plain dangerous arithmetic that is possibly subject to overflow is cheap. -->
Pony语言专注于两个目标，性能和安全性。这两个目标有时会发生冲突。对于整数和浮点数的算术尤其如此。为了确保代码安全，应在每个可能发生计算操的作上检查溢出，被零除和其他错误情况。 Pony尝试在编译时强制执行尽可能多的安全性检查，但是对算术运算的检查只能在运行时进行。另一方面，从代码的性能角度考虑，应尽快的执行整数算术运算，并在尽可能少的CPU周期内执行完毕。检查溢出是昂贵的，导致溢出的`简单危险算术`是廉价的。

<!-- Pony provides different ways of doing arithmetic to give programmers the freedom to chose which operation suits best for them, the safe but slower operation or the fast one, because performance is crucial for the use case. -->
Pony提供了不同的算术运算方式，使程序员可以自由选择最适合他们的操作：`安全但较慢的操作`或`不太安全但快速的操作`，因为性能对于某些应用场景至关重要。

<!-- ## Integers -->
## 整数（Integers）

<!-- ### Ponys default Arithmetic -->
### 默认的算术运算符（Ponys default Arithmetic）

<!-- Doing arithmetic on integer types in Pony with the well known operators like `+`, `-`, `*`, `/` etc. tries to balance the needs for performance and correctness. All default arithmetic operations do not expose any undefined behaviour or error conditions. That means it handles both the cases for overflow/underflow and division by zero. Overflow/Underflow are handled with proper wrap around semantics, using one's completement on signed integers. In that respect we get behaviour like: -->
在处理整数的`+`, `-`, `*`, `/`算术运算上，Pony使用了一些技巧，试图平衡对性能和正确性的需求。所有默认的算术运算都不会暴露任何未定义的行为或错误条件。这意味着它可以处理上溢/下溢和零除的情况。上溢/下溢使用对带符号整数的补全来适当地环绕语义来处理。在这方面，我们得到如下行为：

```pony
// unsigned wrap-around on overflow
U32.max_value() + 1 == 0

// signed wrap-around on overflow/underflow
I32.min_value() - 1 == I32.max_value()
```

<!-- Division by zero is a special case, which affects the division `/` and remainder `%` operators. In Mathematics, division by zero is undefined. In order to avoid either defining division as partial, throwing an error on division by zero or introducing undefined behaviour for that case, the _normal_ division is defined to be `0` when the divisor is `0`. This might lead to silent errors, when used without care. Choose [Partial and checked Arithmetic](#partial-and-checked-arithmetic) to detect division by zero. -->
除零是一种特殊情况，它会影响除法运算符/和取余%运算符。在数学中，零除是不确定的。为了避免将除法定义为部分除法，在除数为零时引发错误或在这种情况下引入未定义的行为，当除数为`0`时，将 _normal_ 除法定义为`0`。如果不小心使用，可能会导致无提示错误。选择[部分和校验的算术](#partial-and-checked-arithmetic)以除以零。

<!-- In contrast to [Unsafe Arithmetic](#unsafe-arithmetic) default arithmetic comes with a small runtime overhead because unlike the unsafe variants, it does detect and handle overflow and division by zero. -->
与[Unsafe Arithmetic](#非安全的算术运算符（Unsafe Arithmetic）) 相比，默认算法具有一点点运行时开销，因为它与unsafe变体不同，它会检测并处理溢出和除零的情况。

---

Operator | Method | Description
---------|--------|------------
`+`      | add()  | wrap around on over-/underflow
`-`      | sub()  | wrap around on over-/underflow
`*`      | mul()  | wrap around on over-/underflow
`/`      | div()  | `x / 0 = 0`
`%`      | rem()  | `x % 0 = 0`
`%%`     | mod()  | `x %% 0 = 0`
`-`      | neg()  | wrap around on over-/underflow
`>>`     | shr()  | filled with zeros, so `x >> 1 == x/2` is true
`<<`     | shl()  | filled with zeros, so `x << 1 == x*2` is true

---

<!-- ### Unsafe Arithmetic -->
### 非安全的算术运算符（Unsafe Arithmetic）

<!-- Unsafe integer arithmetic comes close to what you can expect from integer arithmetic in C. No checks, _raw speed_, possibilities of overflow, underflow or division by zero. Like in C, overflow, underflow and division by zero scenarios are undefined. Don't rely on the results in these cases. It could be anything and is highly platform specific. Division by zero might even crash your program with a `SIGFPE`. Our suggestion is to use these operators only if you can make sure you can exclude these cases. -->
不安全的整数算术接近于C语言中的整数算术所期望的结果。不做任何检查， _raw speed_ ，上溢，下溢或被零除的可能性。像在C中一样，上溢，下溢和被零除的情况是不确定的。在这些情况下，请勿依赖结果。它可以是任何东西，并且高度特定于平台。被零除甚至可能会用SIGFPE使程序崩溃。我们的建议是仅在确保可以排除这些情况时才使用这些运算符。

<!-- Here is a list with all unsafe operations defined on Integers: -->
非安全的算术运算符函数别名的完整列表：

---

Operator | Method        | Undefined in case of
---------|---------------|---------------------
`+~`     | add_unsafe()  | Overflow  E.g. `I32.max_value() +~ I32(1)`
`-~`     | sub_unsafe()  | Overflow
`*~`     | mul_unsafe()  | Overflow.
`/~`     | div_unsafe()  | Division by zero and overflow. E.g. I32.min_value() / I32(-1)
`%~`     | rem_unsafe()  | Division by zero and overflow.
`%%~`    | mod_unsafe()  | Division by zero and overflow.
`-~`     | neg_unsafe()  | Overflow. E.g. `-~I32.max_value()`
`>>~`    | shr_unsafe()  | If non-zero bits are shifted out. E.g. `I32(1) >>~ U32(2)`
`<<~`    | shl_unsafe()  | If bits differing from the final sign bit are shifted out.

---

<!-- #### Unsafe Conversion -->
### 非安全的类型转换（Unsafe Conversion）

<!-- Converting between integer types in Pony needs to happen explicitly. Each numeric type can be converted explicitly into every other type. -->
在Pony中整数类型之间的转换需要明确进行。每个数字类型都可以显式转换为其他类型。

```pony
// converting an I32 to a 32 bit floating point
I32(12).f32()
```

<!-- For each conversion operation there exists an unsafe counterpart, that is much faster when converting from and to floating point numbers. All these unsafe conversion between numeric types are undefined if the target type is smaller than the source type, e.g. if we convert from `I64` to `F32`. -->
对于每个转换操作，都存在一个不安全的对应项，当从浮点数转换为浮点数时，转换会更快。如果目标类型长度小于源类型，例如，数字类型之间的所有这些不安全转换都是不确定的。如果我们将I64转换为F32：

```pony
// converting an I32 to a 32 bit floating point, the unsafe way
I32(12).f32_unsafe()

// an example for an undefined unsafe conversion
I64.max_value().f32_unsafe()

// an example for an undefined unsafe conversion, that is actually safe
I64(1).u8_unsafe()
```

<!-- Here is a full list of all available conversions for numeric types: -->
这是数字类型所有可用转换的完整列表：

---

Safe conversion | Unsafe conversion
----------------|------------------
u8()            |  u8_unsafe()
u16()           |  u16_unsafe()
u32()           |  u32_unsafe()
u64()           |  u64_unsafe()
u128()          |  u128_unsafe()
ulong()         |  ulong_unsafe()
usize()         |  usize_unsafe()
i8()            |  i8_unsafe()
i16()           |  i16_unsafe()
i32()           |  i32_unsafe()
i64()           |  i64_unsafe()
i128()          |  i128_unsafe()
ilong()         |  ilong_unsafe()
isize()         |  isize_unsafe()
f32()           |  f32_unsafe()
f64()           |  f64_unsafe()

---

### Partial and Checked Arithmetic

<!-- If overflow or division by zero are cases that need to be avoided and performance is no critical priority, partial or checked arithmetic offer great safety during runtime. Partial arithmetic operators error on overflow/underflow and division by zero. Checked arithmetic methods return a tuple of the result of the operation and a `Boolean` indicating overflow or other exceptional behaviour. -->
如果需要避免溢出或被零除，并且性能问题不太关键，那么部分或校验算法将在运行期间提供极大的安全性。上/下溢和除以零的部分算术运算符错误。经过检查的算术方法将返回运算结果的元组，并返回表示溢出或其他异常行为的布尔值。

```pony
// partial arithmetic
let result =
  try
    USize.max_value() +? env.args.size()
  else
    env.out.print("overflow detected")
  end

// checked arithmetic
let result =
  match USize.max_value().addc(env.args.size())
  | (let result: USize, false) =>
    // use result
    ...
  | (_, true) =>
    env.out.print("overflow detected")
  end
```

<!-- Partial as well as checked arithmetic comes with the burden of handling exceptions on every case and incurs some performance overhead, be warned. -->
请注意，部分以及经过检查的算法会带来在不同的异常处理逻辑负担，并会产生一些性能开销。

Partial Operator | Method        | Description
-----------------|---------------|------------
`+?`             | add_partial() | errors on overflow/underflow
`-?`             | sub_partial() | errors on overflow/underflow
`*?`             | mul_partial() | errors on overflow/underflow
`/?`             | div_partial() | errors on overflow/underflow and division by zero
`%?`             | rem_partial() | errors on overflow/underflow and division by zero
`%%?`            | mod_partial() | errors on overflow/underflow and division by zero

---

<!-- Checked arithmetic functions all return the result of the operation and a `Boolean` flag indicating overflow/underflow or division by zero in a tuple. -->
算术运算检查函数返回运算结果和一个`布尔`值，指示在元组中上溢/下溢或被零除。

Checked Method | Description
---------------|------------
addc()         | Checked addition, second tuple element is `true` on overflow/underflow.
subc()         | Checked subtraction, second tuple element is `true` on overflow/underflow.
mulc()         | Checked multiplication, second tuple element is `true` on overflow.
divc()         | Checked division, second tuple element is `true` on overflow or division by zero.
remc()         | Checked remainder, second tuple element is `true` on overflow or division by zero.
modc()         | Checked modulo, second tuple element is `true` on overflow or division by zero.
fldc()         | Checked floored division, second typle element is `true` on overflow or division by zero.

---

<!-- ## Floating Point -->
## 浮点数（Floating Point）

<!-- Pony default arithmetic on floating point numbers (`F32`, `F64`) behave as defined in the floating point standard IEEE 754. -->
Pony中默认的浮点数(`F32`, `F64`)算法的行为与浮点标准 IEEE 754. 中定义的相同。

<!-- That means e.g. that division by `+0` returns `Inf` and by `-0` returns `-Inf`. -->
这意味着用`+0`除会返回`Inf` ，用`-0`除会返回`-Inf`。

<!-- ### Unsafe Arithmetic -->
### 非安全的算术运算（Unsafe Arithmetic）

<!-- Unsafe Floating Point operations do not necessarily comply with IEEE 754 for every input or every result. If any argument to an unsafe operation or its result are `+/-Inf` or `NaN`, the result is actually undefined. -->
对于每个输入或每个结果，不安全的浮点操作不一定符合IEEE 754。如果任何不安全操作或其结果的参数为“ +/- Inf”或“ NaN”，则结果实际上是不确定的。

<!-- This allows more aggressive optimizations and for faster execution, but only yields valid results for values different that the exceptional values `+/-Inf` and `NaN`. We suggest to only use unsafe arithmetic on floats if you can exclude those cases. -->
这样可以进行更积极的优化并加快执行速度，但是对于与例外值+/- Inf和NaN不同的值，只会产生有效的结果。如果可以排除这些情况，建议仅对浮点数使用不安全的算法。

---

Operator | Method
---------|---------------
`+~`     | add_unsafe()
`-~`     | sub_unsafe()
`*~`     | mul_unsafe()
`/~`     | div_unsafe()
`%~`     | rem_unsafe()
`%%~`    | mod_unsafe()
`-~`     | neg_unsafe()
`<~`     | lt_unsafe()
`>~`     | gt_unsafe()
`<=~`    | le_unsafe()
`>=~`    | ge_unsafe()
`=~`     | eq_unsafe()
`!=~`    | ne_unsafe()

---

<!-- Additionally `sqrt_unsafe()` is undefined for negative values. -->
另外，没有对于负数定义`sqrt_unsafe()`。
