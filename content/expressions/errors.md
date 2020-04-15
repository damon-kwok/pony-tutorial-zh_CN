---
title: "异常处理（Errors）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 70
toc: true
---

<!-- Pony doesn't feature exceptions as you might be familiar with them from languages like Python, Java, C++ et al. It does, however, provide a simple partial function mechanism to aid in error handling. Partial functions and the `error` keyword used to raise them look similar to exceptions in other languages but have some important semantic differences. Let's take a look at how you work with Pony's error and then how it differs from the exceptions you might be used to. -->
你可能熟悉Python、Ruby、Java、C++等语言中的异常处理方式，Pony中有所不同，Pony提供了一个更简单的机制来帮助处理异常。Pony触发异常的方式与其他语言有一些重要的语义区别，Pony用`error`（还有`as`）关键字和`偏函数(Partial functions)`来触发异常。让我们看看Pony是如何处理异常的，然后和你习惯的语言对比一下有什么不同。

<!-- ## Raising and handling errors -->
## 异常的触发和处理(Raising and handling errors)

<!-- An error is raised with the command `error`. At any point, the code may decide to declare an `error` has occurred. Code execution halts at that point, and the call chain is unwound until the nearest enclosing error handler is found. This is all checked at compile time so errors cannot cause the whole program to crash. -->
使用`error`可以触发一个异常。可以任何时候都可以使用`error`。此时后续代码会停止执行，调用链被解除，逻辑跳寻找并转到异常处理代码块中继续执行。异常处理机制是在编译时会进行检查，确保在运行时不会导致整个程序崩溃。

<!-- Error handlers are declared using the `try`-`else` syntax. -->
异常处理程序是使用`try`-`else`语法声明的。

```pony
try
  callA()
  if not callB() then error end
  callC()
else
  callD()
end
```

<!-- In the above code `callA()` will always be executed and so will `callB()`. If the result of `callB()` is true then we will proceed to `callC()` in the normal fashion and `callD()` will not then be executed. -->
在上面的代码中，`callA()`总是会被执行，`callB()`也会被执行。如果`callB()`的返回值为`true`，那么我们将按照正常方式继续执行`callC()`，那么`callD()`将不会被执行。

<!-- However, if `callB()` returns false, then an error will be raised. At this point, execution will stop and the nearest enclosing error handler will be found and executed. In this example that is, our else block and so `callD()` will be executed. -->
但是，如果`callB()`返回`false`，则会触发一个异常。此时，执行将停止，寻找并执行最近的异常处理程序。在本例中，else中的`callD()`将被执行。

<!-- In either case, execution will then carry on with whatever code comes after the `try end`. -->
在这两种情况下，程序都将继续执行`try end`后面的逻辑。

<!-- __Do I have to provide an error handler?__ No. The `try` block will handle any errors regardless. If you don't provide an error handler then no error handling action will be taken - execution will simply continue after the `try` expression. -->
__必须提供一个异常处理程序吗?__ 不需要。`try`可以单独工作。如果不提供`else`，则不会执行任何异常处理操作,直接转转到`try`表达式之后继续执行。

<!-- If you want to do something that might raise an error, but you don't care if it does you can just put in it a `try` block without an `else`. -->
如果有一些可能会触发异常的逻辑，但你不需要在发生后做出处理(比如:一个文件存在的话就读取它的内容)，可以把这段逻辑里面放在一个`try`中,不需要提供`else`。

```pony
try
  // Do something that may raise an error
end
```

<!-- __Is there anything my error handler has to do?__ No. If you provide an error handler then it must contain some code, but it is entirely up to you what it does. -->
__异常处理逻辑中需要做些什么? __ 无所谓。如果您提供了一个异常处理程序，那么它必须包含一些代码，具体执行些什么逻辑取决于你自己。

<!-- __What's the resulting value of a try block?__ The result of a `try` block is the value of the last statement in the `try` block, or the value of the last statement in the `else` clause if an error was raised. If an error was raised and there was no `else` clause provided, the result value will be `None`. -->
一个`try`代码块的返回值是什么?`try`的返回值是`try`的最后一个表达式的值，如果出现过程中出现了异常，就使用`else`子句中的最后一个表达式的值。如果出现了一个异常，并且没有提供`else`子句，那么返回值将是`None`。

<!-- ## Partial functions -->
## 偏函数(Partial functions)

