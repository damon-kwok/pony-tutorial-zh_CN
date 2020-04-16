---
title: "别名（Aliasing）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 60
toc: true
---

<!-- __Aliasing__ means having more than one reference to the same object, within the same actor. This can be the case for a variable or a field. -->
__别名（Aliasing）__意味着在同一行为人内对同一对象有多个引用。这可以是变量或字段的情况。

<!-- In most programming languages, aliasing is pretty simple. You just assign some variable to another variable, and there you go, you have an alias. The variable you assign to has the same type (or some supertype) as what's being assigned to it, and everything is fine. -->
在大多数编程语言中，别名非常简单。你只需要把一个变量赋值给另一个变量，就得到了一个别名。赋值给的变量与赋值给它的变量具有相同的类型(或某些超类型)，一切正常。

<!-- In Pony, that works for some reference capabilities, but not all. -->
在Pony中，这适用于一些引用权能，但不是全部。

<!-- ## Aliasing and deny guarantees -->
## 别名和拒绝保证（Aliasing and deny guarantees）

<!-- The reason for this is that the `iso` reference capability denies other `iso` variables that point to the same object. That is, you can only have one `iso` variable pointing to any given object. The same goes for `trn`. -->
这样做的原因是`iso`引用权能拒绝了指向相同对象的其他`iso`变量。也就是说，你只能有一个`iso`变量指向任何给定的对象。trn也是一样。

```pony
fun test(a: Wombat iso) =>
  var b: Wombat iso = a // Not allowed!
```

<!-- Here we have some function that gets passed an isolated Wombat. If we try to alias `a` by assigning it to `b`, we'll be breaking reference capability guarantees so the compiler will stop us. -->
这里我们有一些函数传递给一个孤立的袋熊。如果我们试图通过将`a`赋值给`b`来别名`a`，我们将破坏引用权能保证，因此编译器将阻止我们。

<!-- __What can I alias an `iso` as?__ Since an `iso` says no other variable can be used by _any_ actor to read from or write to that object, we can only create aliases to an `iso` that can neither read nor write. Fortunately, we've got a reference capability that does exactly that: `tag`. So we can do this and the compiler will be happy: -->
我可以将`iso`别名为什么?由于`iso`表示任何其他变量都不能被_any_ actor用来读写该对象，所以我们只能为既不能读也不能写的`iso`创建别名。幸运的是，我们有一个引用权能:' tag '。所以我们可以这样做，编译器会很高兴:

```pony
fun test(a: Wombat iso) =>
  var b: Wombat tag = a // Allowed!
```

<!-- __What about aliasing `trn`?__ Since a `trn` says no other variable can be used by _any_ actor to write to that object, we need something that doesn't allow writing but also doesn't prevent our `trn` variable from writing. Fortunately, we've got a reference capability that does that too: `box`. So we can do this and the compiler will be happy: -->
那混叠`trn`呢?因为‘trn’表示任何其他变量都不能被_any_ actor用来写入该对象，所以我们需要一些不允许写入但也不阻止‘trn’变量写入的东西。幸运的是，我们还有一个引用权能可以做到这一点:`box`。所以我们可以这样做，编译器会很高兴:

```pony
fun test(a: Wombat trn) =>
  var b: Wombat box = a // Allowed!
```

<!-- __What about aliasing other stuff?__ For both `iso` and `trn`, the guarantees require that aliases must give up on some ability (reading and writing for `iso`, writing for `trn`). For the other capabilities (`ref`, `val`, `box` and `tag`), aliases allow for the same operations, so such a reference can just be aliased as itself. -->
那混叠其他东西呢?对于`iso`和`trn`，保证要求别名必须放弃某些权能(`iso`的读和写，`trn`的写)。对于其他功能(' ref '、' val '、' box '和' tag ')，别名允许相同的操作，所以这样的引用可以作为它自己的别名。

<!-- ## What counts as making an alias? -->
## 什么算假名?（What counts as making an alias?）

<!-- There are three things that count as making an alias: -->
有三件事可以算作别名:

<!-- 1. When you __assign__ a value to a variable or a field. -->
<!-- 2. When you __pass__ a value as an argument to a method. -->
<!-- 3. When you __call a method__, an alias of the receiver of the call is created. It is accessible as `this` within the method body. -->
1. 当您将一个值赋值给一个变量或字段时。
2. 当您将一个值作为参数传递给一个方法时。
3. 当您调用一个method__时，调用的接收者的别名被创建。它可以在方法体中以`this`的形式访问。

<!-- In all three cases, you are making a new _name_ for the object. This might be the name of a local variable, the name of a field, or the name of a parameter to a method. -->
在这三种情况下，都要为对象创建一个新的_name_。这可能是局部变量的名称、字段的名称或方法的参数的名称。

<!-- ## Ephemeral types -->
## 短暂的类（Ephemeral types）

<!-- In Pony, every expression has a type. So what's the type of `consume a`? It's not the same type as `a`, because it might not be possible to alias `a`. Instead, it's an __ephemeral__ type. That is, it's a type for a value that currently has no name (it might have a name through some other alias, but not the one we just consumed or destructively read). -->
在小马，每个表情都有一个类型。那么`消费a`是什么类型的呢?它不是与‘a’相同的类型，因为可能无法别名‘a’。相反，它是一种短暂的类型。也就是说，它是当前没有名称的值的类型(它可能通过其他别名具有名称，但不是我们刚刚使用或销毁读取的名称)。

<!-- To show a type is ephemeral, we put a `^` at the end. For example: -->
要显示一个类型是短暂的，我们在末尾加上一个' ^ '。例如:

```pony
fun test(a: Wombat iso): Wombat iso^ =>
  consume a
```

<!-- Here, our function takes an isolated Wombat as a parameter and returns an ephemeral isolated Wombat. -->
在这里，我们的函数接受一个隔离的袋熊作为参数，并返回一个临时隔离的袋熊。

<!-- This is useful for dealing with `iso` and `trn` types, and for generic types, but it's also important for constructors. A constructor always returns an ephemeral type, because it's a new object. -->
这对于处理`iso`和`trn`类型以及泛型类型很有用，但是对于构造函数也很重要。构造函数总是返回临时类型，因为它是一个新对象。

<!-- ## Alias types -->
## 类型别名（Alias types）

<!-- For the same reason Pony has ephemeral types, it also has alias types. An alias type is a way of saying "whatever we can safely alias this thing as". It's only needed when dealing with generic types, which we'll discuss later. -->
就像Pony有临时类型一样，它也有别名类型。别名类型是一种表示`我们可以安全地将这个东西别名为`的方式。只有在处理泛型类型时才需要它，稍后我们将对此进行讨论。

<!-- We indicate an alias type by putting a `!` at the end. Here's an example: -->
我们通过输入'来指示别名类型!``最后。这里有一个例子:

```pony
fun test(a: A) =>
  var b: A! = a
```

<!-- Here, we're using `A` as a __type variable__, which we'll cover later. So `A!` means "an alias of whatever type `A` is". -->
在这里，我们使用`A`作为一个变量的类型，我们将在后面介绍。所以`!的意思是`任何类型的‘A’的别名`。
