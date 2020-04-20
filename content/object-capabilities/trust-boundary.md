---
title: "边界信任（Trust Boundary）"
section: "对象权能模型（Object Capabilities）"
menu:
  toc:
    parent: "object-capabilities"
    weight: 20
toc: true
---

<!-- We mentioned previously that the C FFI can be used to break pretty much every guarantee that Pony makes. This is because, once you've called into C, you are executing arbitrary machine code that can stomp memory addresses, write to anything, and generally be pretty badly behaved. -->
我们之前提到过，C FFI可以用来打破Pony做出的几乎所有约束限制。这是因为，一旦调用了C语言，就可能会执行任意的机器码，这些代码会破坏内存地址、修改任何数据，而且通常表现得非常糟糕。

<!-- ## Trust boundaries -->
## 边界信任

<!-- When we talk about trust, we don't mean things you trust because you think they are perfect. Instead, we mean things you _have_ to trust in order to get things done, even though you know they are _imperfect_. -->
当我们谈论信任的时候，我们并不是指你相信的事情，因为你认为它们是完美的。相反，我们指的是那些你必须信任才能完成的事情，即使你知道它们是不完美的。

<!-- In Pony, when you use the C FFI, you are basically declaring that you trust the C code that's being executed. That's fine, because you may need it to get work done. But what about trusting someone else's code to use the C FFI? You may need to, but you definitely want to know that it's happening. -->
在Pony中，当你使用C FFI时，你基本上是在声明你相信正在执行的C代码。这很好，因为你可能需要它来完成工作。但是信任别人的代码来使用cffi呢?你可能需要知道，但你肯定想知道它正在发生。

<!-- ## Safe packages -->
## 安全包

<!-- The normal way to handle that is to be sure you're using just the code you need to use in your program. Pretty simple! Don't use some random package from the internet without looking at the code and making sure it doesn't do nasty FFI stuff. -->
处理这个问题的常规方法是确保您只使用了程序中需要使用的代码。很简单!不要使用一些随机的包从互联网上没有看到的代码，并确保它不会做令人讨厌的FFI的东西。

<!-- But we can do better than that. -->
但我们可以做得更好。

<!-- In Pony, you can optionally declare a set of _safe_ packages on the `ponyc` command line, like this: -->
在Pony中，你可以选择在‘ponyc’命令行上声明一组_safe_包，就像这样:

```sh
ponyc --safe=files:net:net/ssl my_project
```

<!-- Here, we are declaring that only the `files`, `net` and `net/ssl` packages are allowed to use C FFI calls. We've established our trust boundary: any other packages that try to use C FFI calls will result in a compile-time error. -->
这里，我们声明只有“文件”、“net”和“net/ssl”包才允许使用C FFI调用。我们已经建立了信任边界:任何其他试图使用C FFI调用的包都会导致编译时错误。
