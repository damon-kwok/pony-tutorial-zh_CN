---
title: "运算符（Operators）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 30
toc: true
---

<!-- ## Infix Operators -->
## 中缀运算符（Infix Operators）

<!-- Infix operators take two operands and are written between those operands. Arithmetic and comparison operators are the most common: -->
中缀运算符需要两个操作数，位于操作数之间。算术和比较运算符是最常见的中缀运算符：

```pony
1 + 2
a < b
```

<!-- Pony has pretty much the same set of infix operators as other languages. -->
Pony的中缀运算符集和其他语言基本一致。

<!-- ## Operator aliasing -->
## 运算符别名（Operator aliasing）

<!-- Most infix operators in Pony are actually aliases for functions. The left operand is the receiver the function is called on and the right operand is passed as an argument. For example, the following expressions are equivalent: -->
Pony中的大多数中缀运算符都是函数的别名。左边的操作数是调用函数的接收器，右边的操作数作为参数传递。例如，下面的两个表达式是等效的：

```pony
x + y
x.add(y)
```

<!-- This means that `+` is not a special symbol that can only be applied to magic types. Any type can provide its own `add` function and the programmer can then use `+` with that type if they want to. -->
`+`不是只能用于特性类型的特殊符号。任何类型都可以提供自己的`add`函数，然后可以根据需要对该类型使用`+`。

<!-- When defining your own `add` function there is no restriction on the types of the parameter or the return type. The right side of the `+` will have to match the parameter type and the whole `+` expression will have the type that `add` returns. -->
在定义自己的`add`函数时，对参数类型或返回类型没有限制。 `+`的右侧必须与参数类型匹配，并且`+`表达式将具有`add`函数返回的类型。

<!-- Here's a full example for defining a type which allows the use of `+`. This is all you need: -->
下面的代码是自定义`+`运算符的完整示例：

```pony
// Define a suitable type
class Pair
  var _x: U32 = 0
  var _y: U32 = 0

  new create(x: U32, y: U32) =>
    _x = x
    _y = y

  // Define a + function
  fun add(other: Pair): Pair =>
    Pair(_x + other._x, _y + other._y)

// Now let's use it
class Foo
  fun foo() =>
    var x = Pair(1, 2)
    var y = Pair(3, 4)
    var z = x + y
```

