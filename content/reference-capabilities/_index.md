---
title: "概述（Overview）"
section: "引用权能（Reference Capabilities）"
layout: overview
menu:
  toc:
    identifier: "reference-capabilities-overview"
    parent: "reference-capabilities"
    weight: 1
---

<!-- We've covered the basics of Pony's type system and then expressions, this chapter about reference capabilities will cover another feature of Pony's type system. There aren't currently any mainstream programming languages that feature reference capabilities. What is a reference capability? -->
我们已经介绍了Pony的类型系统的基础知识，然后是表达式，这一章关于引用权能将介绍Pony的类型系统的另一个特性。目前还没有任何主流编程语言具有引用权能。什么是引用权能?

<!-- Well, a reference capability is built on the idea of "a capability". -->
<!-- A capability is the ability to do "something". Usually that "something" involves an external resource that you might want access to; like the filesystem or the network. This usage of capability is called an object capability and is discussed [in the next chapter](/object-capabilities.html). -->
引用权能是建立在`能力`这个概念上的。
能力是做`某事`的能力。通常，`某物`涉及您可能希望访问的外部资源;比如文件系统或网络。这种能力的使用被称为对象能力，[在下一章](/object-capabilities.html)中将对此进行讨论。

<!-- Pony also features a different kind of capability, called a "reference capability". Where object capabilities are about being granted the ability to do things with objects, reference capabilities are about denying you the ability to do things with memory references. For example, "you can have access to this memory BUT ONLY for reading it. You can not write to it". That's a reference capability and it's denying you access to do things. -->
Pony还有一种不同的功能，称为`引用权能`。对象功能是指被授予使用对象执行操作的能力，而引用权能是指拒绝使用内存引用执行操作的能力。例如，"你可以访问这个内存，但只能读取它。你不能向它发送消息"。这是一个引用权能，它拒绝你做事情。

<!-- Reference capabilities are core to what makes Pony special. You might remember in the introduction to this tutorial what we said about Pony: -->
引用权能是Pony与众不同的核心。你可能还记得在本教程的介绍中我们说过的Pony:

<!-- * It's type safe. Really type safe. There's a mathematical [proof](http://www.ponylang.org/media/papers/opsla237-clebsch.pdf) and everything. -->
<!-- * It's memory safe. Ok, this comes with type safe, but it's still interesting. There are no dangling pointers, no buffer overruns, heck, the language doesn't even have the concept of _null_! -->
<!-- * It's exception safe. There are no runtime exceptions. All exceptions have defined semantics, and they are _always_ handled. -->
<!-- * It's data-race-free. Pony doesn't have locks or atomic operations or anything like that. Instead, the type system ensures _at compile time_ that your concurrent program can never have data races. So you can write highly concurrent code and never get it wrong. -->
<!-- * It's deadlock free. This one is easy because Pony has no locks at all! So they definitely don't deadlock, because they don't exist. -->
它是类型安全的。类型安全。有一个数学[证明](http://www.ponylang.org/media/papers/opsla237-clebsch.pdf)和一切。
它是内存安全的。这个带有类型安全，但仍然很有趣。没有悬空指针，没有缓冲区溢出，糟糕的是，这种语言甚至没有_null_的概念!
它是异常安全的。没有运行时异常。所有的异常都有定义的语义，它们总是被处理。
*数据竞争。Pony没有锁或原子操作或任何类似的东西。相反，类型系统确保_at编译时您的并发程序永远不会发生数据竞争。因此，您可以编写高度并发的代码，并且永远不会出错。
*没有死锁。这个很简单，因为Pony根本没有锁!所以它们肯定不会死锁，因为它们不存在。

<!-- Reference capabilities are what make all that awesome possible. -->
引用权能使所有这些都成为可能。

<!-- Code examples in this chapter might be kind of sparse, because we're largely dealing with higher-level concepts. Try to read through the chapter at least once before starting to put the ideas into practice. By the time you finish this chapter, you should start to have a handle on what reference capabilities are and how you can use them. Don't worry if you struggle with them at first. For most people, it's a new way of thinking about your code and takes a while to grasp. If you get stuck trying to get your capabilities right, definitely reach out for help. Once you've used them for a couple weeks, problems with capabilities start to melt away, but before that can be a real struggle. Don't worry, we all went through that struggle. In fact, there's a section of the Pony website dedicated to resources that can help in [learning reference capabilities](https://www.ponylang.io/learn/#reference-capabilities). And by all means, reach out to the Pony community for help. We are here to help you get over the reference capabilities learning curve. It's not easy. We know that. It's a new way of thinking for folks, so do [please reach out](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help). We're waiting to hear from you. -->
本章中的代码示例可能比较少，因为我们主要处理的是更高级的概念。在把这些想法付诸实践之前，试着至少通读一遍这一章。在完成本章之前，您应该已经开始了解什么是引用权能以及如何使用它们。如果你一开始就和他们斗争，不要担心。对于大多数人来说，这是一种思考代码的新方式，需要一段时间才能掌握。如果你在努力提高自己的能力时遇到了困难，一定要寻求帮助。一旦你使用了几周，功能上的问题就开始消失了，但在那之前可能是一个真正的斗争。别担心，我们都经历过这种挣扎。事实上，Pony网站上有一节专门介绍可以帮助学习引用权能的资源(https://www.ponylang.io/learn/#reference-capabilities)。无论如何，向Pony社区寻求帮助。我们在这里帮助您克服引用权能学习曲线。这并不容易。我们都知道。这对人们来说是一种新的思维方式，所以请伸出你的手(https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help)。我们在等你的消息。

<!-- Scared? Don't be. Ready? Good. Let's get started. -->
害怕吗?不要。准备好了吗?好。让我们开始吧。
