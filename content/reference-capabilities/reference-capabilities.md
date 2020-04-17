---
title: "引用权能（Reference Capabilities）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 20
toc: true
---

<!-- So if the object _is_ the capability, what controls what we can do with the object? How do we express our _access rights_ on that object? -->
如果对象具有权能，我们该怎么来控制？比如如何表示该对象的 _访问权限（access rights）_ ？

<!-- In Pony, we do it with _reference capabilities_. -->
Pony中，可以用`引用权能（reference capabilities）`来处理。

<!-- ## Rights are part of a capability -->
## 权限只是权能的一部分

<!-- If you open a file in UNIX and get a file descriptor back, that file descriptor is a token that designates an object - but it isn't a capability. To be a capability, we need to open that file with some permission - some access right. For example: -->
如果在UNIX系统中打开一个文件，会返回一个文件描述符，该文件描述符只是一个文件指向标识——并不是一个权能（描述符只体现了`能`没有体现`权`,描述符使可以访问文件，但是访问权呢？）。完整的`权能`：需要获得权限，然后指定文件路径和打开模式。例如:

````bash
chmod u+w /etc/passwd
````

```C
int fd = open("/etc/passwd", O_RDWR);
```

<!-- Now we have a token and a set of rights. -->
现在我们拥有了`/etc/passwd`文件的读写权，然后使用`O_RDWR`模式打开们见得到了一个文件描述符。访问授权、打开模式、文件描述符共同构成了一个`权能`。

<!-- In Pony, every reference has both a type and a reference capability. In fact, the reference capability is _part_ of its type. These allow you to specify which of your objects can be shared with other actors and allow the compiler to check that what you're doing is concurrency safe. -->
在Pony中，所有被引用的数据都有一个`类型`和一个`引用权能（reference capabilities）`。实际上，`引用权能（reference capabilities）`是其类型的一部分。这允许你指定哪些对象可以与其他actor共享，并允许编译器检查你的逻辑是并发安全的。

<!-- ## Basic concepts -->
## 基本概念（Basic concepts）

<!-- There are a few simple concepts you need to understand before reference capabilities will make any sense. We've talked about some of these already, and some may already be obvious to you, but it's worth recapping here. -->
在`引用权能（reference capabilities）`变得有意义之前，您需要理解一些简单的概念。我们已经讨论过其中的一些，有些可能对你来说已经很明显了，但是在这里值得回顾一下。

<!-- __Shared mutable data is hard__ -->
__共享可变数据非常困难__

<!-- The problem with concurrency is shared mutable data. If two different threads have access to the same piece of data then they might try to update it at the same time. At best this can lead to the two threads having different versions of the data. At worst the updates can interact badly resulting in the data being overwritten with garbage. The standard way to avoid these problems is to use locks to prevent data updates from happening at the same time. This causes big performance hits and is very difficult to get right, so it causes lots of bugs. -->
并发性的问题根源来自于共享可变数据。如果两个线程可以同时访问同一个数据，那么它们可能会同时修改它。这最多可能导致两个线程拥有不同版本的数据。在最坏的情况下，更新可能会交互不良，导致数据被垃圾覆盖。避免这些问题的常规做法是使用锁来防止同时发生数据更新。这会导致巨大的性能开销（上下文切换），并且一不小心就会导致bug。

<!-- __Immutable data can be safely shared__ -->
__不可变数据可以安全地共享__

<!-- Any data that is immutable (i.e. it cannot be changed) is safe to use concurrently. Since it is immutable it is never updated and it's the updates that cause concurrency problems. -->
任何不可变的数据(即不会被修改数据)都可以安全地并发使用。因为它们永远不会更新，正是更新导致了并发性问题。

<!-- __Isolated data is safe__ -->
__被隔离的数据是安全的__