<!-- It is possible to overload infix operators to some degree using union types or f-bounded polymorphism, but this is beyond the scope of this tutorial. See the Pony standard library for further information. -->
使用类型联合体或f界多态性可以在某种程度上重载中缀运算符，但这不在本教程的讨论范围之内。有关更多信息，请参考[Pony标准库](https://stdlib.ponylang.org)。

<!-- You do not have to worry about any of this if you don't want to. You can simply use the existing infix operators for numbers just like any other language and not provide them for your own types. -->
如果您不想，则不必担心任何这些。您可以像使用任何其他语言一样简单地将现有的中缀运算符用于数字，而不必为您自己的类型提供它们。

<!-- The full list of infix operators that are aliases for functions is: -->
中缀运算符函数别名的完整列表：

---

<!-- Operator   | Method         | Description                     | Note -->
运算符       | 函数         | 描述                     | 注意
-----------|----------------|---------------------------------|---------------
`+`        | add()          | Addition                        |
`-`        | sub()          | Subtraction                     |
`*`        | mul()          | Multiplication                  |
`/`        | div()          | Division                        |
`%`        | rem()          | Remainder                       |
`%%`       | mod()          | Modulo                          | Starting with version `0.26.1`
`<<`       | shl()          | Left bit shift                  |
`>>`       | shr()          | Right bit shift                 |
`and`      | op_and()       | And, both bitwise and logical   |
`or`       | op_or()        | Or, both bitwise and logical    |
`xor`      | op_xor()       | Xor, both bitwise and logical   |
`==`       | eq()           | Equality                        |
`!=`       | ne()           | Non-equality                    |
`<`        | lt()           | Less than                       |
`<=`       | le()           | Less than or equal              |
`>=`       | ge()           | Greater than or equal           |
`>`        | gt()           | Greater than                    |
`>~`       | gt_unsafe()    | Unsafe greater than             |
`+~`       | add_unsafe()   | Unsafe Addition                 |
`-~`       | sub_unsafe()   | Unsafe Subtraction              |
`*~`       | mul_unsafe()   | Unsafe Multiplication           |
`/~`       | div_unsafe()   | Unsafe Division                 |
`%~`       | rem_unsafe()   | Unsafe Remainder                |
`%%~`      | mod_unsafe()   | Unsafe Modulo                   | Starting with version `0.26.1`
`<<~`      | shl_unsafe()   | Unsafe left bit shift           |
`>>~`      | shr_unsafe()   | Unsafe right bit shift          |
`==~`      | eq_unsafe()    | Unsafe equality                 |
`!=~`      | ne_unsafe()    | Unsafe non-equality             |
`<~`       | lt_unsafe()    | Unsafe less than                |
`<=~`      | le_unsafe()    | Unsafe less than or equal       |
`>=~`      | ge_unsafe()    | Unsafe greater than or equal    |
`+?`       | add_partial()? | Partial Addition                |
`-?`       | sub_partial()? | Partial Subtraction             |
`*?`       | mul_partial()? | Partial Multiplication          |
`/?`       | div_partial()? | Partial Division                |
`%?`       | rem_partial()? | Partial Remainder               |
`%%?`      | mod_partial()? | Partial Modulo                  | Starting with version `0.26.1`

---

<!-- ## Short circuiting -->
## 短路求值（Short circuiting）

<!-- The `and` and `or` operators use __short circuiting__ when used with Bool variables. This means that the first operand is always evaluated, but the second is only evaluated if it can affect the result. -->
与Bool变量一起使用时，and和or运算符会采用使用 __短路求值（short circuiting）__ 。第一个操作数始终会被求值，第二个操作数仅在对有影响结果时，才会被求值。

<!-- For `and`, if the first operand is __false__ then the second operand is not evaluated since it cannot affect the result. -->
对于`and`，如果第一个操作数是 __false__ ，则不会对第二个操作数求值，因为它不会影响结果。

<!-- For `or`, if the first operand is __true__ then the second operand is not evaluated since it cannot affect the result. -->
对于`or`，如果第一个操作数是 __true__ ，则不会对第二个操作数求值，因为它不会影响结果。

<!-- This is a special feature built into the compiler, it cannot be used with operator aliasing for any other type. -->
这是编译器内置的特殊功能，不能与其他类型的运算符别名一起使用。

<!-- ## Unary operators -->
## 一元运算符（Unary operators）

<!-- The unary operators are handled in the same manner, but with only one operand. For example, the following expressions are equivalent: -->
一元运算符的与中缀运算符的别名机制类似，但只有一个操作数。例如，以下表达式是等效的：

```pony
-x
x.neg()
```

<!-- The full list of unary operators that are aliases for functions is: -->
一元运算符函数别名的完整列表：

---

<!-- Operator | Method       | Description -->
运算符   | 函数          | 描述
---------|--------------|------------
-        | neg()        | Arithmetic negation
not      | op_not()     | Not, both bitwise and logical
-~       | neg_unsafe() | Unsafe arithmetic negation

---

<!-- ## Precedence -->
## 运算符优先级（Precedence）

<!-- In Pony, unary operators always bind stronger than any infix operators: `not a == b` will be interpreted as `(not a) == b` instead of `not (a == b)`. -->
在Pony中，一元运算符的优先级总是比中缀运算符高：`not a == b`将被解释为`（not a）== b`而不是`not（a == b）`。

<!-- When using infix operators in complex expressions a key question is the __precedence__, i.e. which operator is evaluated first. Given this expression: -->
在复杂表达式中使用中缀运算符时， __优先级__ 问题很关键。试着评估下面这句表达式的运算符优先级：

```pony
1 + 2 * 3  // Compilation failed.
```

<!-- We will get a value of 9 if we evaluate the addition first and 7 if we evaluate the multiplication first. In mathematics, there are rules about the order in which to evaluate operators and most programming languages follow this approach. -->
如果我们计算加法，得到的结果为9，如果计算乘法，得到的结果为7。在数学中有一套评估运算符优先级的规则，大多数编程语言都遵循这种方法。

<!-- The problem with this is that the programmer has to remember the order and people aren't very good at things like that. Most people will remember to do multiplication before addition, but what about left bit shifting versus bitwise and? Sometimes people misremember (or guess wrong) and that leads to bugs. Worse, those bugs are often very hard to spot. -->
这样做的问题是程序员必须记住规则，通常人们对这样的事情不是很擅长。大多数人会记得在加法之前要进行乘法运算，但是左移与按位运算又如何呢？有时人们会忘记（或猜错），这会导致错误。更糟糕的是，这些错误通常很难发现。

<!-- Pony takes a different approach and outlaws infix precedence. Any expression where more than one infix operator is used __must__ use parentheses to remove the ambiguity. If you fail to do this the compiler will complain. -->
Pony采取了不同的做法，去避免这个问题。 如果一个表达式中需要使用多个中缀运算符 __必须__ 使用括号来消除歧义。如果您不这样做，编译时会报错。

<!-- This means that the example above is illegal in Pony and should be rewritten as: -->
上面的示例在Pony中是非法的，正确的写法为：

```pony
1 + (2 * 3)  // 7
```

<!-- Repeated use of a single operator, however, is fine: -->
如果使用的是相同的运算符就可以省略括号：

```pony
1 + 2 + 3  // 6
```

<!-- Meanwhile, mixing unary and infix operators do not need additional parentheses as unary operators always bind more closely, so if our example above used a negative three: -->
同时，一元运算符和中缀运算符的混合不需要额外的括号，因为一元运算符总是和操作数紧贴在一起，因此如果上面的示例使用的是负3是没问题的。但是如果用到不同的中缀运算符：

```pony
1 + 2 * -3  // Compilation failed.
```

<!-- We would still need parentheses to remove the ambiguity for our infix operators like we did above, but not for the unary arithmetic negative (`-`): -->
仍然需要括号来消除歧义，就像上面所做的一样，但是不需要负数一元运算（`-`）加括号：

```pony
1 + (2 * -3)  // -5
```

<!-- We can see that it makes more sense for the unary operator to be applied before either infix as it only acts on a single number in the expression so it is never ambiguous. -->
我们可以看到，将一元运算符应用在中缀运算之前更有意义，因为它仅作用于表达式中的单个数字，因此它永远不会模棱两可。

<!-- Unary operators can also be applied to parentheses and act on the result of all operations in those parentheses prior to applying any infix operators outside the parentheses: -->
一元运算符也可以应用于括号，在括号之外，所有中缀运算符之前，对括号中的运算结果进行操作：

```pony
1 + -(2 * -3)  // 7
```
