---
title: "权能矩阵（Reference Capability Matrix）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 110
toc: true
---

<!-- At this point, it's quite possible that you read the previous sections in this chapter and are still pretty confused about the relation between reference capabilities. It's okay! We have all struggled when learning this part of Pony, too. Once you start working on Pony code, you'll get a better intuition with them. -->
此时，您很可能已经阅读了本章前面的部分，仍然对引用权能之间的关系感到困惑。这是好的!当我们学习小马的这一部分时，我们也都很挣扎。一旦你开始编写小马代码，你就会对它们有一个更好的直觉。

<!-- In the meantime, if you still feel like all these tidbits in the chapter are still scrambled in your head, there is one resource often presented with Pony that can give you a more visual representation: the __reference capability matrix__. -->
与此同时，如果您仍然觉得本章中的所有花絮仍在您的脑海中乱作一堆，那么有一种通常使用Pony提供的资源可以为您提供一种更直观的表示:即 __引用权能矩阵（reference capability matrix）__。

<!-- It is also the origin of the concept behind each capability in Pony, in the sense of how each capability denies certain properties to its reference -- in other words, which guarantees a capability makes. We will explain what that actually means before presenting the matrix. -->
它也是Pony中每个权能背后的概念的起源，意思是每个权能如何拒绝它所引用的某些属性——换句话说，这保证了权能的产生。在展示矩阵之前，我们会解释这到底是什么意思。

## Local and global aliases

<!-- Before anything else, we want to clarify what we mean by "local" and "global" aliases. -->
首先，我们要澄清"本地"和"全局"别名的含义。

<!-- A local alias is a reference to the same variable that exists in the same actor. Whenever you pass a value around, and it's not the argument of an actor's behavior, this is the kind of alias we are working with. -->
局部别名是对存在于同一参与者中的相同变量的引用。当你传递一个值的时候，它不是一个actor行为的参数，这是我们使用的别名。

<!-- On the other hand, a global alias is a reference to the same variable that can exist in a _different_ actor. That is, it describes the properties of how two or more actors could interact with the same reference. -->
另一方面，全局别名是对可以存在于_different actor中的相同变量的引用。也就是说，它描述了两个或多个参与者如何与同一个引用交互的属性。

<!-- Each reference capability in Pony is actually a pair of local guarantees and global guarantees. For instance, `ref` doesn't deny any read/write capabilities inside the actor, but denies other actors from reading or writing to that reference. -->
Pony中的每个引用权能实际上是一对局部保证和全局保证。例如，`ref`不否认参与者内部的任何读/写权能，但否认其他参与者对该引用的读或写权能。

<!-- You may recall from the [Reference Capability Guarantees](guarantees.md) section that mutable references cannot be safely shared between actors, while immutable references can be read by multiple actors. In general, global properties are always as restrictive or more restrictive than the local properties to that reference - what is denied globally must also be denied locally. For example, it's not possible to write to an immutable reference in either a global or local alias. It's also not possible to read from or write to an opaque reference, `tag`. Therefore, some combinations of local and global aliases are impossible, and have no designated capabilities. -->
您可能还记得在[Reference Capability guarantee](guarantee .md)一节中提到，易变的引用不能在参与者之间安全地共享，而不可变的引用可以由多个参与者读取。一般来说，全局属性总是与引用的局部属性具有相同或更严格的限制—全局拒绝的内容也必须在局部拒绝。例如，不可能以全局或本地别名写入不可变引用。也不可能从不透明的引用`tag`读取或写入。因此，局部和全局别名的某些组合是不可能的，并且没有指定的权能。

## Reference capability matrix

<!-- Without further ado, here's the reference capability matrix: -->
言归正传，下面是引用权能矩阵:

---

&nbsp; | Deny global read/write aliases | Deny global write aliases | Don't deny any global aliases
----- | ----- | ----- | -----
__Deny local read/write aliases__ | __`iso`__ | |
__Deny local write aliases__ | `trn` | __`val`__ |
__Don't deny any local aliases__ | `ref` | `box` | __`tag`__
&nbsp; | _(Mutable)_ | _(Immutable)_ | _(Opaque)_

---

<!-- In the context of the matrix, "denying a capability" means that any other alias to that reference is not allowed to do that action. For example, since `trn` denies other local write aliases (but allows reads), this is the only reference that allows writing to the object; and since it denies both read and write aliases to other actors, it's safe to write inside this actor, thus being mutable. And since `box` does not break any guarantees that `trn` makes (local reads are allowed, but global writes are forbidden), we can create `box` aliases to a `trn` reference. -->
在矩阵的上下文中，"拒绝某个权能"意味着不允许该引用的任何其他别名执行该操作。例如，由于`trn`拒绝其他本地写别名(但允许读)，所以这是惟一允许对对象进行写的引用;由于它同时拒绝向其他参与者写入和读取别名，所以在这个actor中写入别名是安全的，因此是可变的。由于`box`不会破坏`trn`所做的任何保证(允许本地读取，但禁止全局写入)，所以我们可以为`trn`引用创建`box`别名。

