---
;title: "The Pony Type System at a Glance"
title: "类型系统概览"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 10
toc: true
---

<!-- Pony is a _statically typed_ language, like Java, C#, C++, and many others. This means the compiler knows the type of everything in your program. This is different from _dynamically typed_ languages, such as Python, Lua, JavaScript, and Ruby. -->
Pony是一种静态类型的语言，和Java，C＃，C++等语言类似。编译器知道你的程序中所有的数据类型。有别于动态类型的语言（例如Python，Lua，JavaScript和Ruby）。

<!-- ## Static vs Dynamic: What's the difference? -->
## 静态语言与动态语言究竟有何不同？

<!-- In both kinds of language, your data has a type. So what's the difference? -->
在两种语言中，数据都具有数据类型。那有什么区别呢？

<!-- With a _dynamically typed_ language, a variable can point to objects of different types at different times. This is flexible, because if you have a variable `x`, you can assign an integer to it, then assign a string to it, and your compiler or interpreter doesn't complain. -->
在 _动态类型_ 语言中，变量可以在不同的时间指向不同类型的对象。这很灵活，比如一个变量`x`，你可以为其分配一个整数，然后为其赋值一个字符串，编译器或解释器不会报错。

<!-- __But what if I try to do a string operation on `x` after assigning an integer to it?__ Generally speaking, your program will raise an error. You might be able to handle the error in some way, depending on the language, but if you don't, your program will crash. -->
__动态语言中给`x`赋了整数值后在对其执行字符串操作会发生什么？__ 多数情况下，您的程序会报错。你需要以某种方式处理该错误（处理方式取决于你用的语言），如果不处理，程序将会崩溃。

<!-- When you use a _statically typed_ language, a variable has a type. That is, it can only point to objects of a certain type (although in Pony, a type can actually be a collection of types, as we'll see later). If you have an `x` that expects to point to an integer, you can't assign a string to it. Your compiler complains, and it complains __before__ you ever try to run your program. -->
当你使用 _静态类型_ 语言时，变量具有类型。一个变量只能指向某一种类型（在Pony中，类型实际上可以是类型的集合，我们将在后面看到）。如果有一个整数型变量`x`，那就不能再赋值字符串。否则编译器会报错，程序会无法运行。

<!-- ## Types are guarantees -->
## 类型提供的保证和约束（Types are guarantees）

<!-- When the compiler knows what types things are, it can make sure some things in your program work without you having to run it or test it. These things are the _guarantees_ that a language's type system provides. -->
当编译器知道数据类型时，它可以确保程序中的数据符合预期可以运行，而无需在运行时再对数据进行类型检测。这就是静态语言的类型系统提供的 _保证（约束、限制）_ 。

<!-- The more powerful a type system is, the more things it can prove about your program without having to run it. -->
类型系统越强大，在编译时就可以从程序获得越多有用的信息（用来分析）。

<!-- __Do dynamic types make guarantees too?__ Yes, but they do it at runtime. For example, if you call a method that doesn't exist, you will usually get some kind of exception. But you'll only find out when you try to run your program. -->
__动态类型是否也可以提供一些保证？__ 可以是可以，但需要是在运行时间才能处理。例如，如果您调用一个不存在的方法，会触发到某种异常。但是，只有在运行到这行代码时，才会触发。

<!-- ## What guarantees does Pony's type system give me? -->
## Pony的类型系统可以为我们带来什么保证？

<!-- The Pony type system offers a lot of guarantees, even more than other statically typed languages. -->
Pony类型的系统提供了很多保证，甚至比其他静态类型的语言还要多。

<!-- * If your program compiles, it won't crash. -->
<!-- * There will never be an unhandled exception. -->
<!-- * There's no such thing as `null`, so your program will never try to dereference `null`. -->
<!-- * There will never be a data race. -->
<!-- * Your program will never deadlock. -->
<!-- * Your code will always be capabilities-secure. -->
<!-- * All message passing is -->
<!-- [causal](https://courses.cs.vt.edu/~cs5204/fall00/causal.html). (Not ca**su**al!) -->
* 能通过编译，就不会崩溃。
* 永远不会有未处理的异常。
* 没有`null`类型，不需要判断`null`。
* 无数据竞争。
* 不会出现死锁。
* 代码权能安全性。
* [causal](https://courses.cs.vt.edu/~cs5204/fall00/causal.html). (Not ca**su**al!)
<!-- * 所有消息传递为 [causal](https://courses.cs.vt.edu/~cs5204/fall00/causal.html) （不可以！） -->

<!-- Some of those will make sense right now. Some of them may not mean much to you yet (like capabilities-security and causal messaging), but we'll get to those concepts later on. -->
上述的概念中有一些你现在可以理解。还有一些不能理解的概念暂时可以无需关心（例如权能安全性和因果消息传递），但稍后我们将介绍这些概念。

<!-- __If I use Pony's FFI to call code written in another language, does Pony magically make the same guarantees for the code I call?__ Sadly, no. Pony's type system guarantees only apply to code written in Pony. Code written in other languages gets only the guarantees provided by that language. -->
__用Pony的FFI调用其他种语言编写的代码，Pony是否能调用的代码做出类型保证？__ 很不幸，并不能。 Pony的类型系统保证性仅适用于Pony编写的代码。用其他语言编写的代码，需要其他语言来提供保证。
