---
title: "Pony 教程"
layout: _default/single
toc: true
---

<!-- Welcome to the Pony tutorial! If you're reading this, chances are you want to learn Pony. That's great, we're going to make that happen. -->
来到这里，说明你对Pony很感兴趣，不感兴趣也没关系，马上你就会感兴趣。多说无益，现在我们就来盘它！

<!-- This tutorial is aimed at people who have some experience programming already. It doesn't really matter if you know a little Python, or a bit of Ruby, or you are a JavaScript hacker, or Java, or Scala, or C/C++, or Haskell, or OCaml: as long as you've written some code before, you should be fine. -->
本教程假设你已经有了一定的编程经验。

<!-- ## What's Pony, anyway? -->
## Pony语言简介

<!-- Pony is an object-oriented, actor-model, capabilities-secure programming language. It's __object-oriented__ because it has classes and objects, like Python, Java, C++, and many other languages. It's __actor-model__ because it has _actors_ (similar to Erlang, Elixir, or Akka). These behave like objects, but they can also execute code _asynchronously_. Actors make Pony awesome. -->
Pony是一个 __面向对象__ 、内置 __Actor模型__ 、 __强调安全性__ 的编程语言。Pony拥有类似于Python、Java、C++等语言的 __面向对象__ 特性。Pony内置的 __Actor模型__ （类似于Erlang、Elixir和Akka），actor看起来像是对象，但是可以 __异步__ 执行代码。

<!-- When we say Pony is __capabilities-secure__, we mean a few things: -->
Pony强调的 __安全性__ ,表现在这几个方面：

<!-- * It's type safe. Really type safe. There's a mathematical [proof](https://www.ponylang.io/media/papers/opsla237-clebsch.pdf) and everything. -->
<!-- * It's memory safe. Ok, this comes with type safe, but it's still interesting. There are no dangling pointers, no buffer overruns, heck, the language doesn't even have the concept of _null_! -->
<!-- * It's exception safe. There are no runtime exceptions. All exceptions have defined semantics, and they are _always_ handled. -->
<!-- * It's data-race free. Pony doesn't have locks or atomic operations or anything like that. Instead, the type system ensures _at compile time_ that your concurrent program can never have data races. So you can write highly concurrent code and never get it wrong. -->
<!-- * It's deadlock free. This one is easy, because Pony has no locks at all! So they definitely don't deadlock, because they don't exist. -->
* 类型安全性。
* 内存安全性。
* 异常处理安全性。
* 无数据竞争。Pony中没有提供锁、原子操作等机制。编译器会在 __编译期__ 检测出潜在的数据竞争问题。
* 无死锁。再说一次：Pony没有锁！临界区、读写锁、自旋锁、统统不存在！

<!-- We'll talk more about capabilities-security, including both __object capabilities__ and __reference capabilities__ later. -->
后面的章节我们还会讨论更多安全性相关问题，包括 __对象权能模型__ 和 __引用权能__ 。

## The Pony Philosophy: Get Stuff Done

In the spirit of [Richard Gabriel](http://www.jwz.org/doc/worse-is-better.html), the Pony philosophy is neither "the-right-thing" nor "worse-is-better". It is "get-stuff-done".

* Correctness. Incorrectness is simply not allowed. _It's pointless to try to get stuff done if you can't guarantee the result is correct._

* Performance. Runtime speed is more important than everything except correctness. If performance must be sacrificed for correctness, try to come up with a new way to do things. _The faster the program can get stuff done, the better. This is more important than anything except a correct result._

* Simplicity. Simplicity can be sacrificed for performance. It is more important for the interface to be simple than the implementation. _The faster the programmer can get stuff done, the better. It's ok to make things a bit harder on the programmer to improve performance, but it's more important to make things easier on the programmer than it is to make things easier on the language/runtime._

* Consistency. Consistency can be sacrificed for simplicity or performance.
_Don't let excessive consistency get in the way of getting stuff done._

* Completeness. It's nice to cover as many things as possible, but completeness can be sacrificed for anything else. _It's better to get some stuff done now than wait until everything can get done later._

The "get-stuff-done" approach has the same attitude towards correctness and simplicity as "the-right-thing", but the same attitude towards consistency and completeness as "worse-is-better". It also adds performance as a new principle, treating it as the second most important thing (after correctness).

## Guiding Principles

Throughout the design and development of the language the following principles should be adhered to.

* Use the get-stuff-done approach.

* Simple grammar. Language must be trivial to parse for both humans and computers.

* No loadable code. Everything is known to the compiler.

* Fully type safe. There is no "trust me, I know what I'm doing" coercion.

* Fully memory safe. There is no "this random number is really a pointer, honest."

* No crashes. A program that compiles should never crash (although it may hang or do something unintended).

* Sensible error messages. Where possible use simple error messages for specific error cases. It is fine to assume the programmer knows the definitions of words in our lexicon, but avoid compiler or other computer science jargon.

* Inherent build system. No separate applications required to configure or build.

* Aim to reduce common programming bugs through the use of restrictive syntax.

* Provide a single, clean and clear way to do things rather than catering to every programmer's preferred prejudices.

* Make upgrades clean. Do not try to merge new features with the ones they are replacing, if something is broken remove it and replace it in one go. Where possible provide rewrite utilities to upgrade source between language versions.

* Reasonable build time. Keeping down build time is important, but less important than runtime performance and correctness.

* Allowing the programmer to omit some things from the code (default arguments, type inference, etc) is fine, but fully specifying should always be allowed.

* No ambiguity. The programmer should never have to guess what the compiler will do, or vice-versa.

* Document required complexity. Not all language features have to be trivial to understand, but complex features must have full explanations in the docs to be allowed in the language.

* Language features should be minimally intrusive when not used.

* Fully defined semantics. The semantics of all language features must be available in the standard language docs. It is not acceptable to leave behaviour undefined or "implementation dependent".

* Efficient hardware access must be available, but this does not have to pervade the whole language.

* The standard library should be implemented in Pony.

* Interoperability. Must be interoperable with other languages, but this may require a shim layer if non primitive types are used.

* Avoid library pain. Use of 3rd party Pony libraries should be as easy as possible, with no surprises. This includes writing and distributing libraries and using multiple versions of a library in a single program.

## More help

Working your way through the tutorial but in need of more help? Not to worry, we have you covered.

If you are looking for an answer "right now", we suggest you give our [Zulip community](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help) a try. Whatever your question is, it isn't dumb, and we won't get annoyed.

Think you've found a bug? Check your understanding first by writing to the ["beginner help" stream in Zulip](https://ponylang.zulipchat.com/#narrow/stream/189985-beginner-help). Once you know it's a bug, [open an issue](https://github.com/ponylang/ponyc/issues).

# Help us

Found a typo in this tutorial? Perhaps something isn't clear? We welcome pull requests against the tutorial: [https://github.com/ponylang/pony-tutorial](https://github.com/ponylang/pony-tutorial).

Be sure to check out the [contribution guidelines](https://github.com/ponylang/pony-tutorial/blob/master/CONTRIBUTING.md) before opening your PR.