<!-- You'll notice that the top-right side is empty. That's because, as previously discussed, we cannot make any local guarantees that are more restrictive than the global guarantees, or we'd end up with invalid capabilities that could be written to in this actor but read somewehre else at the same time. -->
你会发现右上角是空的。这是因为，正如前面所讨论的，我们不能做出比全局保证更具限制性的任何局部保证，否则我们将以无效的权能结束，这些权能可以在这个actor中写入，但同时读取其他内容。

<!-- The matrix also helps visualizing other concepts previously discussed in this chapter: -->
矩阵还有助于可视化其他概念之前在本章讨论:

<!-- * __Sendable capabilities__. If we want to send references to a different actor, we must make sure that the global and local aliases make the same guarantees. It'd be unsafe to send a `trn` to another actor, since we could possibly hold `box` references locally. Only `iso`, `val`, and `tag` have the same global and local restrictions – all of which are in the main diagonal of the matrix. -->
<!-- * __Ephemeral subtyping__. If we have an ephemeral capability (for instance, `iso^` after consuming an isolated variable), we can be more permissive for the new alias, i.e. remove restrictions, such as allowing local aliases with read capabilities, and receive the reference into a `trn^`; or both read and write, which gives us `ref`. The same is true for more global alias, and we can get `val`, `box`, or `tag`. Visually, this would be equivalent to walking downwards and/or to-the-right starting from the capability in the matrix. -->
<!-- * __Recovering capabilities__. This is when we "lift" a capability, from a mutable reference to `iso` or an immutable reference to `val`. The matrix equivalent would be walking upwards starting from the capability – quite literally lifting in this case. -->
<!-- * __Aliasing__. With a bit more of imagination, it's possible to picture aliasing `iso` and `trn` as reflecting them on the secondary diagonal of the matrix onto `tag` and `box`, respectively. The reason for that lies on which restrictions arise from the local guarantees. An `iso` doesn't allow different aliases to read or write, which `tag` enforces; and `trn` doesn't allow different aliases to write but allows them to do local reads, fitting `box`'s restrictions. -->
* __Sendable capabilities__。如果我们希望将引用发送给不同的参与者，我们必须确保全局别名和本地别名做出相同的保证。将`trn`发送给另一个演员是不安全的，因为我们可能在本地保存`box`引用。只有`iso`、`val`和`tag`具有相同的全局和局部限制—所有这些都在矩阵的主对角线上。
* __Ephemeral subtyping__。如果我们有一个临时的权能(例如，`iso^`在使用了一个单独的变量之后)，我们可以对新的别名更加宽容，例如删除限制，例如允许本地别名具有读取权能，并将引用接收到一个`trn^`;或者是读和写，这给了我们`ref`。对于更多的全局别名也是如此，我们可以得到`val`、`box`或`tag`。从视觉上看，这相当于从矩阵的权能开始向下走和/或向右走。
* __Recovering capabilities__。这就是我们`提升`一个权能的时候，从一个可变的引用提升到`iso`，或者从一个不可变的引用提升到`val`。矩阵等价物将会从权能开始向上走-在这种情况下相当字面地提升。
* __Aliasing__。想象一下，你可以把`iso`和`trn`的别名分别映射到矩阵的次对角线上的`tag`和`box`上。其原因在于哪些限制是由地方担保产生的。`iso`不允许不同的别名读取或写入，而`标签`则强制执行;`trn`不允许不同的别名写入，但允许他们做本地读取，符合`box`的限制。

<!-- We want to emphasize that trying to apply the reference capability matrix to some capabilities problems is not guaranteed to work (viewpoint adaptation is one example). The matrix is the original definition of the reference capabilities, presented here as a mnemonic device. Whenever you struggle with reference capabilities, we recommend that you reread the corresponding section of this chapter to understand why something is not allowed by the compiler. -->
我们想要强调的是，试图将引用权能矩阵应用于某些权能问题并不能保证一定有效(视点适应就是一个例子)。矩阵是引用权能的原始定义，在这里作为助记设备给出。每当您遇到引用权能方面的困难时，我们建议您重新阅读本章相应的部分，以理解为什么编译器不允许某些东西。
