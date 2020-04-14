---
;title: "Hello World: Your First Pony Program"
title: "Hello World"
section: "入门（Getting Started）"
menu:
  toc:
    parent: "getting-started"
    weight: 10
toc: true
---
<!-- Now that you've successfully installed the Pony compiler, let's start programming! Our first program will be a very traditional one. We're going to print "Hello, world!". First, create a directory called `helloworld`: -->
你已经成功的安装了Pony编译器，我们来编写自己的第一个Pony程序：输出"hello,world!"。首先，创建一个目录：`helloworld`：

```bash
$ mkdir helloworld
$ cd helloworld
```

<!-- __Does the name of the directory matter?__ Yes, it does. It's the name of your program! By default when your program is compiled, the resulting executable binary will have the same name as the directory your program lives in. You can also set the name using the --bin-name or -b options on the command line. -->
__目录名称重要吗？__ 是的。这将是你的程序最终输的名！默认情况下，在编译程序时，生成的可执行二进制文件将使用源码的目录名。你可以在命令行上使用--bin-name或-b选项指定想要的文件名。

<!-- ## The code -->
## 代码

Then, create a file in that directory called `main.pony`.
然后用你的文本编辑器在这个目录中新建一个文件：`main.pony`。

<!-- __Does the name of the file matter?__ Not to the compiler, no. Pony doesn't care about filenames other than that they end in `.pony`. But it might matter to you! By giving files good names, it can be easier to find the code you're looking for later. -->
__文件名重要吗？__ 对编译器来说不重要。 Pony只关心源文件的扩展名`.pony`，不关心文件名。但文件名对你来说很重要！给源文件起个好名字，可以方便以后定位代码。

<!-- In your file, put the following code: -->
在文件中输入下面的代码：

```pony
actor Main
  new create(env: Env) =>
    env.out.print("Hello, world!")
```

<!-- ## Compiling the program -->
## 编译程序

<!-- Now compile it: -->
现在可以编译程序了：

```bash
$ ponyc
Building .
Building builtin
Generating
Optimising
Writing ./helloworld.o
Linking ./helloworld
```

<!-- (If you're using Docker, you'd write something like `$ docker run -v Some_Absolute_Path/helloworld:/src/main ponylang/ponyc`, depending of course on what the absolute path to your `helloworld` directory is.) -->
（如果你想要Docker中使用Pony，可以这么做：`$ docker run -v Some_Absolute_Path/helloworld:/src/main ponylang/ponyc`，需要将helloworld目录的路径换成自己的。）

<!-- Look at that! It built the current directory, `.`, plus the stuff that is built into Pony, `builtin`, it generated some code, optimised it, created an object file (don't worry if you don't know what that is), and linked it into an executable with whatever libraries were needed. If you're a C/C++ programmer, that will all make sense to you, otherwise, it probably won't, but that's ok, you can ignore it. -->
你会发现，它在当前目录进行了构建，`.`，Pony对代码进行了优化，并创建了一些目标文件（如果您不知道那是什么，不用担心） ，然后将其链接到了一个可执行文件中。如果您是C/C++程序员，那对你来说可能是有意义的，否则你可以忽略它。

<!-- __Wait, it linked too?__ Yes. You won't need a build system (like `make`) for Pony. It handles that for you (including handling the order of dependencies when you link to C libraries, but we'll get to that later). -->
__构建过程中也进行链接吗？__ 是的。Pony不需要额外的构建程序（例如`make`），自己就会处理（包括在链接到C库时处理依赖项的顺序，后面章节会介绍）。

<!-- ## Running the program -->
## 运行程序

<!-- Now we can run the program: -->
现在可以运行程序了：

```bash
$ ./helloworld
Hello, world!
```

<!-- Congratulations, you've written your first Pony program! Next, we'll explain what some of that code does. -->
恭喜，你已经完成了第一个Pony程序！下一节我们会讲解这几行代码的含义。
