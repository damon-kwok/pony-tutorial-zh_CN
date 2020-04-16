---
title: "准备工作（What You Need）"
section: "入门（Getting Started）"
menu:
  toc:
    parent: "getting-started"
    weight: 5
toc: true
---
<!-- To get started, you'll need a text editor and the [ponyc](https://github.com/ponylang/ponyc) compiler. Or if you are on a not supported platform or don't want to install the compiler you can use the [Pony's Playground](https://playground.ponylang.io/). -->
你需要一个[文本编辑器](https://github.com/ponylang/ponyc#editor-support)，还有[Pony](https://github.com/ponylang/ponyc)编译器。如果想偷点懒可以直接使用[Pony的Playground](https://playground.ponylang.io/)。

<!-- ## The Pony compiler -->
## Pony编译器

<!-- Before you get started, please check out the [installation instructions](https://github.com/ponylang/ponyc/blob/master/INSTALL.md) for the Pony compiler. -->
Pony编译器的：[安装教程](https://github.com/ponylang/ponyc/blob/master/INSTALL.md)。Windows用户可以直接[下载Pony](https://dl.cloudsmith.io/public/ponylang/releases/raw/versions/latest/ponyc-x86-64-pc-windows-msvc.zip)。另外在Windows上编译Pony程序你需要安装[VsualStudio](https://www.visualstudio.com/vs/community/)或[Microsoft C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)。

<!-- ## A text editor -->
## 文本编辑器

<!-- While you can write code using any editor, it's nice to use one with some support for the language. We maintain a list of [editors supporting Pony](https://github.com/ponylang/ponyc#editor-support). -->
你可以用自己喜欢的文本编辑器，这里是Pony对[编辑器的支持列表](https://github.com/ponylang/ponyc#editor-support)

<!-- ## The compiler -->
## 编译器说明

<!-- Pony is a _compiled_ language, rather than an _interpreted_ one. In fact, it goes even further: Pony is an _ahead-of-time_ (AOT) compiled language, rather than a _just-in-time_ (JIT) compiled language. -->
Pony是一个`编译型`语言，非`解释型`语言。更进一步来说：Pony是一种`提前（AOT）`编译型语言，而不是`即时（JIT）`编译型语言。

<!-- What this means is that once you build your program, you can run it over and over again without needing a compiler or a virtual machine or anything else. It's a complete program, all on its own. -->
这表示只要你编译成功，就可以直接运行它，不再需编译器，虚拟机或其他任何运行时。编译出来的是一个完整程序。

<!-- But it also means you need to build your program before you can run it. In an interpreted language or a JIT compiled language, you tend to do things like this to run your program: -->
所以你必须先构建程序，才能运行。在解释性语言或JIT编译语言中，你可能会用下面的方式来运行程序：

```bash
$ python helloworld.py
```

<!-- Or maybe you put a __shebang__ in your program (like `#!/usr/bin/env python`), then `chmod` to set the executable bit, and then do: -->
或者你可能在代码文件头部加入`shebang`(`#!/usr/bin/env python3`)，然后使用`chmod`添加运行权限，然后运行：

```bash
$ ./helloworld.py
```

<!-- When you use Pony, you don't do any of that! -->
但是这些方法在Pony中行不通！

<!-- ## Compiling your program -->
## 编译你的程序

<!-- If you are in the same directory as your program, you can just do: -->
进入代码目录，直接输入`ponyc`就可以进行编译：

```bash
$ ponyc
```

<!-- That tells the Pony compiler that your current working directory contains your source code, and to please compile it. If your source code is in some other directory, you can tell ponyc where it is: -->
这相当于告诉编译器你的代码在当前目录里，请遍历所有源码文件然后编译它们。如果代码在其他目录里，把目录作为参数传入就行了：

```bash
$ ponyc path/to/my/code
```

<!-- There are other options as well, but we'll cover those later. -->
还有一些其他编译选项，后面会介绍。
