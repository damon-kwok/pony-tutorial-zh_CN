---
title: "权能转让和破坏性读取（Consume and Destructive Read）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 40
toc: true
---

<!-- An important part of Pony's capabilities is being able to say "I'm done with this thing." We'll cover two means of handling this situation: consuming a variable and destructive reads. -->
Pony能力的一个重要部分就是能够说"我已经完成了这件事。"我们将介绍处理这种情况的两种方法:使用变量和破坏性读取。

<!-- ## Consuming a variable -->
## 权能转让（Consuming a variable）

<!-- Sometimes, you want to _move_ an object from one variable to another. In other words, you don't want to make a _new_ name for the object, exactly, you want to move the object from some existing name to a different one. -->
有时，你希望将对象从一个变量移动到另一个变量。换句话说，你不希望为对象创建一个_new_名称，确切地说，你希望将对象从某个现有名称移动到另一个不同的名称。

<!-- You can do this by using `consume`. When you `consume` a variable you take the value out of it, effectively leaving the variable empty. No code can read from that variable again until a new value is written to it. Consuming a local variable or a parameter allows you to make an alias with the same type, even if it's an `iso` or `trn`. For example: -->
你可以通过使用`转让（consume）`来做到这一点。当你`转让（consume）`一个变量时，你将从中取出该变量的值，实际上是将该变量留空。在向该变量写入新值之前，任何代码都不能再次读取该变量。使用局部变量或参数允许你使用相同类型的别名，即使它是`iso`或`trn`。例如:

```pony
fun test(a: Wombat iso) =>
  var b: Wombat iso = consume a // Allowed!
```

<!-- The compiler is happy with that because by consuming `a`, you've said the value can't be used again and the compiler will complain if you try to. -->
编译器对此很高兴，因为通过使用`a`，你已经说过该值不能再次使用，如果你尝试使用，编译器将会抱怨。

```pony
fun test(a: Wombat iso) =>
  var b: Wombat iso = consume a // Allowed!
  var c: Wombat tag = a // Not allowed!
```

<!-- Here's an example of that. When you try to assign `a` to `c`, the compiler will complain. -->
这里有一个例子。当你试图把`a`赋值给`c`时，编译器会报错。

<!-- __Can I `consume` a field?__ Definitely not! Consuming something means it is empty, that is, it has no value. There's no way to be sure no other alias to the object will access that field. If we tried to access a field that was empty, we would crash. But there's a way to do what you want to do: _destructive read_. -->
我可以`转让（consume）`一个字段吗?__当然不行!转让（consume）意味着它是空的，也就是说，它没有价值。无法确保对象的其他别名不会访问该字段。如果我们试图访问一个空字段，我们会崩溃。但是有一种方法可以做你想做的:_destructive read_。

<!-- ## Destructive read -->
## 破坏性读取（Destructive read）

<!-- There's another way to _move_ a value from one name to another. Earlier, we talked about how assignment in Pony returns the _old_ value of the left-hand side, rather than the new value. This is called _destructive read_, and we can use it to do what we want to do, even with fields. -->
还有一种方法可以将值从一个名称移动到另一个名称。在前面，我们讨论了Pony中的赋值如何返回左侧的_old_值，而不是新值。这叫做_destructive read_，我们可以用它来做我们想做的事情，即使是字段。

```pony
class Aardvark
  var buddy: Wombat iso

  new create() =>
    buddy = recover Wombat end

  fun ref test(a: Wombat iso) =>
    var b: Wombat iso = buddy = consume a // Allowed!
```

<!-- Here, we consume `a`, assign it to the field `buddy`, and assign the _old_ value of `buddy` to `b`. -->
在这里，我们使用`a`，将它分配给`buddy`字段，并将`buddy`的_old_值分配给`b`。

<!-- __Why is it ok to destructively read fields when we can't consume them?__ Because when we do a destructive read, we assign to the field so it always has a value. Unlike `consume`, there's no time when the field is empty. That means it's safe and the compiler doesn't complain. -->
为什么当我们不能使用字段时，可以销毁它们呢?因为当我们做一个破坏性的读取时，我们给字段赋值，所以它总是有一个值。不像`转让（consume）`，没有时间当字段是空的。这意味着它是安全的，编译器不会报错。