<!-- If a block of data has only one reference to it then we call it __isolated__. Since there is only one reference to it, isolated data cannot be __shared__ by multiple threads, so there are no concurrency problems. Isolated data can be passed between multiple threads. As long as only one of them has a reference to it at a time then the data is still safe from concurrency problems. -->
如果一个数据块只有一个对它的引用，那么我们称它为 __被隔离的数据(isolated)__ 。因为只有一个对它的引用，所以不会被被多个线程共享，也就不存在并发问题。`被隔离的数据`可以在多个线程之间传递。只要同一时间只存在一个对它的引用，那么数据就是安全的，不会出现并发问题。

<!-- __Isolated data may be complex__ -->
__被隔离的数据有可以是复合类型__

<!-- An isolated piece of data may be a single byte. But it can also be a large data structure with multiple references between the various objects in that structure. What matters for the data to be isolated is that there is only a single reference to that structure as a whole. We talk about the __isolation boundary__ of a data structure. For the structure to be isolated: -->
一个被隔离的数据（isolated）可以是一个字节，也可以是一个大型数据结构，并且在该结构中的各个对象之间有多个引用。对于隔离的数据来说，重要的是整个程序中只有一个单一的引用。我们讨论了数据结构的隔离边界。对结构进行隔离:

<!-- 1. There must only be a single reference outside the boundary that points to an object inside. -->
<!-- 2. There can be any number of references inside the boundary, but none of them must point to an object outside. -->
1. 外部必须只能有一个指向内部对象的引用。
2. 外部可以有任意数量的引用，但它们都不能指向外部的对象。

<!-- __Every actor is single threaded__ -->
__每个`actor`都只会在`一个`线程中运行__

<!-- The code within a single actor is never run concurrently. This means that, within a single actor, data updates cannot cause problems. It's only when we want to share data between actors that we have problems. -->
actor内部的代码永远不会并发运行。在actor中，数据更新不会导致并发问题。只有当我们希望在actor之间共享数据时，才会遇到问题。

<!-- __OK, safely sharing data concurrently is tricky. How do reference capabilities help?__ -->
__好吧，看来同时安全地共享数据确实很棘手，来看看`引用权能（reference capabilities）`是怎么做到的。__

<!-- By sharing only immutable data and exchanging only isolated data we can have safe concurrent programs without locks. The problem is that it's very difficult to do that correctly. If you accidentally hang on to a reference to some isolated data you've handed over or change something you've shared as immutable then everything goes wrong. What you need is for the compiler to force you to live up to your promises. Pony reference capabilities allow the compiler to do just that. -->
通过`只共享不可变的数据`和`只交换被隔离的数据`，我们可以拥有没有锁的安全并发程序。问题是要正确地做到这一点是非常困难的。如果您不小心挂起了对某些已提交的独立数据的引用，或者将已共享的内容更改为不可变的，那么一切都会出错。你需要的是编译器强迫你去实现你的承诺。小马`引用权能（reference capabilities）`允许编译器这样做。

<!-- ## Type qualifiers -->
## 类型限定符（Type qualifiers）

<!-- If you've used C/C++, you may be familiar with `const`, which is a _type qualifier_ that tells the compiler not to allow the programmer to _mutate_ something. -->
如果你使用过C/C++，您可能熟悉`const`，它是一个 _类型限定符（type qualifier）_ ，告诉编译器不允许程序员修改某些东西。

A reference capability is a form of _type qualifier_ and provides a lot more guarantees than `const` does!
`引用权能（reference capabilities）`是 _type qualifier_ 的一种形式，它提供了比`const`多得多的保证!

<!-- In Pony, every use of a type has a reference capability. These capabilities apply to variables, rather than to the type as a whole. In other words, when you define a class `Wombat`, you don't pick a reference capability for all instances of the class. Instead, `Wombat` variables each have their own reference capability. -->
在Pony中，所有的类型都有一个`引用权能（reference capabilities）`作为类型限定符。这个规则只适用于变量（局部变量、字段、参数等），而不是类型。换句话说，当你定义一个类`Wombat`时，你不会为这个类的所有实例使用某个`引用权能（reference capabilities）`。相反，`Wombat`的每个字段都有自己的`引用权能（reference capabilities）`。

