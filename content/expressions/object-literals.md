---
title: "object关键字（Object Literals）"
section: "Expressions"
menu:
  toc:
    parent: "expressions"
    weight: 100
toc: true
---

Sometimes it's really convenient to be able to write a whole object inline. In Pony, this is called an object literal, and it does pretty much exactly what an object literal in JavaScript does: it creates an object that you can use immediately.
有时候，能够内联地写一个完整的对象是非常方便的。在Pony中，这被称为对象文字，它做的事情和JavaScript中的对象文字差不多:它创建一个你可以立即使用的对象。

But Pony is statically typed, so an object literal also creates an anonymous type that the object literal fulfills. This is similar to anonymous classes in Java and C#. In Pony, an anonymous type can provide any number of traits and interfaces.
但是Pony是静态类型的，所以一个对象文字也会创建一个匿名类型，这个对象文字会满足这个匿名类型。这类似于Java和c#中的匿名类。在Pony中，匿名类型可以提供任意数量的特征和接口。

## What's this look like, then?

It basically looks like any other type definition, but with some small differences. Here's a simple one:
它与其他类型定义基本相同，但有一些细微的区别。下面是一个简单的例子:

```pony
object
  fun apply(): String => "hi"
end
```

Ok, that's pretty trivial. Let's extend it so that it explicitly provides an interface so that the compiler will make sure the anonymous type fulfills that interface. You can use the same notation to provide traits as well.
这很简单。让我们对其进行扩展，以便显式地提供一个接口，以便编译器能够确保匿名类型满足该接口。您也可以使用相同的符号来提供特征。

```pony
object is Hashable
  fun apply(): String => "hi"
  fun hash(): USize => this().hash()
end
```

What we can't do is specify constructors in an object literal, because the literal _is_ the constructor. So how do we assign to fields? Well, we just assign to them. For example:
我们不能在对象文字中指定构造函数，因为文字_is_构造函数。那么我们如何分配字段呢?我们只是分配给它们。例如:

```pony
use "collections"

class Foo
  fun foo(str: String): Hashable =>
    object is Hashable
      let s: String = str
      fun apply(): String => s
      fun hash(): USize => s.hash()
    end
```

When we assign to a field in the constructor, we are _capturing_ from the lexical scope the object literal is in. Pretty fun stuff! It lets us have arbitrarily complex __closures__ that can even have multiple entry points (i.e. functions you can call on a closure).
当我们在构造函数中赋值给一个字段时，我们从对象文字所在的词法作用域中获取_capturing_。非常有趣的东西!它让我们有任意复杂的可以有多个入口点(例如，你可以在一个闭包上调用函数)的。

An object literal with fields is returned as a `ref` by default unless an explicit reference capability is declared by specifying the capability after the `object` keyword. For example, an object with sendable captured references can be declared as `iso` if needed:
默认情况下，带有字段的对象文字将作为‘ref’返回，除非通过在‘object’关键字后指定一个显式引用功能来声明该功能。例如，一个带有sendable捕获引用的对象可以在需要时声明为`iso`:

```pony
use "collections"

class Foo
  fun foo(str: String): Hashable iso^ =>
    object iso is Hashable
      let s: String = str
      fun apply(): String => s
      fun hash(): USize => s.hash()
    end
```

We can also implicitly capture values from the lexical scope by using them in the object literal. Sometimes values that aren't local variables, aren't fields, and aren't parameters of a function are called _free variables_. By using them in a function, we are _closing over_ them - that is, capturing them. The code above could be written without the field `s`:
我们还可以通过在对象文字中使用词法作用域来隐式捕获值。有时，那些不是局部变量、不是字段、也不是函数参数的值被称为_free variables_。通过在函数中使用它们，我们将对它们进行覆盖——也就是捕获它们。上面的代码可以写没有字段' s ':

