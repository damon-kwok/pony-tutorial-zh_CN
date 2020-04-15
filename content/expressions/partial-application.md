---
title: "Partial Application"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 110
toc: true
---

Partial application lets us supply _some_ of the arguments to a constructor, function, or behaviour, and get back something that lets us supply the rest of the arguments later.
部分应用程序允许我们向构造函数、函数或行为提供_some_参数，然后返回一些东西，以便稍后提供其余的参数。

## A simple case

A simple case is to create a "callback" function. For example:
一个简单的例子是创建一个`回调`函数。例如:

```pony
class Foo
  var _f: F64 = 0

  fun ref addmul(add: F64, mul: F64): F64 =>
    _f = (_f + add) * mul

class Bar
  fun apply() =>
    let foo: Foo = Foo
    let f = foo~addmul(3)
    f(4)
```

This is a bit of a silly example, but hopefully, the idea is clear. We partially apply the `addmul` function on `foo`, binding the receiver to `foo` and the `add` argument to `3`. We get back an object, `f`, that has an `apply` method that takes a `mul` argument. When it's called, it in turn calls `foo.addmul(3, mul)`.
这是一个有点傻的例子，但希望，思路是清楚的。我们在`foo`上部分地应用了`addmul`函数，把接收者绑定到`foo`上，把`add`参数绑定到`3`上。我们取回一个对象，' f '，它有一个' apply '方法，它接受一个' mul '参数。当它被调用时，它反过来调用' foo '。mul addmul (3)`。

We can also bind all the arguments:
我们也可以绑定所有的参数:

```pony
let f = foo~addmul(3, 4)
f()
```

Or even none of the arguments:
甚至没有一个论点:

```pony
let f = foo~addmul()
f(3, 4)
```

## Out of order arguments
无序参数

Partial application with named arguments allows binding arguments in any order, not just left to right. For example:
带命名参数的部分应用程序允许以任何顺序绑定参数，而不只是从左到右。例如:

```pony
let f = foo~addmul(where mul = 4)
f(3)
```

Here, we bound the `mul` argument but left `add` unbound.
在这里，我们绑定了`mul`参数，但未绑定`add`。

## Partially applying a partial application
部分应用部分应用

Since partial application results in an object with an apply method, we can partially apply the result!
由于部分应用程序会产生一个带有apply方法的对象，所以我们可以部分地应用结果!

```pony
let f = foo~addmul()
let f2 = f~apply(where mul = 4)
f2(3)
```

## Partial application is an object literal
部分应用程序是一个对象文字

Under the hood, we're assembling an object literal for partial application. It captures some of the lexical scope as fields and has an `apply` method that takes some, possibly reduced, number of arguments. This is actually done as sugar, by rewriting the abstract syntax tree for partial application to be an object literal, before code generation.
在底层，我们正在为部分应用程序组装一个对象文字。它捕获一些作为字段的词法作用域，并有一个`应用`方法，该方法接受一些(可能减少的)参数。这实际上是通过在代码生成之前，将局部应用程序的抽象语法树重写为对象文字来完成的。

That means partial application results in an anonymous class and returns a `ref`. If you need another reference capability, you can wrap partial application in a `recover` expression.
这意味着部分应用程序将产生一个匿名类并返回一个' ref '。如果您需要另一个引用功能，您可以将部分应用程序封装在一个`恢复`表达式中。