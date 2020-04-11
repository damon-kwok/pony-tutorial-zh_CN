---
title: "并发单元（Actors）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 40
toc: true
---

<!-- An __actor__ is similar to a __class__, but with one critical difference: an actor can have __behaviours__. -->
__actor__ 和 __类__ 类似，但是有一个关键区别：actor可以有 __行为__ 。

## Behaviours 行为

<!-- A __behaviour__ is like a __function__, except that functions are _synchronous_ and behaviours are _asynchronous_. In other words, when you call a function, the body of the function is executed immediately, and the result of the call is the result of the body of the function. This is just like method invocation in any other object-oriented language. -->
__行为__ 类似于 __函数__，不过函数是 _同步执行_ 的，但是行为是 _异步执行_ 的。换句话说，当您调用一个函数时，该函数将立即执行，并且立即就可以得到函数的返回值。函数就像其他面向对象语言中的方法调用一样。

<!-- But when you call a behaviour, the body is __not__ executed immediately. Instead, the body of the behaviour will execute at some indeterminate time in the future. -->
但是调用行为，__不会__立即执行。相反，该行为的将在将来某个不确定的时间执行。

<!-- A behaviour looks like a function, but instead of being introduced with the keyword `fun`, it is introduced with the keyword `be`. -->
行为看起来像一个函数，但是它不是由关键字`fun`定义的，而是使用`be`关键字。

<!-- Like a function, a behaviour can have parameters. Unlike a function, it doesn't have a receiver capability (a behaviour can be called on a receiver of any capability) and you can't specify a return type. -->
和函数一样，行为也可以具有参数。与函数不同，它没有接收器功能（可以在任何功能的接收器上调用行为），您不能为行为指定返回类型。

<!-- __So what does a behaviour return?__ Behaviours always return `None`, like a function without explicit result type, because they can't return something they calculate (since they haven't run yet). -->
__那么行为会返回什么呢？__ 行为总是返回`None`，就像没有显式的返回类型的函数一样，它们无法返回所计算的内容（因为调用时它们尚未运行）。

```pony
actor Aardvark
  let name: String
  var _hunger_level: U64 = 0

  new create(name': String) =>
    name = name'

  be eat(amount: U64) =>
    _hunger_level = _hunger_level - amount.min(_hunger_level)
```

Here we have an `Aardvark` that can eat asynchronously. Clever Aardvark.
在这里，我们有一个可以异步进餐的“土豚”。聪明的土豚。

<!-- ## Message Passing  -->
## 消息处理

<!-- If you are familiar with actor-based languages like Erlang, you are familiar with the concept of "message passing". It's how actors communicate with one another. Behaviours are the Pony equivalent. When you call a behavior on an actor, you are sending it a message. -->
如果您熟悉基于语言的语言（例如Erlang），那么您将熟悉“消息传递”的概念。演员之间就是这样交流的。行为等同于小马。当您调用演员的行为时，您正在向其发送消息。

<!-- If you aren't familiar with message passing, don't worry about it. We've got you covered. All will be explained below. -->
如果您不熟悉消息传递，则不必担心。我们已经覆盖了您。所有内容将在下面说明。

<!-- ## Concurrent -->
## 并发

<!-- Since behaviours are asynchronous, it's ok to run the body of a bunch of behaviours at the same time. This is exactly what Pony does. The Pony runtime has its own cooperative scheduler, which by default has a number of threads equal to the number of CPU cores on your machine. Each scheduler thread can be executing an actor behaviour at any given time, so Pony programs are naturally concurrent. -->
由于行为是异步的，因此可以同时运行一系列行为的主体。这正是Pony所做的。 Pony运行时具有自己的协作调度程序，默认情况下，该调度程序的线程数等于您计算机上的CPU内核数。每个调度程序线程可以在任何给定时间执行参与者行为，因此Pony程序自然是并发的。

<!-- ## Sequential -->
## 执行次序

<!-- Actors themselves, however, are sequential. That is, each actor will only execute one behaviour at a time. This means all the code in an actor can be written without caring about concurrency: no need for locks or semaphores or anything like that. -->
Actor本身是有序的。也就是说，每个actor一次只能执行一个行为。这意味着可以在不考虑并发性的情况下编写actor中的所有代码：不需要锁，信号量或类似的东西。