<!-- Pony does not require that all errors are handled immediately as in our previous examples. Instead, functions can raise errors that are handled by whatever code calls them. These are called partial functions (this is a mathematical term meaning a function that does not have a defined result for all possible inputs, i.e. arguments). Partial functions __must__ be marked as such in Pony with a `?`, both in the function signature (after the return type) and at the call site (after the closing parentheses). -->
Pony并不要求像前面的例子那样立即处理所有的异常。函数执行时可能触发异常，而这些异常可以由调用它们的代码处理。这些被称为`偏函数(Partial functions)`(这是一个数学术语，意思是对于所有可能的输入(即参数)没有定义返回值的函数)。`偏函数`必须在函数签名(定义返回类型)之后和调用位置(右括号之后)加上`?`。

<!-- For example, a somewhat contrived version of the factorial function that accepts a signed integer will error if given a negative input. It's only partially defined over its valid input type. -->
下面示例，如果输入为负数，那么接受有符号整数的阶乘函数就会出错。这个函数只在有效的输入时才会正确执行。

```pony
fun factorial(x: I32): I32 ? =>
  if x < 0 then error end
  if x == 0 then
    1
  else
    x * factorial(x - 1)?
  end
```

<!-- Everywhere that an error can be generated in Pony (an error command, a call to a partial function, or certain built-in language constructs) must appear within a `try` block or a function that is marked as partial. This is checked at compile time, ensuring that an error cannot escape handling and crash the program. -->
在Pony会发生异常的逻辑(`error`表达式，`偏函数`的调用，或者某些内置的机制（比如`as`）)，都必须实用`try`代码块，或者放在`偏函数`中。编译时对此进行检查，以确保异常的触发不会导致程序崩溃。

<!-- Prior to Pony 0.16.0, call sites of partial functions were not required to be marked with a `?`. This often led to confusion about the possibilities for control flow when reading code. Having every partial function call site clearly marked makes it very easy for the reader to immediately understand everywhere that a block of code may jump away to the nearest error handler, making the possible control flow paths more obvious and explicit. -->
在Pony的`0.16.0`版本之前，`偏函数`的调用位置不需要标记`?`，在阅读代码时会有点费解。之后的版本为`偏函数`作出清楚的标记，代码更易读，可以清晰的了解逻辑会跳转到最近的异常处理代码块中执行，条例更加清晰明确。

<!-- ## Partial constructors and behaviours -->
## 部分构造函数和行为(Partial constructors and behaviours)

<!-- Class constructors may also be marked as partial. If a class constructor raises an error then the construction is considered to have failed and the object under construction is discarded without ever being returned to the caller. -->
类的构造函数也可以标记为`偏函数`。如果在类的构造函数中触发异常，则认为构造失败，正在构造的对象被丢弃，而且不会返回给调用者。

<!-- When an actor constructor is called the actor is created and a reference to it is returned immediately. However, the constructor code is executed asynchronously at some later time. If an actor constructor were to raise an error it would already be too late to report this to the caller. For this reason, constructors for actors may not be partial. -->
当调用actor构造函数时，将创建actor并立即返回对它的引用。但是，actor的构造函数代码将在稍后的某个时间异步执行。如果在actor构造函数内触发一个异常，那么向调用者报告这个异常就晚了。因此，actor的构造函数不能被设置为`偏函数`。

<!-- Behaviours are also executed asynchronously and so cannot be partial for the same reason. -->
行为也是异步执行的，因此`行为`也不被标记为`偏函数`。

<!-- ## Try-then blocks -->
## Try-then表达式

<!-- In addition to an `else` error handler, a `try` command can have a `then` block. This is executed after the rest of the `try`, whether or not an error is raised or handled. Expanding our example from earlier: -->
除了`else`表达式外，`try`还可以使用`then`表达式。它会在`try`和`else`之后执行，不管是否触发异常一定会被执行。我们对前面的示例做一下扩展:

```pony
try
  callA()
  if not callB() then error end
  callC()
else
  callD()
then
  callE()
end
```

<!-- The `callE()` will always be executed. If `callB()` returns true then the sequence executed is `callA()`, `callB()`, `callC()`, `callE()`. If `callB()` returns false then the sequence executed is `callA()`, `callB()`, `callD()`, `callE()`. -->
`callE()`始终会被执行。如果`callB()`返回`true`，那么执行的序列是`callA()`， `callB()`， `callC()`， `callE()`。如果`callB()`返回`false`，那么执行的序列是`callA()`， `callB()`， `callD()`， `callE()`。

<!-- __Do I have to have an else error handler to have a then block?__ No. You can have a `try`-`then` block without an `else` if you like. -->
__必须在使用了`else`处理程序后,才能用`then`表达式吗?__ 不需要。如果你喜欢的话，你可以直接使用`try`-`then`,省略掉`else`。

