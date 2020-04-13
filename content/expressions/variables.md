---
title: "变量（Variables）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 20
toc: true
---

<!-- Like most other programming languages Pony allows you to store data in variables. There are a few different kinds of variables which have different lifetimes and are used for slightly different purposes. -->
和大多数编程语言一样，Pony可以数据存储在变量中。不同类型的变量具有不同的生存期，使用场景也有所不同。

<!-- ## Local variables -->
## 局部变量

<!-- Local variables in Pony work very much as they do in other languages, allowing you to store temporary values while you perform calculations. Local variables live within a chunk of code (they are _local_ to that chunk) and are created every time that code chunk executes and disposed of when it completes. -->
Pony中的局部变量与其他语言一样，可以让您在执行计算时存储临时值。局部变量位于代码块中（它们是该代码块的 _local_ ），并在每次进入代码块时自动创建，并在代码块结束时销毁。

<!-- To define a local variable the `var` keyword is used (`let` can also be used, but we'll get to that later). Right after the `var` comes the variable's name, and then you can (optionally) put a `:` followed by the variable's type. For example: -->
定义局部变量，可以使用关键字`var`（也可以使用`let`，但是我们稍后再讲）。在`var`后面紧跟着的时变量的名称，变量名后面加上一个`：`可以设置（可选）变量的类型。例如：

```pony
var x: String = "Hello"
var y = "Hello"
```

<!-- Here, we're assigning the __string literal__ `"Hello"` to `x`. -->
上面示例中，创建了一个字符串类型的变量`x`，并赋值为`"Hello"`。

<!-- You don't have to give a value to the variable when you define it: you can assign one later if you prefer. If you try to read the value from a variable before you've assigned one, the compiler will complain instead of allowing the dreaded _uninitialised variable_ bug. -->
定义变量时不时必须要给变量赋值：如果愿意，可以稍后再赋值。如果在变量赋值之前尝试从变量中读取值，编译器会报一个变量还未初始化的错误： _uninitialized variable_ 。

<!-- Every variable has a type, but you don't have to specify it in the declaration if you provide an initial value. The compiler will automatically use the type of the initial value of the variable. -->
每个变量都有一个类型，但是如果提供初始值，则不必在声明中指定它。编译器将自动使用变量初始值的类型。

<!-- The following definitions of `x`, `y` and `z` are all effectively identical. -->
下面示例中x，y和z定义方式是等价的。

```pony
var x: String = "Hello"

var y = "Hello"

var z: String
z = "Hello"
```

<!-- __Can I miss out both the type and initial value for a variable?__ No. The compiler will complain that it can't figure out a type for that variable. -->
__我可以同时省略变量的类型和初始值吗？__ 不行。这样的话编译器无法确定变量的类型。

<!-- All local variable names start with a lowercase letter. If you want to you can end them with a _prime_ `'` (or more than one) which is useful when you need a second variable with almost the same meaning as the first. For example, you might have one variable called `time` and another called `time'`. -->
所有局部变量名称均以小写字母开头。如果需要，可以用一个（或多个）引号`'`结尾，当您规避变量重名问题时，会很有用。例如，有一个名为`time`的变量，另一个变量名可以用`time'`。

<!-- The chunk of code that a variable lives in is known as its __scope__. Exactly what its scope is depends on where it is defined. For example, the scope of a variable defined within the `then` expression of an `if` statement is that `then` expression. We haven't looked at `if` statements yet, but they're very similar to every other language. -->
变量所在的代码块称为 __作用域（scope）__ 。作用域范围取决于它的定义位置。例如，在`if`语句的`then`表达式中定义的变量的作用域就是该`then`表达式。虽然我们还没有讲到`if`语句，不过它与其他种语言中的用法是相似的。

```pony
if a > b then
  var x = "a is bigger"
  env.out.print(x)  // OK
end

env.out.print(x)  // Illegal
```

<!-- Variables only exist from when they are defined until the end of the current scope. For our variable `x` this is the `end` at the end of the then expression: after that, it cannot be used. -->
变量定义后仅在作用域结束前有效。对于变量`x`，它的有效范围是`then`表达式到结尾的的`end`之间：在那之后，就无法再使用它了。

<!-- ## Var vs. let -->
## var和let

<!-- Local variables are declared with either a `var` or a `let`. Using `var` means the variable can be assigned and reassigned as many times as you like. Using `let` means the variable can only be assigned once. -->
局部变量用`var`或`let`声明。使用`var`表示这个变量可以反复赋值。使用`let`表示这个是常量只能赋值一次，无法被修改。

```pony
var x: U32 = 3
let y: U32 = 4
x = 5  // OK
y = 6  // Error, y is let
```

<!-- Using `let` instead of `var` also means the variable has to be assigned immediately. -->
使用`let`时必须赋初值。

```pony
let x: U32 = 3 // Ok
let y: U32 // Error, can't declare a let local without assigning to it
y = 6 // Error, can't reassign to a let local
```

<!-- Note that a variable having been declared with `let` only restricts reassignment, and does not influence the mutability of the object it references. This is the job of reference capabilities, explained later in this tutorial. -->
注意，用`let`声明的变量只有自身的重新赋值受到限制，并且不影响其引用的对象的可变性。这是`引用权能`中的内容，本教程后面的章节将对此进行说明。

<!-- You never have to declare variables as `let`, but if you know you're never going to change what a variable references then using `let` is a good way to catch errors. It can also serve as a useful comment, indicating what is referenced is not meant to be changed. -->
变量不时必须要声明为`let`，但是如果您知道永远不会更改变量引用的内容，那么使用`let`是个好主义。这也时一种有用的类型注释，指示所引用的内容不能更改。

<!-- ## Fields -->
## 字段

<!-- In Pony, fields are variables that live within objects. They work like fields in other object-oriented languages. -->
在Pony中，字段是存在于对象中的变量。它们像其他面向对象语言中的字段一样工作。

<!-- Fields have the same lifetime as the object they're in, rather than being scoped. They are set up by the object constructor and disposed of along with the object. -->
字段与它们所在的对象具有相同的生存期，而不是被限制范围。它们由对象构造函数设置，并与对象一起处理。

<!-- If the name of a field starts with `_`, it's __private__. That means only the type the field is in can have code that reads or writes that field. Otherwise, the field is __public__ and can be read or written from anywhere. -->
如果字段名称以“ _”开头，则为__private__。这意味着只有该字段所在的类型才能具有读取或写入该字段的代码。否则，该字段为__public__，可以从任何地方读取或写入。

<!-- Just like local variables, fields can be `var` or `let`. Nevertheless, rules for field assignment differ a bit from variable assignment. No matter the type of the field (either `var` or `let`), either: -->
和局部变量一样，字段也可以用var或let定义。但是，字段分配的规则与变量分配有所不同。无论字段的类型（`var`还是`let`），都需要遵守下面的约定：
<!-- 1. an initial value has to be assigned in their definition or -->
<!-- 2. an initial value has to be assigned in the constructor method. -->
1.必须在其定义中分配一个初始值（或第2条）
2.必须在构造方法中分配一个初始值。

<!-- In the example below, the initial value of the two fields of the class `Wombat` is assigned at the definition level: -->
在下面的示例中，类`Wombat`的两个字段在定义时分配了初始值：
```pony
class Wombat
  let name: String = "Fantastibat"
  var _hunger_level: U32 = 0
```
<!-- Alternatively, these fields could be assigned in the constructor method: -->
也可以在构造函数中设置这些字段：

```pony
class Wombat
  let name: String
  var _hunger_level: U32

  new create(hunger: U32) =>
    name = "Fantastibat"
    _hunger_level = hunger
```
<!-- If the assignment is not done at the definition level or in the constructor, an error is raised by the compiler. This is true for both `var` and `let` fields. -->
如果在定义和在构造函数中都没有初始化字段，就会引发编译错误。对于`var`和`let`字段都是如此。

<!-- Please note that the assignment of a value to a field has to be explicit. The below example raises an error when compiled, even when the field is of `var` type: -->
请注意，必须将字段的值进行明确的初始化。下面示例尝试其他函数中初始化字段，在编译时会引发错误，即便该字段是`var`类型的也不行：
```pony
class Wombat
  let name: String
  var _hunger_level: U64

  new ref create(name': String, level: U64) =>
    name = name'
    set_hunger_level(level)
    // Error: field _hunger_level left undefined in constructor

  fun ref set_hunger_level(hunger_level: U64) =>
    _hunger_level = hunger_level
```
<!-- We will see later in the Methods section that a class can have several constructors. For now, just remember that if the assignment of a field is not done at the definition level, it has to be done in each constructor of the class the field belongs to. -->
稍后在`方法`章节中，我们将看到一个类可以具有多个构造函数。现在，请记住，如果没有在定义时对字段做出初始化，就必须在该字段所属的类的每个构造函数中进行赋值。

<!-- As for variables, using `var` means a field can be assigned and reassigned as many times as you like in the class. Using `let` means the field can only be assigned once. -->
使用`var`声明的字段可以多次赋值。使用`let`声明的字段只能被初始化一次。

```pony
class Wombat
  let name: String
  var _hunger_level: U64

  new ref create(name': String, level: U64) =>
    name = name'
    _hunger_level = level

  fun ref set_hunger_level(hunger_level: U64) =>
    _hunger_level = hunger_level // Ok, _hunger_level is of var type

  fun ref set_name(name' : String) =>
    name = name' // Error, can't assign to a let definition more than once
```

<!-- __Can field declarations appear after methods?__ No. If `var` or `let` keywords appear after a `fun` or `be` declaration, they will be treated as variables within the method body rather than fields within the type declaration. As a result, fields must appear prior to methods in the type declaration -->
__可以在类成员函数中声明字段吗？__ 当然不行。如果`var`或`let`关键字出现在`fun`或`be`声明之后，它们将被视为方法中的局部变量，而不是类型声明中的字段。字段必须出现在类型声明中的所有方法之前。

## Embedded Fields
<!-- ## 嵌套类型字段（Embedded Fields） -->

Unlike local variables, some types of fields can be declared using `embed`. Specifically, only classes or structs can be embedded - interfaces, traits, primitives and numeric types cannot. A field declared using `embed` is similar to one declared using `let`, but at the implementation level, the memory for the embedded class is laid out directly within the outer class. Contrast this with `let` or `var`, where the implementation uses pointers to reference the field class. Embedded fields can be passed to other functions in exactly the same way as `let` or `var` fields. Embedded fields must be initialised from a constructor expression.
<!-- 与局部变量不同，字段的声明是`嵌入`在某些类型中的。准确的说，字段只能嵌入在`类`或`结构体`中，而不能嵌入在`接口`，`特征`，`基元类`和`数字类型`中。 -->
<!-- 使用`embed`声明的字段与使用`let`声明的字段相似，但是在实现上，嵌入式类的内存直接布置在外部类中。将此与`let`或`var`进行对比，后者的实现使用指针来引用字段类。嵌入字段可以通过与“ let”或“ var”字段完全相同的方式传递给其他函数。嵌入式字段必须从构造函数表达式中初始化。 -->

__Why would I use `embed`?__ `embed` avoids a pointer indirection when accessing a field and a separate memory allocation when creating that field. By default, it is advised to use `embed` if possible. However, since an embedded field is allocated alongside its parent object, exterior references to the field forbids garbage collection of the parent, which can result in higher memory usage if a field outlives its parent. Use `let` if this is a concern for you.
<!-- __为什么要在类型中使用`嵌入字段`？__ `嵌入字段`在访问时可以避免外部访问，而在创建该字段时也可以避免单独的内存分配。默认情况下，建议尽可能使用`embed`。但是，由于嵌入式字段是与其类型对象一起分配的，因此对该字段的外部引用将会阻止父对象的垃圾回收，如果某个字段的生命周期超过其类型对象，会导致对象无法释放内存一直被占用。如果您对此感到担忧，请使用`let`。 -->

<!-- ## Globals -->
## 全局变量

<!-- Some programming languages have __global variables__ that can be accessed from anywhere in the code. What a bad idea! Pony doesn't have global variables at all. -->
很多编程语言都允许全局变量，可以从代码中的任何位置对其进行访问。这是个糟糕的做法！Pony中没有全局变量。

<!-- ## Shadowing -->
## 隐藏（Shadowing）

<!-- Some programming languages let you declare a variable with the same name as an existing variable, and then there are rules about which one you get. This is called _shadowing_, and it's a source of bugs. If you accidentally shadow a variable in Pony, the compiler will complain. -->
有些编程语言（类如Rust）允许你声明一个与现有变量同名的变量，替代旧的。这个方式还被美其名曰 _隐藏（shadowing）_ ，这容易引发bug，Pony中不允许这么做。如果你不小心在Pony中隐藏了变量，编译时会报错。

<!-- If you need a variable with _nearly_ the same name, you can use a prime `'`. -->
如果您需要一个 _同名变量_ ，可以用前面提到的方式：使用`'`符号。