```pony
use "collections"

class Foo
  fun foo(str: String): Hashable iso^ =>
    object iso is Hashable
      fun apply(): String => str
      fun hash(): USize => str.hash()
    end
```

## Lambdas

Arbitrarily complex closures are nice, but sometimes we just want a simple closure. In Pony, you can use the lambdas for that. A lambda is written as a function (implicitly named `apply`) enclosed in curly brackets:
任意复杂的闭包很好，但有时我们只想要一个简单的闭包。在Pony中，你可以使用lambdas。一个lambda被写成一个函数(隐式命名为`应用`)，括在花括号中:

```pony
{(s: String): String => "lambda: " + s }
```

This produces the same code as:
这产生相同的代码:

```pony
object
  fun apply(s: String): String => "lambda: " + s
end
```

The reference capability of the lambda object can be declared by appending it after the closing curly bracket:
lambda对象的引用能力可以通过在右花括号后追加声明:

```pony
{(s: String): String => "lambda: " + s } iso
```

This produces the same code as:
这产生相同的代码:

```pony
object iso
  fun apply(s: String): String => "lambda: " + s
end
```

Lambdas can be used to capture from the lexical scope in the same way as object literals can assign from the lexical scope to a field. This is done by adding a second argument list after the parameters:
Lambdas可用于从词法作用域捕获，其方式与对象文字可从词法作用域分配到字段的方式相同。这是通过在参数之后添加第二个参数列表来实现的:

```pony
class Foo
  new create(env:Env) =>
    foo({(s: String)(env) => env.out.print(s) })

  fun foo(f: {(String)}) =>
    f("Hello World")
```

It's also possible to use a _capture list_ to create new names for things. A capture list is a second parenthesised list after the parameters:
也可以使用_capture list_为事物创建新名称。捕获列表是参数之后的第二个括号列表:

```pony
  new create(env:Env) =>
    foo({(s: String)(myenv = env) => myenv.out.print(s) })
```

The type of a lambda is also declared using curly brackets. Within the brackets, the function parameter types are specified within parentheses followed by an optional colon and return type. The example above uses `{(String)}` to be the type of a lambda function that takes a `String` as an argument and returns nothing.
lambda的类型也使用花括号声明。在方括号中，函数参数类型在圆括号中指定，后面跟着一个可选的冒号和返回类型。上面的示例使用`{(String)}`作为lambda函数的类型，该函数接受`String`作为参数，不返回任何内容。

If the lambda object is not declared with a specific reference capability, the reference capability is inferred from the structure of the lambda. If the lambda does not have any captured references, it will be `val` by default; if it does have captured references, it will be `ref` by default. The following is an example of a `val` lambda object:
如果lambda对象没有使用特定的引用功能声明，则引用功能将从lambda的结构中推断出来。如果lambda没有捕获到任何引用，则默认为`val`;如果它确实捕获了引用，那么默认情况下它将是' ref '。下面是一个`val`lambda对象的例子:

```pony
use "collections"

actor Main
  new create(env:Env) =>
    let l = List[U32]
    l.>push(10).>push(20).>push(30).push(40)
    let r = reduce(l, 0, {(a:U32, b:U32): U32 => a + b })
    env.out.print("Result: " + r.string())

  fun reduce(l: List[U32], acc: U32, f: {(U32, U32): U32} val): U32 =>
    try
      let acc' = f(acc, l.shift()?)
      reduce(l, acc', f)
    else
      acc
    end
```

The `reduce` method in this example requires the lambda type for the `f` parameter to require a reference capability of `val`. The lambda object passed in as an argument does not need to declare an explicit reference capability because `val` is the default for a lambda that does not capture anything.
本例中的‘reduce’方法要求‘f’参数的lambda类型需要‘val’的引用功能。作为参数传入的lambda对象不需要声明显式引用功能，因为`val`是不捕获任何内容的lambda的默认值。

