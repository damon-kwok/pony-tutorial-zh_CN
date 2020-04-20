---
title: "Pony 教程"
layout: _default/single
toc: true
---

<!-- Welcome to the Pony tutorial! If you're reading this, chances are you want to learn Pony. That's great, we're going to make that happen. -->
欢迎来到Pony编程语言教程，如果你对Pony感兴趣，那么你来对地方了。Pony是一个神奇的语言，就让我们来一探究竟！

<!-- This tutorial is aimed at people who have some experience programming already. It doesn't really matter if you know a little Python, or a bit of Ruby, or you are a JavaScript hacker, or Java, or Scala, or C/C++, or Haskell, or OCaml: as long as you've written some code before, you should be fine. -->
本教程假设你已经有了一定的编程经验。

<!-- ## What's Pony, anyway? -->
## Pony语言简介

<!-- Pony is an object-oriented, actor-model, capabilities-secure programming language. It's __object-oriented__ because it has classes and objects, like Python, Java, C++, and many other languages. It's __actor-model__ because it has _actors_ (similar to Erlang, Elixir, or Akka). These behave like objects, but they can also execute code _asynchronously_. Actors make Pony awesome. -->
Pony是一个`面向对象`（类似于Python、Java、C++等语言的`面向对象`特性）的编程语言，非常注重`性能`和`安全性`。原生提供强大的`Actor模型`（类似于Erlang、Elixir和Akka）解决并发问题。<!-- Actor让Pony拥有强大的表现力。 -->

<!-- When we say Pony is __capabilities-secure__, we mean a few things: -->
Pony强调的`安全性`,表现在这几个方面：

