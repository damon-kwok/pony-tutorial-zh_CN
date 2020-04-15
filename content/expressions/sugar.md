---
title: "语法糖（Sugar）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 90
toc: true
---

Pony allows you to omit certain small details from your code and will put them back in for you. This is done to help make your code less cluttered and more readable. Using sugar is entirely optional, you can always write out the full version if you prefer.
Pony允许你从你的代码中省略一些小细节，然后把它们放回去。这样做是为了帮助您的代码更简洁、更易读。使用sugar是完全可选的，如果您愿意，总是可以写出完整的版本。

## Apply

Many Pony classes have a function called `apply` which performs whatever action is most common for that type. Pony allows you to omit the word `apply` and just attempt to do a call directly on the object. So:
许多小马类都有一个名为`应用`的函数，它执行该类型最常见的操作。Pony允许你省略`应用`这个词，只是尝试直接调用对象。所以:

```pony
var foo = Foo.create()
foo()
```

becomes:
就变成:

```pony
var foo = Foo.create()
foo.apply()
```

Any required arguments can be added just like normal method calls.
任何必需的参数都可以像普通的方法调用那样添加。

```pony
var foo = Foo.create()
foo(x, 37 where crash = false)
```

becomes:
就变成:

```pony
var foo = Foo.create()
foo.apply(x, 37 where crash = false)
```

__Do I still need to provide the arguments to apply?__ Yes, only the `apply` will be added for you, the correct number and type of arguments must be supplied. Default and named arguments can be used as normal.
我仍然需要提供应用的参数吗?是的，只有`应用`将为您添加，正确的参数数量和类型必须提供。默认参数和命名参数可以正常使用。

__How do I call a function foo if apply is added?__ The `apply` sugar is only added when calling an object, not when calling a method. The compiler can tell the difference and only adds the `apply` when appropriate.
如果应用被添加，我如何调用函数foo ?只有在调用对象时才添加`应用`，而不是在调用方法时。编译器可以分辨出两者的区别，只在适当的时候添加`apply`。

## Create

To create an object you need to specify the type and call a constructor. Pony allows you to miss out the constructor and will insert a call to `create()` for you. So:
要创建对象，您需要指定类型并调用构造函数。Pony允许您忽略构造函数，并将为您插入一个`create()`调用。所以:

```pony
var foo = Foo
```

becomes:
就变成:

```pony
var foo = Foo.create()
```

Normally types are not valid things to appear in expressions, so omitting the constructor call is not ambiguous. Remember that you can easily spot that a name is a type because it will start with a capital letter.
通常，类型在表达式中是无效的，因此省略构造函数调用并不含糊。请记住，您可以很容易地识别出名称是类型，因为它以大写字母开头。

If arguments are needed for `create` these can be provided as if calling the type. Default and named arguments can be used as normal.
如果‘create’需要参数，则可以像调用类型一样提供这些参数。默认参数和命名参数可以正常使用。

```pony
var foo = Foo(x, 37 where crash = false)
```

becomes:
就变成:

```pony
var foo = Foo.create(x, 37 where crash = false)
```

__What if I want to use a constructor that isn't named create?__ Then the sugar can't help you and you have to write it out yourself.
如果我想使用一个没有命名为create的构造函数会怎么样呢?那么，糖帮不了你，你必须自己把它写出来。

__If the create I want to call takes no arguments can I still put in the parentheses?__ No. Calls of the form `Type()` use the combined create-apply sugar (see below). To get `Type.create()` just use `Type`.
如果我想调用的create没有参数，我还可以把它放在括号里吗?__没有。调用形式`Type()`时使用组合创建—应用sugar(参见下面)。要获得' Type.create() '只需使用' Type '。

## Combined create-apply

If a type has a create constructor that takes no arguments then the create and apply sugar can be used together. Just call on the type and calls to create and apply will be added. The call to create will take no arguments and the call to apply will take whatever arguments are supplied.
如果类型有一个不带参数的create构造函数，那么create和apply sugar可以一起使用。只需调用类型，创建和应用的调用将被添加。对create的调用将不接受任何参数，而对apply的调用将接受提供的任何参数。

```pony
var foo = Foo()
var bar = Bar(x, 37 where crash = false)
```

becomes:
就变成:

```pony
var foo = Foo.create().apply()
var bar = Bar.create().apply(x, 37 where crash = false)
```

__What if the create has default arguments? Do I get the combined create-apply sugar if I want to use the defaults?__ The combined create-apply sugar can only be used when the `create` constructor has no arguments. If there are default arguments then this sugar cannot be used.
如果create有默认参数怎么办?如果我想使用默认设置，是否可以得到合并后的创建-应用sugar ?只有当`创建`构造函数没有参数时，才可以使用组合的create-apply sugar。如果有默认参数，则不能使用此糖。

## Update

The `update` sugar allows any class to use an assignment to accept data. Many languages allow this for assigning into collections, for example, a simple C array, `a[3] = x;`.
`update`sugar允许任何类使用赋值来接受数据。许多语言都允许将它赋值到集合中，例如，一个简单的C数组' a[3] = x; '。

In any assignment where the left-hand side is a function call, Pony will translate this to a call to update, with the value from the right-hand side as an extra argument. So:
在任何左边是函数调用的赋值中，Pony都会将其转换为对update的调用，而右边的值则是一个额外的参数。所以:

```pony
foo(37) = x
```

becomes:
就变成:

```pony
foo.update(37 where value = x)
```

The value from the right-hand side of the assignment is always passed to a parameter named `value`. Any object can allow this syntax simply by providing an appropriate function `update` with an argument `value`.
赋值右侧的值总是传递给一个名为' value '的参数。任何对象都可以简单地通过提供一个适当的函数' update '和一个参数' value '来支持这种语法。

__Does my update function have to have a single parameter that takes an integer?__ No, you can define update to take whatever parameters you like, as long as there is one called `value`. The following are all fine:
我的更新函数必须有一个接受整数的参数吗?__不，你可以定义任何你喜欢的参数，只要有一个叫`值`的参数。以下都可以:

```pony
foo1(2, 3) = x
foo2() = x
foo3(37, "Hello", 3.5 where a = 2, b = 3) = x
```

__Does it matter where `value` appears in my parameter list?__ Whilst it doesn't strictly matter it is good practice to put `value` as the last parameter. That way all of the others can be specified by position.
在我的参数列表中`value`出现在哪里有关系吗?虽然从严格意义上讲没什么关系，但把`值`作为最后一个参数是个好习惯。这样，所有其他的都可以通过位置来指定。