<!-- As an example, in some languages, you have to define a type that represents a mutable `String` and another type that represents an immutable `String`. For example, in Java, there is a `String` and a `StringBuilder`. In Pony, you can define a single class `String` and have some variables that are `String ref` (which are mutable) and other variables that are `String val` (which are immutable). -->
例如，在某些语言中，必须定义一个表示可变`字符串(String)`的类型和另一个表示不可变`字符串`的类型。例如，在Java中，有一个`String`和一个`StringBuilder`。在Pony中，一个`String`类就够了，定义可变字符串：`String ref`和不可变字符串：`String val`。

<!-- ## The list of reference capabilities -->
## `引用权能（reference capabilities）`列表

<!-- There are six reference capabilities in Pony and they all have strict definitions and rules on how they can be used. We'll get to all of that later, but for now here are their names and what you use them for: -->
Pony有六种`引用权能（reference capabilities）`，它们都有严格的定义和使用规则。我们稍后会讲到，现在先来看看它们的名字和用途:

<!-- __Isolated__, written `iso`. This is for references to isolated data structures. If you have an `iso` variable then you know that there are no other variables that can access that data. So you can change it however you like and give it to another actor. -->
__被隔离的数据（Isolated）__，关键字为`iso`。这是为了引用隔离的数据结构。如果你有一个“iso”变量，那么你就知道没有其他变量可以访问这些数据。所以你可以随心所欲地改变它，把它给另一个演员。

<!-- __Value__, written `val`. This is for references to immutable data structures. If you have a `val` variable then you know that no-one can change the data. So you can read it and share it with other actors. -->
__不可变数据（Value）__,关键字为`val`。这是用于引用不可变的数据结构。如果你有一个“val”变量，那么你就知道没有人可以改变数据。所以你可以读它，并与其他演员分享。

<!-- __Reference__, written `ref`. This is for references to mutable data structures that are not isolated, in other words, "normal" data. If you have a `ref` variable then you can read and write the data however you like and you can have multiple variables that can access the same data. But you can't share it with other actors. -->
__可变数据（Reference）__，关键字为`ref`。这是为了引用非独立的可变数据结构，换句话说，就是“正常”数据。如果你有一个`ref`变量，那么你可以读和写的数据，无论你喜欢，你可以有多个变量，可以访问相同的数据。但是你不能和其他演员分享。

<!-- __Box__. This is for references to data that is read-only to you. That data might be immutable and shared with other actors or there may be other variables using it in your actor that can change the data. Either way, the `box` variable can be used to safely read the data. This may sound a little pointless, but it allows you to write code that can work for both `val` and `ref` variables, as long as it doesn't write to the object. -->
__只读引用（Box）__ ，关键字为`box`，这是为了引用对您来说是只读的数据。这些数据可能是不可变数据，并与其他actor共享，或者在您的actor中使用它的其他变量可以更改数据。无论哪种方式，`box`变量都可以用来安全地读取数据。这听起来可能有点无意义，但它允许您编写既可以用于`val`变量又可以用于`ref`变量的代码，只要它不写入对象即可。

<!-- __Transition__, written `trn`. This is used for data structures that you want to write to, while also holding read-only (`box`) variables for them. You can also convert the `trn` variable to a `val` variable later if you wish, which stops anyone from changing the data and allows it be shared with other actors. -->
__可传输数据（Transition）__,关键字为`trn`。这用于您希望写入的数据结构，同时为它们保存只读(`box`)变量。如果您愿意，您还可以稍后将`trn`变量转换为`val`变量，这将阻止任何人更改数据，并允许与其他actor共享数据。

<!-- __Tag__. This is for references used only for identification. You cannot read or write data using a `tag` variable. But you can store and compare `tag`s to check object identity and share `tag` variables with other actors. -->
__标识（Tag）__ 关键字为`tag`。它仅用于定义标识符。不能对`tag`变量进行读写。但是`tag`可以存储和比较，可以方便的用以对象标识检查，另外`tag`变量可以与其他actor共享。

