---
title: "Passing and Sharing References"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 70
toc: true
---

<!-- Reference capabilities make it safe to both __pass__ mutable data between actors and to __share__ immutable data amongst actors. Not only that, they make it safe to do it with no copying, no locks, in fact, no runtime overhead at all. -->
引用权能让我们在actor之间可以安全地`传递`可变数据，也可以在actor之间可以安全地`共享`不变数据。不仅如此，它们还提供了安全的方法进行数据处理，不需要复制，不需要加锁，并且不会有运行时开销。

## Passing

<!-- For an object to be mutable, we need to be sure that no _other_ actor can read from or write to that object. The three mutable reference capabilities (`iso`, `trn`, and `ref`) all make that guarantee. -->
对于要可变的对象，我们需要确保没有`其他actor`可以对该对象读写。有三种可变引用权能(`iso`、`trn`和`ref`)都可以对此进行限制。

<!-- But what if we want to pass a mutable object from one actor to another? To do that, we need to be sure that the actor that is _sending_ the mutable object also _gives up_ the ability to both read from and write to that object. -->
但是如果我们想要将一个可变对象从一个actor`传递`给另一个actor呢?为此，我们需要确保正在`发送（sending）`可变对象的actor也`give up`从该对象读写的权能。

<!-- This is exactly what `iso` does. It is _read and write unique_, there can only be one reference at a time that can be used for reading or writing. If you send an `iso` object to another actor, you will be giving up the ability to read from or write to that object. -->
这正是`iso`所做的。它是读写唯一的，一次只能有一个可用于读写的引用。如果您将一个`iso`对象发送给另一个参与者，您将放弃从该对象读写的权能。

<!-- __So I should use `iso` when I want to pass a mutable object between actors?__ Yes! If you don't need to pass it, you can just use `ref` instead. -->
那么当我想要在actor之间传递一个可变对象时，我应该使用`iso`吗?__是的!如果你不需要传递它，你可以使用`ref`代替。

## Sharing

<!-- If you want to __share__ an object amongst actors, then we have to make one of the following guarantees: -->
如果你想在参与者之间共享一个对象，那么我们必须遵守如下约束:

<!-- 1. Either _no_ actor can write to the object, in which case _any_ actor can read from it, or -->
<!-- 2. Only _one_ actor can write to the object, in which case _other_ actors can neither read from or write to the object. -->
1. _no_ actor可以写对象，在这种情况下_any_ actor可以从对象中读取，或者
2. 只有_one_ actor可以对对象进行写操作，在这种情况下_other_ actor既不能对对象进行读操作，也不能对对象进行写操作。

<!-- The first guarantee is exactly what `val` does. It is _globally immutable_, so we know that _no_ actor can ever write to that object. As a result, you can freely send `val` objects to other actors, without needing to give up the ability to read from that object. -->
第一个约束就是val所做的。它具有`全局不可变性`，所以我们知道没有actor可以修改那个对象。因此，您可以自由地将`val`对象发送给其他参与者，而不需要放弃从该对象读取数据的权能。

<!-- __So I should use `val` when I want to share an immutable object amongst actors?__ Yes! If you don't need to share it, you can just use `ref` instead, or `box` if you want it to be immutable. -->
那么当我想要在参与者之间共享一个不可变的对象时，我应该使用`val`吗?__是的!如果你不需要共享它，你可以使用`ref`代替，或`box`如果你想它是不可变的。

<!-- The second guarantee is what `tag` does. Not the part about only one actor writing (that's guaranteed by any mutable reference capability), but the part about not being able to read from or write to an object. That means you can freely pass `tag` objects to other actors, without needing to give up the ability to read from or write to that object. -->
第二个保证是`tag`的权能。不是关于只写一个参与者的部分(这是由任何可变引用权能保证的)，而是关于不能读写对象的部分。这意味着您可以自由地将`tag`对象传递给其他参与者，而不需要放弃从该对象读写的权能。

<!-- __What's the point in sending a tag reference to another actor if it can't then read or write the fields?__ Because `tag` __can__ be used to __identify__ objects and sometimes that's all you need. Also, if the object is an actor you can call behaviours on it even though you only have a `tag`. -->
如果另一个参与者不能读写字段，那么将tag引用发送给另一个参与者又有什么意义呢?因为`tag`可以被用来识别目标，有时这就是你所需要的。另外，如果对象是一个参与者，即使您只有一个`tag`，您也可以对其调用行为。

<!-- __So I should use `tag` when I want to share the identity of a mutable object amongst actors?__ Yes! Or, really, the identity of anything, whether it's mutable, immutable, or even an actor. -->
那么当我想要在actor之间共享可变对象的身份时，我应该使用`tag`吗?__是的!或者，实际上，任何东西的恒等式，不管是可变的，不可变的，甚至是一个行动者。

<!-- ## Reference capabilities that can't be sent -->
## 无法发送的引用权能

<!-- You may have noticed we didn't mention `trn`, `ref`, or `box` as things you can send to other actors. That's because you can't do it. They don't make the guarantees we need in order to be safe. -->
你可能已经注意到我们并没有提到`trn`，`ref`，或者`box`作为你可以发送给其他actor的东西。那是因为你做不到。他们不会为了安全而做出我们需要的约束。

<!-- So when should you use those reference capabilities? -->
那么什么时候应该使用这些引用权能呢?

<!-- * Use `ref` when you need to be able to change an object over time. On the other hand, if your program wouldn't be any slower if you used an immutable type instead, you may want to use a `val` anyway. -->
<!-- * Use `box` when you don't care whether the object is mutable or immutable. In other words, you want to be able to read it, but you don't need to write to it or share it with other actors. -->
<!-- * Use `trn` when you want to be able to change an object for a while, but you also want to be able to make it _globally immutable_ later. -->
*当你需要能够随着时间改变一个对象时，使用`ref`。另一方面，如果您的程序在使用不可变类型时不会变得更慢，那么您可能希望使用`val`。
*当你不关心对象是可变的还是不可变的时候，使用`box`。换句话说，你想要能够阅读它，但你不需要给它写信或与其他演员分享它。
*当你想要改变一个对象一段时间，但你也想让它 _全局不可变（global immutable）_ 以后使用`trn`。