<!-- * It's type safe. Really type safe. There's a mathematical [proof](https://www.ponylang.io/media/papers/opsla237-clebsch.pdf) and everything. -->
<!-- * It's memory safe. Ok, this comes with type safe, but it's still interesting. There are no dangling pointers, no buffer overruns, heck, the language doesn't even have the concept of _null_! -->
<!-- * It's exception safe. There are no runtime exceptions. All exceptions have defined semantics, and they are _always_ handled. -->
<!-- * It's data-race free. Pony doesn't have locks or atomic operations or anything like that. Instead, the type system ensures _at compile time_ that your concurrent program can never have data races. So you can write highly concurrent code and never get it wrong. -->
<!-- * It's deadlock free. This one is easy, because Pony has no locks at all! So they definitely don't deadlock, because they don't exist. -->
* 类型安全性。
* 内存安全性。
* 异常处理安全性。
* 无数据竞争。Pony没有提供锁、原子操作等机制。编译器会在`编译期`检测出潜在的数据竞争问题。
* 无死锁。Pony中没有锁的概念！临界区、读写锁、自旋锁、统统不存在。

<!-- We'll talk more about capabilities-security, including both __object capabilities__ and __reference capabilities__ later. -->
后面的章节我们还会讨论更多安全性相关问题，包括 __对象权能模型__ 和 __引用权能__ 。

<!-- ## The Pony Philosophy: Get Stuff Done -->
## Pony的哲学：Get Stuff Done

<!-- In the spirit of [Richard Gabriel](http://www.jwz.org/doc/worse-is-better.html), the Pony philosophy is neither "the-right-thing" nor "worse-is-better". It is "get-stuff-done". -->
上世纪末麻省理工和斯坦福推崇[Do the Right Thing](https://mitpress.mit.edu/books/do-right-thing)的语言设计哲学，[理查德·加布里埃尔 1989年写过一篇文章：`Worse is Better`](http://www.jwz.org/doc/worse-is-better.html)。Pony的哲学既不是`the-right-thing`，也不是`worse-is-better`。而是`get-stuff-done`。

<!-- * Correctness. Incorrectness is simply not allowed. _It's pointless to try to get stuff done if you can't guarantee the result is correct._ -->
* 正确性（Correctness）。不正确的情况是不允许的。如果不能保证结果的正确性，那么试图把事情做完是没有意义的。

<!-- * Performance. Runtime speed is more important than everything except correctness. If performance must be sacrificed for correctness, try to come up with a new way to do things. _The faster the program can get stuff done, the better. This is more important than anything except a correct result._ -->
* 高性能（Performance）。除了正确性，运行速度比其他一切都重要。如果必须为了正确性而牺牲性能，那么尝试用一种新的方法来做事情。程序完成得越快越好。这比任何事情都重要，除了一个正确的结果

<!-- * Simplicity. Simplicity can be sacrificed for performance. It is more important for the interface to be simple than the implementation. _The faster the programmer can get stuff done, the better. It's ok to make things a bit harder on the programmer to improve performance, but it's more important to make things easier on the programmer than it is to make things easier on the language/runtime._ -->
* 易用性（Simplicity）。为了易用性可以适当牺牲性能。接口的简单性比实现的简单性更重要。_程序员越快完成任务越好。让程序员在提高性能时遇到一些困难是可以的，但是让程序员在提高性能时遇到的困难比让语言/运行时遇到的困难更重要。_

<!-- * Consistency. Consistency can be sacrificed for simplicity or performance. -->
<!-- _Don't let excessive consistency get in the way of getting stuff done._ -->
*一致性。一致性可能会因为简单性或性能而被牺牲。但是不让让过分的一致性妨碍事情的完成。

<!-- * Completeness. It's nice to cover as many things as possible, but completeness can be sacrificed for anything else. _It's better to get some stuff done now than wait until everything can get done later._ -->
*完整性。覆盖尽可能多的内容很好，但是可以牺牲完整性来完成其他任何事情。与其等到以后什么事都能做完，不如现在就把事情做完。

<!-- The "get-stuff-done" approach has the same attitude towards correctness and simplicity as "the-right-thing", but the same attitude towards consistency and completeness as "worse-is-better". It also adds performance as a new principle, treating it as the second most important thing (after correctness). -->
`get-stuff-done`方法对正确性和简单性的态度与`The-right-thing`相同，但对一致性和完整性的态度与`worse-is-better`相同。它还将性能作为一个新的原则，将其作为第二重要的事情(仅次于正确性)对待。

<!-- ## Guiding Principles -->
## 原则指导(Guiding Principles)

<!-- Throughout the design and development of the language the following principles should be adhered to. -->
在整个语言的设计和开发过程中，应遵循以下原则（也适用于Pony程序开发）。

<!-- * Use the get-stuff-done approach. -->
* 使用get-stuff-done方法。

<!-- * Simple grammar. Language must be trivial to parse for both humans and computers. -->
* 简单的语法。必须方便人类阅读和计算机解析。

<!-- * No loadable code. Everything is known to the compiler. -->
* 不允许动态加载代码。编译器必须在编译时知道一切。

<!-- * Fully type safe. There is no "trust me, I know what I'm doing" coercion. -->
* 彻底的贯彻类型安全性。不要提供选择让使用者去确保怎么做才安全。

<!-- * Fully memory safe. There is no "this random number is really a pointer, honest." -->
* 彻底的内存安全。不存在随机指针。

<!-- * No crashes. A program that compiles should never crash (although it may hang or do something unintended). -->
* 不允许崩溃。必须确保能通过编译的程序就不应该崩溃(尽管它可能挂起或做一些意想不到的事情)。

<!-- * Sensible error messages. Where possible use simple error messages for specific error cases. It is fine to assume the programmer knows the definitions of words in our lexicon, but avoid compiler or other computer science jargon. -->
* 友好的错误提示。在可能的情况下，编译器的错误提示不能让人感到费解。给出任何人都能看的懂的错误提示，不要动不动就引入奇怪的术语。

<!-- * Inherent build system. No separate applications required to configure or build. -->
* 内置构建系统。不需要借助外部程序来配置或构建。

<!-- * Aim to reduce common programming bugs through the use of restrictive syntax. -->
* 通过使用限制性语法来减少常见的编程错误。

<!-- * Provide a single, clean and clear way to do things rather than catering to every programmer's preferred prejudices. -->
* 提供一个简单、干净、清晰的方法来做事情，而不是迎合每个程序员的偏好。

<!-- * Make upgrades clean. Do not try to merge new features with the ones they are replacing, if something is broken remove it and replace it in one go. Where possible provide rewrite utilities to upgrade source between language versions. -->
* 编译器版本升级必须干净。不要合并那些为了新功能而替换现有特性的提交，如果哪些新代码造成了现有特性出问题，必须先删除这些代码，找机会修复后再加入。在可能的话提供一个代码升级助手，帮助开发者自动升级源代码支持新版本。

<!-- * Reasonable build time. Keeping down build time is important, but less important than runtime performance and correctness. -->
* 合理的构建时间。降低构建时间很重要，但不如运行时性能和正确性重要。

<!-- * Allowing the programmer to omit some things from the code (default arguments, type inference, etc) is fine, but fully specifying should always be allowed. -->
* 允许程序员从代码中省略一些内容(默认参数、类型推断等)是可以的，但是应该始终允许完全指定。

<!-- * No ambiguity. The programmer should never have to guess what the compiler will do, or vice-versa. -->
* 没有歧义。程序员永远不应该猜测编译器会做什么，反之亦然。

<!-- * Document required complexity. Not all language features have to be trivial to understand, but complex features must have full explanations in the docs to be allowed in the language. -->
* 文档健壮性。并不是所有的语言特性都必须啰嗦的写到文档里，但是复杂的特性必须先在文档中有完整的解释才能加入到语言中。

<!-- * Language features should be minimally intrusive when not used. -->
* 语言特性在不使用时应尽量减少干扰。

<!-- * Fully defined semantics. The semantics of all language features must be available in the standard language docs. It is not acceptable to leave behaviour undefined or "implementation dependent". -->
* 完全定义的语义。所有语言特性的语义必须在标准语言文档中可用。让行为未定义或“依赖于实现”是不可接受的。

<!-- * Efficient hardware access must be available, but this does not have to pervade the whole language. -->
* 必须提供高效的硬件访问，但这并不一定要遍及整个语言。

<!-- * The standard library should be implemented in Pony. -->
* 提供标准库的实现。

<!-- * Interoperability. Must be interoperable with other languages, but this may require a shim layer if non primitive types are used. -->
* 互操作性。必须可以与其他语言互操作，但这可能需要一个垫片层，如果使用非原始类型。

<!-- * Avoid library pain. Use of 3rd party Pony libraries should be as easy as possible, with no surprises. This includes writing and distributing libraries and using multiple versions of a library in a single program. -->
* 避免类库问题。Pony程序对于类库的使用应该尽可能的简单。编写程序和分发类库时，不要同时引入某个类库多个版本。

<!-- ## More help -->
## 获取帮助（More help）

<!-- Working your way through the tutorial but in need of more help? Not to worry, we have you covered. -->
开发中遇到Pony教程里没有提到的问题？别担心，Pony社区的大神们都非常乐于助人。

<!-- If you are looking for an answer "right now", we suggest you give our [Zulip community](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help) a try. Whatever your question is, it isn't dumb, and we won't get annoyed. -->
如果你遇到了问题需要帮助，来这里[Zulip聊天室](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help)。有问题尽管问，在这里不会有人嘲笑你的问题幼稚。

<!-- Think you've found a bug? Check your understanding first by writing to the ["beginner help" stream in Zulip](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help). Once you know it's a bug, [open an issue](https://github.com/ponylang/ponyc/issues). -->
学会如何提问，让大神们可以快速理解你要问的问题，然后把发到[新手求助频道](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help)。如果你确认自己遇到了Bug，那就[打开一个issue](https://github.com/ponylang/ponyc/issues)，马上就会有人响应你。

<!-- # Help us -->
## 回馈社区（Help us）

<!-- Found a typo in this tutorial? Perhaps something isn't clear? We welcome pull requests against the tutorial: [https://github.com/ponylang/pony-tutorial](https://github.com/ponylang/pony-tutorial). -->
如果发现了本教程中的拼写错误，或者表述不清楚的地方，欢迎提交PR:

<!-- Be sure to check out the [contribution guidelines](https://github.com/ponylang/pony-tutorial/blob/master/CONTRIBUTING.md) before opening your PR. -->
提交PR前建议先阅读下[贡献指南](https://github.com/ponylang/pony-tutorial/blob/master/CONTRIBUTING.md)。

捐助Pony项目：[捐款](https://opencollective.com/ponyc)
