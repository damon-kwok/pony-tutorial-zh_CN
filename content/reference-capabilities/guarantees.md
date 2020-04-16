---
title: "引用权能保证（Reference Capability Guarantees）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 30
toc: true
---

<!-- Since types are guarantees, it's useful to talk about what guarantees a reference capability makes. -->
因为类型是保证，所以讨论引用权能的保证是很有用的。

<!-- ## What is denied -->
## `拒绝访问`（What is denied）

<!-- We're going to talk about reference capability guarantees in terms of what's _denied_. By this, we mean: what can other variables _not_ do when you have a variable with a certain reference capability? -->
我们将讨论引用权能保证在什么情况下访问被拒绝。这里我们讨论的问题是：当你有一个具有一定引用权能的变量时，其他变量 _不能_ 做什么?

<!-- We need to distinguish between the actor that contains the variable in question and _other_ actors. -->
我们需要区分包含有变量的actor和 _其他_ actor。

<!-- This is important because data reads and writes from other actors may occur concurrently. If two actors can both read the same data and one of them changes it then it will change under the feet of the other actor. This leads to data-races and the need for locks. By ensuring this situation can never occur, Pony eliminates the need for locks. -->
这一点很重要，因为来自其他actor的数据读写可能同时发生。如果两个actor都能读取相同的数据，并且其中一个更改了数据，那么另一个actor的数据也会发生变化。这将导致数据竞争和对锁的需求。只有确保这种情况永远不会发生，Pony才能消除对锁的需求。

<!-- All code within any one actor always executes sequentially. This means that data accesses from multiple variables within a single actor do not suffer from data-races. -->
任何一个actor中的所有代码总是顺序执行。这意味着来自单个actor中的多个变量的数据访问不会受到数据竞争的影响。

<!-- ## Mutable reference capabilities -->
## 可变引用权能（Mutable reference capabilities）

<!-- The __mutable__ reference capabilities are `iso`, `trn` and `ref`. These reference capabilities are __mutable__ because they can be used to both read from and write to an object. -->
可变的引用权能有`iso`、`trn`和`ref`。用这些引用权能标记的数据是可修改的，它们可以用来读写对象。

<!-- * If an actor has an `iso` variable, no other variable can be used by _any_ actor to read from or write to that object. This means an `iso` variable is the only variable anywhere in the program that can read from or write to that object. It is _read and write unique_. -->
<!-- * If an actor has a `trn` variable, no other variable can be used by _any_ actor to write to that object, and no other variable can be used by _other_ actors to read from or write to that object. This means a `trn` variable is the only variable anywhere in the program that can write to that object, but other variables held by the same actor may be able to read from it. It is _write unique_. -->
<!-- * If an actor has a `ref` variable, no other variable can be used by _other_ actors to read from or write to that object. This means that other variables can be used to read from and write to the object, but only from within the same actor. -->
* 如果一个actor有一个`iso`变量，那么 __任何__ actor都不能读写这个对象。这表示`iso`变量是程序中惟一可以读写该对象的变量。<!-- 它是读写唯一的。 -->
* 如果一个actor有一个`trn`变量，那么 __任何__ actor都不能写入该对象，_其他_ actor也不能读写该对象。这表示`trn`变量是程序中惟一可以写入该对象的变量，但是同一actor持有的其他变量可能可以从该对象读取数据。它是 _write unique_ 。
* 如果一个actor有一个`ref`变量，那么 __其他__ actor都不能使用其他变量来读写这个对象。这意味着可以使用其他变量对对象进行读写操作，但只能从相同的actor中进行。

<!-- __Why can they be used to write?__ Because they all stop _other_ actors from reading from or writing to the object. Since we know no other actor will be reading, it's safe for us to write to the object, without having to worry about data-races. And since we know no other actor will be writing, it's safe for us to read from the object, too. -->
__为什可以安全的修改？__ 因为 __其他__ 的actor无法对其读写。<!-- 没有其他actor会读取数据，所以可以安全地向对象写入数据，而不必担心数据竞争。没有其他actor会写，所以可以安全的读取。 -->

<!-- ## Immutable reference capabilities -->
## 不可变的引用权能(Immutable reference capabilities)

<!-- The __immutable__ reference capabilities are `val` and `box`. These reference capabilities are __immutable__ because they can be used to read from an object, but not to write to it. -->
不可改变的引用权能是`val`和`box`。这些引用权能是不可变的，它们可以用于从对象中读取数据，而不是写入数据。

<!-- * If an actor has a `val` variable, no other variable can be used by _any_ actor to write to that object. This means that the object can't _ever_ change. It is _globally immutable_. -->
<!-- * If an actor has a `box` variable, no other variable can be used by _other_ actors to write to that object. This means that other actors may be able to read the object and other variables in the same actor may be able to write to it (although not both). In either case, it is safe for us to read. The object is _locally immutable_. -->
* 如果一个actor有一个`val`变量，那么 __任何__ actor不能修改。这表示它 _无法被修改_ 。它是永恒不变。
* 如果一个actor有一个`box`变量，那么 __其他__ actors就不能修改。但时其他actor可以读取，而同一actor中的其他变量才可以修改(尽管不是同时写入)。无论哪种情况，我们读取变量都是安全的。对象是_localy mmutable_。

<!-- __Why can they be used to read but not write?__ Because these reference capabilities only stop _other_ actors from writing to the object. That means there is no guarantee that _other_ actors aren't reading from the object, which means it's not safe for us to write to it. It's safe for more than one actor to read from an object at the same time though, so we're allowed to do that. -->
__为什么可以读取不能修改?__ 因为这些引用权能只会阻止 __其他__ actor修改对象。这表示不能保证 _其他_ actor不会从对象中读取数据，这意味着写入它是不安全的。但是，多个actor同时读取一个对象是安全的，所以我们可以这样做。

<!-- ## Opaque reference capabilities -->
## 不透明的引用权能（Opaque reference capabilities）

<!-- There's only one __opaque__ reference capability, which is `tag`. A `tag` variable makes no guarantees about other variables at all. As a result, it can't be used to either read from or write to the object; hence the name __opaque__. -->
只有一种模糊的引用权能，那就是`tag`。`tag`变量对其他变量没有任何保证。因此，它既不能用于从对象中读取数据，也不能用于从对象中写入数据;因此被称为 __不透明（opaque）__ 。

<!-- It's still useful though: you can do identity comparison with it, you can call behaviours on it, and you can call functions on it that only need a `tag` receiver. -->
它仍然是有用的:你可以用它做身份比较，你可以在它上面调用行为，你可以在它上面调用功能，只需要一个`tag`接收器。

<!-- __Why can't `tag` be used to read or write?__ Because `tag` doesn't stop _other_ actors from writing to the object. That means if we tried to read, we would have no guarantee that there wasn't some other actor writing to the object, so we might get a race condition. -->
为什么`tag`不能用来读或写?因为`tag`并不会阻止其他的角色写入对象。这意味着，如果我们试图读取，我们不能保证没有其他actor写入对象，所以我们可能会得到竞态条件。
