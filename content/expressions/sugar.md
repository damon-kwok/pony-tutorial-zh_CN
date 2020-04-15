---
title: "语法糖（Sugar）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 90
toc: true
---

<!-- Pony allows you to omit certain small details from your code and will put them back in for you. This is done to help make your code less cluttered and more readable. Using sugar is entirely optional, you can always write out the full version if you prefer. -->
Pony允许你编写代码时做一些简化，让代码更简洁、易读。使用语法糖是可选的，如果您愿意，你可以始终使用完整的书写方式。

## Apply语法糖

<!-- Many Pony classes have a function called `apply` which performs whatever action is most common for that type. Pony allows you to omit the word `apply` and just attempt to do a call directly on the object. So: -->
许多Pony类都有一个名为`apply`的函数，该函数可以执行一些该类型最常见的操作。Pony允许你省略`apply`这个函数名，直接从对象调用。所以:

```pony
var foo = Foo.create()
foo()
```

<!-- becomes: -->
等同于：

```pony
var foo = Foo.create()
foo.apply()
```

<!-- Any required arguments can be added just like normal method calls. -->
需要参数的话可以像调用普通方法那样去添加。

```pony
var foo = Foo.create()
foo(x, 37 where crash = false)
```

<!-- becomes: -->
等同于：

```pony
var foo = Foo.create()
foo.apply(x, 37 where crash = false)
```

<!-- __Do I still need to provide the arguments to apply?__ Yes, only the `apply` will be added for you, the correct number and type of arguments must be supplied. Default and named arguments can be used as normal. -->
__必须要提供参数吗?__ 是的，只有`apply`会为你自动添加，正确的参数数量和类型必须你自己提供。`默认传参`和`命名传参`的机制可以正常使用。

<!-- __How do I call a function foo if apply is added?__ The `apply` sugar is only added when calling an object, not when calling a method. The compiler can tell the difference and only adds the `apply` when appropriate. -->
__如果`apply`会被自动添加，那我怎么调用其他函数？__ 只有直接调用对象时才添加`apply`，其他时候不会。编译器能判断什么时候该添加`apply`。

## Create语法糖

<!-- To create an object you need to specify the type and call a constructor. Pony allows you to miss out the constructor and will insert a call to `create()` for you. So: -->
要实例化对象，你需要指定类型并调用构造函数。Pony允许你忽略构造函数，并将为您插入一个`create()`调用。所以:

```pony
var foo = Foo
```

<!-- becomes: -->
等同于：

```pony
var foo = Foo.create()
```

<!-- Normally types are not valid things to appear in expressions, so omitting the constructor call is not ambiguous. Remember that you can easily spot that a name is a type because it will start with a capital letter. -->
通常，类型在表达式中是不被求值的，因此省略掉构造函数并不会造成歧义。记住，类型是很容易被识别的，因为它始终是以大写字母开头。

<!-- If arguments are needed for `create` these can be provided as if calling the type. Default and named arguments can be used as normal. -->
如果`create`需要参数，可以直接在后面加括号传入。`默认参数`和`命名传参`机制都可以正常使用。

```pony
var foo = Foo(x, 37 where crash = false)
```

<!-- becomes: -->
等同于：

```pony
var foo = Foo.create(x, 37 where crash = false)
```

<!-- __What if I want to use a constructor that isn't named create?__ Then the sugar can't help you and you have to write it out yourself. -->
__如果我想使用一个名字不是`create`的构造函数怎么做?__ 不行，语法糖帮不了你，你必须自己把它写出来。

<!-- __If the create I want to call takes no arguments can I still put in the parentheses?__ No. Calls of the form `Type()` use the combined create-apply sugar (see below). To get `Type.create()` just use `Type`. -->
__如果我要调用的create没有参数，我可以加上括号吗?__ 不能加，调用`Type()`相当于在组合使用`create`和`apply`(参见下面)。要调用`Type.create()`只需使用`Type`。

<!-- ## Combined create-apply -->
## create和apply语法糖的组合使用

<!-- If a type has a create constructor that takes no arguments then the create and apply sugar can be used together. Just call on the type and calls to create and apply will be added. The call to create will take no arguments and the call to apply will take whatever arguments are supplied. -->
如果类型有一个不带参数的`create`构造函数，那么`create`和`apply`语法糖可以一起使用。只需调用类型，创建和应用的调用将被添加。对`create`的调用将不接受任何参数，而对`apply`的调用将接受提供的任何参数。

```pony
var foo = Foo()
var bar = Bar(x, 37 where crash = false)
```

<!-- becomes: -->
等同于：

```pony
var foo = Foo.create().apply()
var bar = Bar.create().apply(x, 37 where crash = false)
```

<!-- __What if the create has default arguments? Do I get the combined create-apply sugar if I want to use the defaults?__ The combined create-apply sugar can only be used when the `create` constructor has no arguments. If there are default arguments then this sugar cannot be used. -->
__如果create有默认参数怎么办？如果我想使用默认参数，是否能使用`create-apply`组合语法糖?__ 只有当`create`构造函数没有参数时，才可以使用组合的`create-apply`语法糖。如果有默认参数，则不能使用。

## Update语法糖

<!-- The `update` sugar allows any class to use an assignment to accept data. Many languages allow this for assigning into collections, for example, a simple C array, `a[3] = x;`. -->
`update`语法糖允许任何类使用赋值的方式来更新数据。在很多语言都可以给集合中的元素赋值，例如，一个简单的C数组`a[3] = x;`。

<!-- In any assignment where the left-hand side is a function call, Pony will translate this to a call to update, with the value from the right-hand side as an extra argument. So: -->
遇到左边是`函数调用`的`赋值表达式`，Pony会将其转换为对`update`的调用，而右边的值则是一个额外的参数。所以:

```pony
foo(37) = x
```

<!-- becomes: -->
等同于：

```pony
foo.update(37 where value = x)
```

<!-- The value from the right-hand side of the assignment is always passed to a parameter named `value`. Any object can allow this syntax simply by providing an appropriate function `update` with an argument `value`. -->
赋值右侧的值总是传递给一个名为`value`的参数。任何对象都可以简单地通过提供一个`update`函数和`value`参数来支持这种语法。

<!-- __Does my update function have to have a single parameter that takes an integer?__ No, you can define update to take whatever parameters you like, as long as there is one called `value`. The following are all fine: -->
__我的`update`函数必须是整数型的参数吗?__ 不，你可以自定义参数类型，只要有一个名为`value`的参数。以下都可以:

```pony
foo1(2, 3) = x
foo2() = x
foo3(37, "Hello", 3.5 where a = 2, b = 3) = x
```

<!-- __Does it matter where `value` appears in my parameter list?__ Whilst it doesn't strictly matter it is good practice to put `value` as the last parameter. That way all of the others can be specified by position. -->
__参数列表中`value`的位置有关系吗？__ 虽然从严格意义上讲没什么关系，但把`value`作为最后一个参数是个好习惯。这样，所有其他参数就可以通过`位置传参`来指定。
