---
title: "柯里化（Partial Application）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 110
toc: true
---

<!-- Partial application lets us supply _some_ of the arguments to a constructor, function, or behaviour, and get back something that lets us supply the rest of the arguments later. -->
`柯里化（Partial Application）`允许我们对`构造函数`、`函数`或`行为`设置 _部分_ 参数，然后返回一个新函数，以便后面再填充其余的参数。

<!-- ## A simple case -->
## 一个简单的例子

<!-- A simple case is to create a "callback" function. For example: -->
这里有一个简单的例子，创建一个`回调`函数。例如:

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

<!-- This is a bit of a silly example, but hopefully, the idea is clear. We partially apply the `addmul` function on `foo`, binding the receiver to `foo` and the `add` argument to `3`. We get back an object, `f`, that has an `apply` method that takes a `mul` argument. When it's called, it in turn calls `foo.addmul(3, mul)`. -->
这是一个有点傻的示例，但希望，思路是清楚的。我们在`foo`上柯里化了`addmul`函数，把接收者绑定到`foo`上，把`add`参数绑定为`3`。我们得到一个返回值它是一个`匿名对象`：`f`，它有一个`apply`方法，接受一个`mul`参数。当它被调用时，它执行`foo.addmul (3,mul)`。

<!-- We can also bind all the arguments: -->
我们也可以绑定所有的参数：

```pony
let f = foo~addmul(3, 4)
f()
```

<!-- Or even none of the arguments: -->
也不提绑定任何实参：

```pony
let f = foo~addmul()
f(3, 4)
```

<!-- ## Out of order arguments -->
## 命名传参（之前讲过的无序传参方式）

<!-- Partial application with named arguments allows binding arguments in any order, not just left to right. For example: -->
命名传参的方式允许`柯里化（Partial Application）`以任意顺序绑定参数，而不只是从左到右。例如:

```pony
let f = foo~addmul(where mul = 4)
f(3)
```

<!-- Here, we bound the `mul` argument but left `add` unbound. -->
在这里，我们绑定了`mul`参数，但未绑定`add`。

<!-- ## Partially applying a partial application -->
## 对柯里化函数进行柯里化（Partially applying a partial application）

<!-- Since partial application results in an object with an apply method, we can partially apply the result! -->
由于`柯里化（Partial Application）`会返回一个一个带有`apply`方法的`匿名对象`，所以我们对它的返回值再次进行柯里化！

```pony
let f = foo~addmul()
let f2 = f~apply(where mul = 4)
f2(3)
```

<!-- ## Partial application is an object literal -->
## 柯里化的返回值是一个匿名对象（Partial application is an object literal）

<!-- Under the hood, we're assembling an object literal for partial application. It captures some of the lexical scope as fields and has an `apply` method that takes some, possibly reduced, number of arguments. This is actually done as sugar, by rewriting the abstract syntax tree for partial application to be an object literal, before code generation. -->
底层实现上，`柯里化（Partial Application）`会自动封装为一个`匿名对象`。它捕获从作用域中捕获字段，并有一个`apply`方法，该方法接受一些参数。这一切都是是在编译阶段，将柯里化的抽象语法树翻译为`匿名对象`来实现的。

<!-- That means partial application results in an anonymous class and returns a `ref`. If you need another reference capability, you can wrap partial application in a `recover` expression. -->
这意味着`柯里化（Partial Application）`将生成一个`匿名类`并返回一个`ref`权限的`匿名对象`。如果你需要其他`引用权能`，可以将`柯里化（Partial Application）`封装在一个`recover`表达式中。
