---
title: "对象权能模型（Object Capabilities）"
section: "对象权能模型（Object Capabilities）"
menu:
  toc:
    parent: "object-capabilities"
    weight: 10
toc: true
---

<!-- Pony's capabilities-secure type system is based on the object-capability model. That sounds complicated, but really it's elegant and simple. The core idea is this: -->
Pony的类型安装系统是构建在[对象权能模型](https://everything.explained.today/Object-capability_model/)的理论基础上的。感觉上很复杂，但是其实非常简单优雅，这是`对象权能模型`的核心概念：

<!-- > A capability is an unforgeable token that (a) designates an object and (b) gives the program the authority to perform a specific set of actions on that object. -->
> 权能是一个不可伪造的令牌，它(a)指定一个对象，(b)授予程序对该对象执行特定操作集的权限。

<!-- So what's that token? It's an address. A pointer. A reference. It's just... an object. -->
那代币是什么?这是一个地址。一个指针。一个引用。它只是...一个对象。

<!-- ## How is that unforgeable? -->
## 这怎么可能是不可伪造的呢?

<!-- Since Pony has no pointer arithmetic and is both type-safe and memory-safe, object references can't be "invented" (i.e. forged) by the program. You can only get one by constructing an object or being passed an object. -->
由于Pony没有指针算法，并且是类型安全的，内存安全的，对象引用不能被程序“发明”(即伪造)。您只能通过构造一个对象或传递一个对象来获得一个。

<!-- __What about the C FFI?__ Using the C FFI can break this guarantee. We'll talk about the __C FFI trust boundary__ later, and how to control it. -->
__那么C FFI呢?__ 使用信用证可以打破这一保证。稍后我们将讨论如何控制边界。

<!-- ## What about global variables? -->
## 那么全局变量呢?

<!-- They're bad! Because you can get them without either constructing them or being passed them. -->
他们是坏的!因为您可以在不构造它们或不传递它们的情况下获得它们。

<!-- Global variables are a form of what is called _ambient authority_. Another form of ambient authority is unfettered access to the file system. -->
全局变量是所谓的_ambient authority_的一种形式。环境权限的另一种形式是不受约束地访问文件系统。

<!-- Pony has no global variables and no global functions. That doesn't mean all ambient authority is magically gone - we still need to be careful about the file system, for example. Having no globals is necessary, but not sufficient, to eliminate ambient authority. -->
Pony没有全局变量，也没有全局函数。这并不意味着所有的环境权限都神奇地消失了——例如，我们仍然需要注意文件系统。没有全局权限对于消除环境权限是必要的，但还不够。

<!-- ## How does this help? -->
## 这有什么用呢?

<!-- Instead of having permissions lists, access control lists, or other forms of security, the object-capabilities model means that if you have a reference to an object, you can do things with that object. Simple and effective. -->
与使用权限列表、访问控制列表或其他形式的安全性不同，对象-功能模型意味着，如果您有对某个对象的引用，则可以使用该对象进行操作。简单而有效的。

<!-- There's a great paper on how the object-capability model works, and it's pretty easy reading: -->
有一篇关于对象-能力模型如何工作的好论文，读起来很容易:

<!-- [Capability Myths Demolished](http://srl.cs.jhu.edu/pubs/SRL2003-02.pdf) -->
[权能梦碎——对象权能模型跌落神坛](http://srl.cs.jhu.edu/pubs/SRL2003-02.pdf)

<!-- ## Capabilities and concurrency -->
## 权能和并发

<!-- The object-capability model on its own does not address concurrency. It makes clear _what_ will happen if there is simultaneous access to an object, but it does not prescribe a single method of controlling this. -->
对象功能模型本身并不处理并发性。它明确了如果同时访问一个对象将会发生什么，但是没有规定控制这个对象的单一方法。

<!-- Combining capabilities with the actor-model is a good start, and has been done before in languages such as [E](http://erights.org/) and Joule. -->
将功能与角色模型相结合是一个良好的开端，以前已经在[E](http://erights.org/)和Joule等语言中实现过。

<!-- Pony does this and also uses a system of _reference capabilities_ in the type system. -->
Pony做到了这一点，并在类型系统中使用了一个_reference功能的系统。
