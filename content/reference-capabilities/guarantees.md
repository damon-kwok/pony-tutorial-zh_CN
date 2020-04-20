---
title: "权能约束（Reference Capability Guarantees）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 30
toc: true
---

<!-- Since types are guarantees, it's useful to talk about what guarantees a reference capability makes. -->
因为类型系统的约束性，所以也需要了解下引用权能提供了哪些约束。

<!-- ## What is denied -->
## 哪些语法是被禁止的？（What is denied）

<!-- We're going to talk about reference capability guarantees in terms of what's _denied_. By this, we mean: what can other variables _not_ do when you have a variable with a certain reference capability? -->
接下来我们将讨论在引用权能的守护下，编译器会阻止哪些非法操作。也就是说：当你定义了一个具有引用权能的变量时，其他变量 _不能_ 对它做什么?

<!-- We need to distinguish between the actor that contains the variable in question and _other_ actors. -->
首先我们需要区分：`自身actor`（变量所在的actor）、`其他actor`（外部actor）和`任何actor`（表示所有actor，包括自身actor和其他actor）。

<!-- This is important because data reads and writes from other actors may occur concurrently. If two actors can both read the same data and one of them changes it then it will change under the feet of the other actor. This leads to data-races and the need for locks. By ensuring this situation can never occur, Pony eliminates the need for locks. -->
需要知道一件重要事：多个actor会存在并发。如果两个actor都能读同一份数据，其中一个更改了数据，另一个actor也必须知道数据发生了变化。这将导致数据竞争，并产生对锁的需求。只有确保这种情况永远不会发生，Pony才能真正的消除锁。

<!-- All code within any one actor always executes sequentially. This means that data accesses from multiple variables within a single actor do not suffer from data-races. -->
还要牢记一点：单个actor中的代码总是顺序执行的。所以，在actor的内部对变量的访问不会产生数据竞争问题。

<!-- ## Mutable reference capabilities -->
## 可变引用权能（Mutable reference capabilities）

<!-- The __mutable__ reference capabilities are `iso`, `trn` and `ref`. These reference capabilities are __mutable__ because they can be used to both read from and write to an object. -->
`可变`的`引用权能`类型有`iso`、`trn`和`ref`。用这些引用权能标记的数据是可以修改的，它们可以用来读写对象。

<!-- * If an actor has an `iso` variable, no other variable can be used by _any_ actor to read from or write to that object. This means an `iso` variable is the only variable anywhere in the program that can read from or write to that object. It is _read and write unique_. -->
<!-- * If an actor has a `trn` variable, no other variable can be used by _any_ actor to write to that object, and no other variable can be used by _other_ actors to read from or write to that object. This means a `trn` variable is the only variable anywhere in the program that can write to that object, but other variables held by the same actor may be able to read from it. It is _write unique_. -->
<!-- * If an actor has a `ref` variable, no other variable can be used by _other_ actors to read from or write to that object. This means that other variables can be used to read from and write to the object, but only from within the same actor. -->
* 如果一个actor有一个`iso`变量，那么 `任何actor`都`不能读写`这个变量的数据。这表示`iso`变量是程序中惟一可以读写自身数据的变量，它具有`读写唯一性（read and write unique）`。<!-- 它是读写唯一的。 -->
* 如果一个actor有一个`trn`变量，`自身actor`可读不可写，`其他actor`不能读写。这表示`trn`变量是程序中惟一可以修改自身数据的变量，但是自身actor持有的其他变量可能可以从该对象读取数据。它具备`修改唯一性（write unique）`。
* 如果一个actor有一个`ref`变量，只有`自身actor`可以读写，`其他actor`不能读写。其他actor想要读写该变量，只能借助消息机制（调用此actor的行为）来进行。

<!-- __Why can they be used to write?__ Because they all stop _other_ actors from reading from or writing to the object. Since we know no other actor will be reading, it's safe for us to write to the object, without having to worry about data-races. And since we know no other actor will be writing, it's safe for us to read from the object, too. -->
__为什么自身actor内部可以安全的修改？__ 因为`其他actor`无法对其读写。<!-- 没有其他actor会读取数据，所以可以安全地向对象写入数据，而不必担心数据竞争。没有其他actor会写，所以可以安全的读取。 -->

<!-- ## Immutable reference capabilities -->
## 不可变的引用权能(Immutable reference capabilities)

<!-- The __immutable__ reference capabilities are `val` and `box`. These reference capabilities are __immutable__ because they can be used to read from an object, but not to write to it. -->
`不可变`的引用权能是`val`和`box`。这些引用权能是不可变的，它们可以用于从对象中读取数据，而不是写入数据。

<!-- * If an actor has a `val` variable, no other variable can be used by _any_ actor to write to that object. This means that the object can't _ever_ change. It is _globally immutable_. -->
<!-- * If an actor has a `box` variable, no other variable can be used by _other_ actors to write to that object. This means that other actors may be able to read the object and other variables in the same actor may be able to write to it (although not both). In either case, it is safe for us to read. The object is _locally immutable_. -->
* 如果一个actor有一个`val`变量，那么`任何actor`不能修改它。这表示它`无法被修改`。它的数据是`全局不可变（globally immutable）`的。
* 如果一个actor有一个`box`变量，那么`其他actor`不能修改她，但可以读取，而自身actor中的其他变量才可以修改(尽管不是同时写入)。无论哪种情况，我们读取变量都是安全的。这类对象是`局部不可变（localy immutable）`的。

<!-- __Why can they be used to read but not write?__ Because these reference capabilities only stop _other_ actors from writing to the object. That means there is no guarantee that _other_ actors aren't reading from the object, which means it's not safe for us to write to it. It's safe for more than one actor to read from an object at the same time though, so we're allowed to do that. -->
__为什么可以读取不能修改?__ 因为这些引用权能只会阻止`其他actor`的修改操作，但是不会阻止`其他actor`的读取操作。因为多个actor同时读取一个对象是安全的，所以这样做没问题。

<!-- ## Opaque reference capabilities -->
## 不透明的引用权能（Opaque reference capabilities）

<!-- There's only one __opaque__ reference capability, which is `tag`. A `tag` variable makes no guarantees about other variables at all. As a result, it can't be used to either read from or write to the object; hence the name __opaque__. -->
`不透明（opaque）`的引用权能只有一种，那就是`tag`。`tag`变量对其他变量没有任何约束。它既不能用于从对象中读取数据，也不能用于从对象中写入数据。因此被称为 __不透明（opaque）__ 。

<!-- It's still useful though: you can do identity comparison with it, you can call behaviours on it, and you can call functions on it that only need a `tag` receiver. -->
即便如此，它仍然是有用的：你可以用它做身份比较，也可以在它上面调用行为和方法，只需要一个`tag`接收器。

<!-- __Why can't `tag` be used to read or write?__ Because `tag` doesn't stop _other_ actors from writing to the object. That means if we tried to read, we would have no guarantee that there wasn't some other actor writing to the object, so we might get a race condition. -->
__为什么编译器不允许对`tag`进行读写？__ 这是Pony的一种取舍，因为在在`tag`调用行为时，我们无法停止这一调佣操作。你可以想象一下这样的情况：如果我们一边用它来调用一些操行为，但是同时我们又其他actor对其修改，那就可能会触发数据竞争的情况。
