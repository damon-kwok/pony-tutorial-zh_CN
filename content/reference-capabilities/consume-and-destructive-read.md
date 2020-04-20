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
权能是Pony的一个重要部分，就是能够说"我已经完成了这件事。" 我们将介绍处理这种情况的两种方法:转让变量和破坏性读取。

<!-- ## Consuming a variable -->
## 转让变量（Consuming a variable）

<!-- Sometimes, you want to _move_ an object from one variable to another. In other words, you don't want to make a _new_ name for the object, exactly, you want to move the object from some existing name to a different one. -->
有时，你希望将对象从一个变量转移到另一个变量。你不希望为对象创建一个`新名字`，只是从某个现有名称移动到另一个不同的名字。

<!-- You can do this by using `consume`. When you `consume` a variable you take the value out of it, effectively leaving the variable empty. No code can read from that variable again until a new value is written to it. Consuming a local variable or a parameter allows you to make an alias with the same type, even if it's an `iso` or `trn`. For example: -->
你可以使用`转让（consume）`表达式来完成。当你`转让（consume）`一个变量时，将从中取出该变量的值，实际上是将该变量清空。在向该变量写入新值之前，任何代码都不能再次读取该变量。使用`局部变量`或`参数`允许你使用相同类型的别名，即使它是`iso`或`trn`。例如:

```pony
fun test(a: Wombat iso) =>
  var b: Wombat iso = consume a // Allowed!
```

<!-- The compiler is happy with that because by consuming `a`, you've said the value can't be used again and the compiler will complain if you try to. -->
此处编译器非常清楚`a`已经被你转让出去了，该变量不能再次使用，如果你尝试使用，编译器就会报错。

```pony
fun test(a: Wombat iso) =>
  var b: Wombat iso = consume a // Allowed!
  var c: Wombat tag = a // Not allowed!
```

<!-- Here's an example of that. When you try to assign `a` to `c`, the compiler will complain. -->
上面示例中，当你试图把`a`赋值给`c`时，编译器会报错。

<!-- __Can I `consume` a field?__ Definitely not! Consuming something means it is empty, that is, it has no value. There's no way to be sure no other alias to the object will access that field. If we tried to access a field that was empty, we would crash. But there's a way to do what you want to do: _destructive read_. -->
__可以`转让（consume）`一个字段吗?__ 当然不行！被`转让（consume）`意味着被置空，也就是说它会没有值。我们无法确保对象的其他别名不会访问该字段。如果试图访问一个空字段，程序会崩溃。但是还有有一种情况你需要知道：`破坏性读取（destructive read）`。

<!-- ## Destructive read -->
## 破坏性读取（Destructive read）

<!-- There's another way to _move_ a value from one name to another. Earlier, we talked about how assignment in Pony returns the _old_ value of the left-hand side, rather than the new value. This is called _destructive read_, and we can use it to do what we want to do, even with fields. -->
还有另一种方法可以将数据从一个变量`移动`到另一个变量。在前面，我们讨论了Pony中的赋值如何返回左侧的`旧值`，而不是`新值`。这叫做`破坏性读取（destructive read）`，我们可以用它来转让任何数据，就算是`字段`也没问题。

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
__为什么可以对一个字段进行破坏性读取，但是不可以销毁它们呢?__ 因为当我们做一个破坏性的读取时，我们给字段赋了一个新的值，所以它总是有一个值。和单纯的`转让（consume）`不同，它没有任何时间让字段是置空状态。这表示它是安全的，编译器不会报错。
