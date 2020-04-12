---
；title: "Hello World: How It Works"
title: "庖丁解牛"
section: "入门（Getting Started）"
menu:
  toc:
    parent: "getting-started"
    weight: 15
toc: true
---
<!-- Let's look at our `helloworld` code again: -->
首先我们来看一下`helloworld`代码：

```pony
actor Main
  new create(env: Env) =>
    env.out.print("Hello, world!")
```

<!-- Let's go through that line by line. -->
接下来我们逐行解释。

<!-- ## Line 1 -->
## 第一行

```pony
actor Main
```

<!-- This is a __type declaration__. The keyword `actor` means we are going to define an actor, which is a bit like a class in Python, Java, C#, C++, etc. Pony has classes too, which we'll see later. -->
这是一个`类型声明`。关键字`actor`表示我们定义了一个并发单元，但是暂时你只需要了解到这是Pony中的声明Main函数的做法，等同于于Python, Java, C#, C++等语言中的程序入口。Pony中也有类的概念，后面会讲解。

<!-- The difference between an actor and a class is that an actor can have __asynchronous__ methods, called __behaviours__. We'll talk more about that later. -->
actor和类的区别是：actor可以`异步执行`函数，这些函数在Pony中被称为`行为（behaviours）`。后面会讲到。

<!-- A Pony program has to have a `Main` actor. It's kind of like the `main` function in C or C++, or the `main` method in Java, or the `Main` method in C#. It's where the action starts. -->
Pony程序中的`Main`actor跟C、C++中的`main`函数，或者Java、C#中的`Main`方法类似，都是表示程序的入口。

<!-- ## Line 2 -->
## 第二行

```pony
  new create(env: Env) =>
```

<!-- This is a __constructor__. The keyword `new` means it's a function that creates a new instance of the type. In this case, it creates a new __Main__. -->
这是一个`构造函数`。关键字`new`表示它可以创建类型实例，这个实例的类型就是`Main`。

<!-- Unlike other languages, constructors in Pony have names. That means there can be more than one way to construct an instance of a type. In this case, the name of the constructor is `create`. -->
和其他语言不同，Pony的构造函数可以有名字。我们可以用不同的构造函数创建特定的实例。这里我们用的是默认构造函数`create`。

<!-- The parameters of a function come next. In this case, our constructor has a single parameter called `env` that is of the type `Env`. -->
接下来构造函数的参数。这里我们的构造函数里定义了一个名为`env`的参数，类型为`Env`。

<!-- In Pony, the type of something always comes after its name and is separated by a colon. In C, C++, Java or C#, you might say `Env env`, but we do it the other way around (like Go, Pascal, Rust, TypeScript, and a bunch of other languages). -->
Pony的参数类型总是在参数之后，并且需要用冒号分割。如果你熟悉C、C++、Java、C#，你可能习惯了`Env env`的写法,不过Pony的参数写法其实也是很常见的（比如在Go、Pascal、Rust、Typecript等语言中）。

<!-- It turns out, our `Main` actor __has__ to have a constructor called `create` that takes a single parameter of type `Env`. That's how all programs start! So the beginning of your program is essentially the body of that constructor. -->
现在明白了，`Main` actor 有一个默认构造函数，它接受单个参数，参数类型为`Env`。程序的入口就是构造函数的函数体。

<!-- __Wait, what's the body?__ It's the code that comes after the `=>`. -->
__函数体是什么？怎么定义？__ 注意看`=>`后面的代码。

<!-- ## Line 3 -->
## 第三行

```pony
    env.out.print("Hello, world!")
```

<!-- This is your program! What the heck is it doing? -->
这个函数体里面就是你的代码。

<!-- In Pony, a dot is either a field access or a method call, much like other languages. If the name after the dot has parentheses after it, it's a method call. Otherwise, it's a field access. -->
在Pony中，怎么确定一个`.`的作用是字段访问还是方法调用？注意观察小括号。有小括号就是方法调用，没有就是字段访问。

<!-- So here, we start with a reference to `env`. We then look up the field `out` on our object `env`. As it happens, that field represents __stdout__, i.e. usually it means printing to your console. Then, we call the `print` method on `env.out`. The stuff inside the parentheses are the arguments to the function. In this case, we are passing a __string literal__, i.e. the stuff in double quotes. -->
这行代码引用了`env`参数。 首先调用了`env`的`out`字段，这个字段表示 __（标准输出流）stdout__ ，内容会被输出到控制台。 然后，调用`out`的`print`函数，将"hello,world!"字符串输出到控制台。

<!-- In Pony, string literals can be in double quotes, `"`, in which case they follow C/C++ style escaping (using stuff like \n), or they can be triple-quoted, `"""` like in Python, in which case they are considered raw data. -->
Pony中的字符串定义可以使用双引号`"`也可以使用三引号。双引号的定义方式是C/C++的风格的（支持\n方式的转译）。三引号`"""`可以定义是Python风格的原始字符串，内容不会被转译。

<!-- __What's an Env, anyway?__ It's the "environment" your program was invoked with. That means it has command line arguments, environment variables, __stdin__, __stdout__, and __stderr__. Pony has no global variables, so these things are explicitly passed to your program. -->
__Env是什么？__ 它是你的程序执行的上下文信息，其中包含`命令行参数`、`环境变量`、`标准输入流`、`标准输出流`、`标准错误流`。Pony不允许全局变量，所以你可以通过Env来初始化配置。

<!-- ## That's it! -->
## 总结

<!-- Really, that's it. The program begins by creating a `Main` actor, and in the constructor, we print "Hello, world!" to __stdout__. Next, we'll start diving into the Pony type system. -->
就是这样。Pony程序开始运行时会创建一个名字为`Main` actor实例，然后执行里面的逻辑：打印"hello,woeld!"到`标准输出流`。接下来我们会讲解Pony的类型系统。