<!-- Note that if you have a variable referring to an actor then you can send messages to that actor regardless of what reference capability that variable has. -->
注意，如果您有一个变量引用一个actor，那么您可以向该actor发送消息，而不用关心该变量具有什么`引用权能（reference capabilities）`。

<!-- ## How to write a reference capability -->
## 引用权能如何使用（How to write a reference capability）

<!-- A reference capability comes at the end of a type. So, for example: -->
`引用权能（reference capabilities）`限定符放在类型后。举个例子:

```pony
String iso // An isolated string
String trn // A transition string
String ref // A string reference
String val // A string value
String box // A string box
String tag // A string tag
```

<!-- __What does it mean when a type doesn't specify a reference capability?__ It means you are using the _default_ reference capability for that type, which is defined along with the type. Here’s an example from the standard library: -->
当类型没有指定`引用权能（reference capabilities）`时，它意味着什么?__意味着你使用了该类型的 _默认_ `引用权能（reference capabilities）`，`默认引用权能`是和类型一起定义的。下面是来自标准库的一个例子:

```pony
class val String
```

<!-- When we use a String we usually mean an immutable string value, so we make `val` the default reference capability for `String` (but not necessarily for `String` constructors, see below). For example, when we don't specify the capability in the following code, the compiler understands that we are using the default reference capability `val` specified in the type definition: -->
当我们使用一个字符串时，我们通常指的是一个不可变的字符串值，因此我们将`val`作为`String`的默认`引用权能（reference capabilities）`(但不一定是`String`构造函数，请参阅下面)。例如，当我们没有在下面的代码中指定`引用权能`时，编译器会使用默认`引用权能（reference capabilities）``val`:

```pony
let a: String val = "Hello, world!"
let b: String = "I'm a wombat!" // Also a String val
```

<!-- __So do I have to specify a reference capability when I define a type?__ Only if you want to. There are sensible defaults that most types will use. These are `ref` for classes, `val` for primitives (i.e. immutable references), and `tag` for actors. -->
__所以定义一个类型时，必须指定一个`引用权能（reference capabilities）`吗？__ 有特殊需求时才需要制定，大多数类型都会使用合理的默认值。类使用`ref`，基元类使用`val`(即不可变的引用)，以及actor使用`tag`。

<!-- ## How to create objects with different capabilities -->
## 如何创建具有不同引用权能的对象

<!-- When you write a constructor, by default, that constructor will either create a new object with `ref` or `tag` as the capability. In the case of actors, the constructor will always create a `tag`. For classes, if defaults to `ref` but you can create with other capabilities. Let's take a look at an example: -->
在编写构造函数时，该构造函数默认将创建一个具有`ref`或`tag`功能的新对象。actor的构造函数总是创建一个`tag`。对于class默认为`ref`，但是你可以使用其他`引用权能`创建。来看一个例子:

```pony
class Foo
  let x: U32

  new val create(x': U32) =>
    x = x'
```

<!-- Now when you call `Foo.create(1)`, you'll get a `Foo val` instead of `Foo ref`. -->
当你调用Foo。create(1)时，你会得到一个`Foo val`而不是`Foo ref`。
<!-- But what if you want to create both `val` and `ref` `Foo`s? You could do something like this: -->
但是如果你想同时创建`val`和`ref`的`Foo`实例呢？你可以这样做：

```pony
class Foo
  let x: U32

  new val create_val(x': U32) =>
    x = x'

  new ref create_ref(x': U32) =>
    x = x'
```

<!-- But, that's probably not what you'd really want to do. Better to use the capabilities recovery facilities of Pony that we'll cover that later in the [Recovering Capabilities]({{< relref "recovering-capabilities.md" >}}) section. -->
但是，这不是最佳做法。最好的做法是使用Pony的`权能借用`功能，我们将在后面的[权能借用]({{< relref "recovering-capabilities.md" >}})一节中介绍。