As mentioned previously the lambda desugars to an object literal with an `apply` method. The reference capability for the `apply` method defaults to `box` like any other method. In a lambda that captures this needs to be `ref` if the function needs to modify any of the captured variables or call `ref` methods on them. The reference capability for the method (versus the reference capability for the object which was described above) is defined by putting the capability before the parenthesized argument list.
如前所述，lambda desugars使用`apply`方法来处理对象文字。`应用`方法的引用功能默认为`框`，就像其他方法一样。在捕获这个的lambda中，如果函数需要修改捕获的任何变量或对其调用' ref '方法，则需要' ref '。方法的引用功能(与上面描述的对象的引用功能相比)是通过将该功能放在圆括号中的参数列表之前来定义的。

```pony
use "collections"

actor Main
  new create(env:Env) =>
    let l = List[String]
    l.>push("hello").push("world")
    var count = U32(0)
    for_each(l, {ref(s:String) =>
      env.out.print(s)
      count = count + 1
    })
    // Displays '0' as the count
    env.out.print("Count: " + count.string())

  fun for_each(l: List[String], f: {ref(String)} ref) =>
    try
      f(l.shift()?)
      for_each(l, f)
    end
```

This example declares the type of the apply function that is generated by the lambda expression as being `ref`. The lambda type declaration for the `f` parameter in the `for_each` method also declares it as `ref`. The reference capability of the lambda type must also be `ref` so that the method can be called. The lambda object does not need to declare an explicit reference capability because `ref` is the default for a lambda that has captures.
如前所述，lambda desugars使用`apply`方法来处理对象文字。`应用`方法的引用功能默认为`框`，就像其他方法一样。在捕获这个的lambda中，如果函数需要修改捕获的任何变量或对其调用' ref '方法，则需要' ref '。方法的引用功能(与上面描述的对象的引用功能相比)是通过将该功能放在圆括号中的参数列表之前来定义的。

The above example also notes a subtle reality of captured references. At first glance one might expect `count` to have been incremented by the application of `f`. However, reassigning a reference, `count = count + 1`, inside a lambda or object literal can never cause a reassignment in the outer scope. If `count` were an object with reference capabilities permiting mutation, the captured reference could be modified with for example `count.increment()`. The resulting mutation would be visible to any location holding a reference to the same object as `count`.
上面的例子还注意到了捕获的引用的一个微妙的事实。乍一看，人们可能会认为`计数`会随着`f`的应用而增加。但是，在lambda或对象文字内部重新分配引用`count = count + 1`永远不会在外部范围内导致重新分配。如果‘count’是一个具有允许变异的引用功能的对象，则可以使用例如‘count.increment()’来修改捕获的引用。产生的突变将是可见的任何位置持有一个引用的对象相同的`计数`。

## Actor literals

Normally, an object literal is an instance of an anonymous class. To make it an instance of an anonymous actor, just include one or more behaviours in the object literal definition.
通常，对象文字是匿名类的实例。要使它成为匿名参与者的实例，只需在对象文字定义中包含一个或多个行为。

```pony
object
  be apply() => env.out.print("hi")
end
```

An actor literal is always returned as a `tag`.
actor文本总是作为`标记`返回。

## Primitive literals

When an anonymous type has no fields and no behaviours (like, for example, an object literal declared as a lambda literal), the compiler generates it as an anonymous primitive, unless a non-`val` reference capability is explicitly given. This means no memory allocation is needed to generate an instance of that type.
当匿名类型没有字段和行为(例如，声明为lambda文字的对象文字)时，编译器将其生成为匿名原语，除非显式地提供了非`val`引用功能。这意味着不需要内存分配来生成该类型的实例。

In other words, in Pony, a lambda that doesn't close over anything has no memory allocation overhead. Nice.
换句话说，在Pony中，不关闭任何东西的lambda没有内存分配开销。好了。

A primitive literal is always returned as a `val`.
原始文字总是以`val`的形式返回。