<!-- When you're writing Pony code, it's nice to think of actors not as a unit of parallelism, but as a unit of sequentiality. That is, an actor should do only what _has_ to be done sequentially. Anything else can be broken out into another actor, making it automatically parallel. -->
在编写Pony代码时，最好不要将actor看作是并行性的单元，而是序列性的单元。也就是说，演员应该只执行顺序要执行的操作。其他任何东西都可以分解为另一个actor，使其自动并行。

<!-- In the example below, the `Main` actor calls a behaviour `call_me_later` which, as we know, is _asynchronous_, so we won't wait for it to run before continuing. Then, we run the method `env.out.print`, which is _also asynchronous_, and will print the provided text to the terminal. Now that we've finished executing code inside the `Main` actor, the behaviour we've called earlier will eventually run, and it will print the last text. -->
在下面的示例中，`Main` actor调用了一个行为`call_me_later`，正如我们所知，该行为是_asynchronous_，因此我们无需等待它运行就可以继续。然后，我们运行方法“ env.out.print”，它也是_asynchronous_，并将提供的文本打印到终端。现在，我们已经完成了在Main角色中执行代码的工作，我们先前调用的行为最终将运行，并且将打印最后的文本。

```pony
actor Main
  new create(env: Env) =>
    call_me_later(env)
    env.out.print("This is printed first")

  be call_me_later(env: Env) =>
    env.out.print("This is printed last")
```

<!-- Since all this code runs inside the same actor, and the calls to the other behaviour `env.out.print` are sequential as well, we are guaranteed that `"This is printed first"` is always printed __before__ `"This is printed last"`. -->
由于所有这些代码都在同一个actor中运行，并且对其他行为`env.out.print`的调用也都是顺序的，因此我们可以确保始终在 __before__ 之前打印出`"This is printed last"`。

<!-- ## Why is this safe? -->
## 为什么这些代码时安全的？

<!-- Because of Pony's __capabilities secure type system__. We've mentioned reference capabilities briefly before when talking about function receiver reference capabilities. The short version is that they are annotations on a type that make all this parallelism safe without any runtime overhead. -->
因为Pony拥有 __权能安全类型系统__ 。在谈论函数接收器参考功能之前，我们已经简要提到了引用权能。简短的版本是它们是对类型的注释，这些注释使所有这些并行性安全无任何运行时开销。

<!-- We will cover reference capabilities in depth later. -->
稍后我们将深入介绍参考功能。

<!-- ## Actors are cheap -->
## Actor的开销非常低

<!-- If you've done concurrent programming before, you'll know that threads can be expensive. Context switches can cause problems, each thread needs a stack (which can be a lot of memory), and you need lots of locks and other mechanisms to write thread-safe code. -->
如果您以前做过并发编程，您将知道线程可能很昂贵。上下文切换可能会导致问题，每个线程都需要一个堆栈（可能会占用很多内存），并且您需要大量的锁和其他机制来编写线程安全的代码。

<!-- But actors are cheap. Really cheap. The extra cost of an actor, as opposed to an object, is about 256 bytes of memory. Bytes, not kilobytes! And there are no locks and no context switches. An actor that isn't executing consumes no resources other than the few extra bytes of memory. -->
但是演员很便宜。真便宜。与对象相比，演员的额外开销约为256个字节的内存。字节，而不是千字节！而且没有锁，也没有上下文切换。除了少数几个额外的内存字节外，没有执行的actor不会消耗资源。

<!-- It's pretty normal to write a Pony program that uses hundreds of thousands of actors. -->
编写一个使用成千上万演员的Pony程序是很正常的。

## Actor finalisers

<!-- Like classes, actors can have finalisers. The finaliser definition is the same (`fun _final()`). All guarantees and restrictions for a class finaliser are also valid for an actor finaliser. In addition, an actor will not receive any further message after its finaliser is called. -->
像班级一样，演员可以有终结者。终结器的定义是相同的（`fun _final（）`）。类终结者的所有保证和限制也对演员终结者有效。另外，参与者的终结器被调用后，它将不再收到任何其他消息。