<!-- __Will my then block really always be executed, even if I return inside the try?__ Yes, your `then` expression will __always__ be executed when the `try` block is complete. The only way it won't be is if the `try` never completes (due to an infinite loop), the machine is powered off, or the process is killed (and then, maybe). -->
__`then`代码块真的总是被执行吗，如果在try里面直接`retuan`呢?__ 放心，当`try`表达式无论以何种方式完成时，你的`then`表达式一定会被执行。唯一的例外情况的是，如果`try`永远无法完成(陷入一个死循环)，电脑被人拔掉电源，或者进程被终止(那你的then就有可能不会被执行...)。

<!-- ## With blocks -->
## With表达式

<!-- A `with` expression can be used to ensure disposal of an object when it is no longer needed. A common case is a database connection which needs to be closed after use to avoid resource leaks on the server. For example: -->
`with`表达式可用于对象不再需要时,执行一些处理操作。常见的情况是数据库连接在使用后需要关闭，以避免服务器上的资源浪费。例如:

```pony
with obj = SomeObjectThatNeedsDisposing() do
  // use obj
end
```

<!-- `obj.dispose()` will be called whether the code inside the `with` block completes successfully or raises an error. To take part in a `with` expression, the object that needs resource clean-up must, therefore, provide a `dispose()` method: -->
`obj.dispose()`会自动在`with`表达式内的代码成功完成或触发异常时被调用。一个对象要使用`with`表达式，需要必须提供一个`dispose()`方法:

```pony
class SomeObjectThatNeedsDisposing
  // constructor, other functions

  fun dispose() =>
    // release resources
```

<!-- It is possible to provide an `else` clause, which is called only in error cases: -->
可以`with`提供`else`选项，它只在触发了异常的情况下被调用:

```pony
with obj = SomeObjectThatNeedsDisposing() do
  // use obj
else
  // only run if an error has occurred
end
```

<!-- Multiple objects can be set up for disposal: -->
`with`可以一次设置多个对象进行处理:

```pony
with obj = SomeObjectThatNeedsDisposing(), other = SomeOtherDisposableObject() do
  // use obj
end
```

<!-- The value of a `with` expression is the value of the last expression in the block, or of the last expression in the `else` block if there is one and an error occurred. -->
`with`的返回值是代码块中的最后一个表达式的值，或者是`else`代码块中的最后一个表达式的值(如果有一个表达式并且发生了异常)。

<!-- ## Language constructs that can raise errors -->
## 触发异常的关键字

<!-- The only language construct that can raise an error, other than the `error` command or calling a partial method, is the `as` command. This converts the given value to the specified type if it can be. If it can't then an error is raised. This means that the `as` command can only be used inside a try block or a partial method. -->
除了`error`表达式或调用`偏函数`外，触发异常还可以使用`as`表达式。比如，将给定的值转换为指定的类型,如果不能转换，就会触发异常。所以`as`命令只能在try代码块或`偏函数`中使用。

<!-- ## Comparison to exceptions in other languages -->
## 与其他语言中的异常处理进行比较

<!-- Pony errors behave very much the same as those in C++, Java, C#, Python, and Ruby. The key difference is that Pony errors do not have a type or instance associated with them. This makes them the same as C++ exceptions would be if a fixed literal was always thrown, e.g. `throw 3;`. This difference simplifies error handling for the programmer and allows for much better runtime error handling performance. -->
Pony的异常处理与C++、Java、C#、Python和Ruby中的异常处理非常类似。主要的区别在于，Pony的异常不需要与类型或实例相关联。这就像抛出固定错误号的C++异常一样。`throw 3;`。这种方式简化了程序员的异常处理逻辑，也可以拥有更好的运行时性能。

<!-- The `else` handler in a `try` expression is just like a `catch(...)` in C++, `catch(Exception e)` in Java or C#, `except:` in Python, or `rescue` in Ruby. Since exceptions do not have types there is no need for handlers to specify types or to have multiple handlers in a single try block. -->
`try`表达式中的`else`处理程序就像C++中的`catch(...)`，Java或C#中的`catch(Exception e)`， Python中的`except:`或Ruby中的`rescue`。因为异常没有类型，所以处理程序不需要指定类型，也就不需要一个try代码块中对应多个异常处理程序。

<!-- The `then` block in a `try` expression is just like a `finally` in Java, C#, or Python and `ensure` in Ruby. -->
`try`表达式中的`then`代码块就像Java、C#或Python中的`finally`和Ruby中的`ensure`一样。

<!-- If required, error handlers can "reraise" by using the `error` command within the handler. -->
如果需要，异常处理逻辑可以通过使用`error`表达式`再次执行`。
