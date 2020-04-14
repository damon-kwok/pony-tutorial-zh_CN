---
title: "流程控制（Control Structures）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 50
toc: true
---

<!-- To do real work in a program you have to be able to make decisions, iterate through collections of items and perform actions repeatedly. For this, you need control structures. Pony has control structures that will be familiar to programmers who have used most languages, such as `if`, `while` and `for`, but in Pony, they work slightly differently. -->
在程序开发过程中，你需要能够对条件做出判断，对集合进行遍历，或者重复执行一些逻辑。因此，流程控制必不可少。Pony具有你在其他语言中所熟悉的流程控制控制，例如`if`，`while`和`for`，但是在Pony中，它们的工作方式略有不同。

<!-- ## Conditionals -->
## 条件判断（Conditionals）

<!-- The simplest control structure is the good old `if`. It allows you to perform some action only when a condition is true. In Pony it looks like this: -->
`if`是最简单的流程控制控制。它仅在条件为真时才允许您执行某些操作。在Pony中的用法：

```pony
if a > b then
  env.out.print("a is bigger")
end
```

<!-- __Can I use integers and pointers for the condition like I can in C?__ No. In Pony `if` conditions must have type Bool, i.e. they are always true or false. If you want to test whether a number `a` is not 0, then you need to explicitly say `a != 0`. This restriction removes a whole category of potential bugs from Pony programs. -->
__可以像在C语言中那样使用整数和指针作为条件吗？__ 不行。在Pony中，`if`条件必须是布尔类型，即条件的返回值值始终为true或false。如果要判断数字`a`是否不为0，则需要明确的使用`a！= 0`。这个限制让Pony程序避免了潜在的类型错误。

<!-- If you want some alternative code for when the condition fails just add an `else`: -->
需要在条件失败时的执行一些逻辑，只需添加一个`else`：

```pony
if a > b then
  env.out.print("a is bigger")
else
  env.out.print("a is not bigger")
end
```

<!-- Often you want to test more than one condition in one go, giving you more than two possible outcomes. You can nest `if` statements, but this quickly gets ugly: -->
通常，您想要检测和处理多种条件，可以嵌套`if`语句，但这有点丑陋：

```pony
if a == b then
  env.out.print("they are the same")
else
  if a > b then
    env.out.print("a is bigger")
  else
    env.out.print("b bigger")
  end
end
```

<!-- As an alternative Pony provides the `elseif` keyword that combines an `else` and an `if`. This works the same as saying `else if` in other languages and you can have as many `elseif`s as you like for each `if`. -->
作为替代方案，Pony提供了结合了`else`和`if`的`elseif`关键字。这与其他语言中`else if`相同，并且每个`if`可以有任意数量的`elseif`：

```pony
if a == b then
  env.out.print("they are the same")
elseif a > b then
  env.out.print("a is bigger")
else
  env.out.print("b bigger")
end
```

<!-- __Why can't I just say "else if" like I do in C? Why the extra keyword?__ The relationship between `if` and `else` in C, and other similar languages, is ambiguous. For example: -->
__为什么不能像在C语言中那样用`else if`？为什么要用新的关键字？__ C和其他类似语言中的`if`和`else`之间的关系是模棱两可的。例如：

```C
// C code
if(a)
  if(b)
    printf("a and b\n");
else
  printf("not a\n");
```
<!-- Here it is not obvious whether the `else` is an alternative to the first or the second `if`. In fact here the `else` relates to the `if(b)` so our example contains a bug. Pony avoids this type of bug by handling `if` and `else` differently and the need for `elseif` comes out of that. -->
上面的示例中，`else`到底归属于第一个还是第二个`if`有点让人困惑。实际上，`else`归属与`if(b)`，显然示例里面`else`语句本意是想归属于`if(a)`。Pony通过以不同的方式处理`if`和`else`来避免此类错误，那就是`elseif`。

<!-- ## Everything is an expression -->
## 一切皆表达式（Everything is an expression）

<!-- The big difference for control structures between Pony and other languages is that in Pony everything is an expression. In languages like C++ and Java `if` is a statement, not an expression. This means that you can't have an `if` inside an expression, there has to be a separate conditional operator '?'. -->
Pony的流程控制与其他语言的最大区别在于，在Pony中，一切都是表达式。在C++和Java这样的语言中，`if`是语句，而不是表达式。这意味着您不能在表达式中包含`if`，而必须有一个单独的条件运算符`？`（三目运算符 `a ? b : c`）。

<!-- In Pony there are no statements there are only expressions, everything hands back a value. Your `if` statement hands you back a value. Your `for` loop (which we'll get to a bit later) hands you back a value. -->
在Pony中，不存在语句的概念，只有表达式，所有东西都有返回一个值。if表达式是一个求值过程。`for`循环表达式也（稍后再讨论）也是如此。

<!-- This means you can use `if` directly in a calculation: -->
这意味着您可以在计算中直接使用`if`：

```pony
x = 1 + if lots then 100 else 2 end
```

<!-- This will give __x__ a value of either 3 or 101, depending on the variable __lots__. -->
通过变量 __lots__ ，为 __x__ 赋值为3或101。

<!-- If the `then` and `else` branches of an `if` produce different types then the `if` produces a __union__ of the two. -->
如果`if`的`then`和`else`分支产生不同的类型，则`if`返回两者的 __联合类型（union）__ 。

```pony
var x: (String | Bool) =
  if friendly then
    "Hello"
  else
    false
  end
```

<!-- __But what if my if doesn't have an else?__ Any `else` branch that doesn't exist gives an implicit `None`. -->
__但是`if`表达式没有`else`呢？__ 任何不存在的`else`分支都会默认返回`None`（None是基元类的实例）。

```pony
var x: (String | None) =
  if friendly then
    "Hello"
  end
```

<!-- The same rules that apply to the value of an `if` expression applies to loops as well. Let's take a look at what a loop value would look like: -->
`if`表达式的规则也适用于循环。我们看一下Pony的循环是什么样的：

```pony
actor Main
  new create(env: Env) =>
    var x: (String | None) =
      for name in ["Bob"; "Fred"; "Sarah"].values() do
        name
      end
    match x
    | let s: String => env.out.print("x is " + s)
    | None => env.out.print("x is None")
    end
```

<!-- This will give __x__ the value "Sarah" as it is the last name in our list. If our loop has 0 iterations, then the value of it's `else` block will be the value of __x__. Or if there is no `else` block, the value will be `None`. -->
__x__ 会被赋值为`Sarah`，它是循环列表中的最后一个元素。如果循环直接跳出了（0次迭代），那么__x__ 的值就是`else`表达式的返回值。如果没有`else`块，则该值为`None`。

```pony
actor Main
  new create(env: Env) =>
    var x: (String | None) =
      for name in Array[String].values() do
        name
      else
        "no names!"
      end
    match x
    | let s: String => env.out.print("x is " + s)
    | None => env.out.print("x is None")
    end
```

<!-- Here the value of __x__ is "no names!" -->
___x__ 的值在这里是"no names!"。

```pony
actor Main
  new create(env: Env) =>
    var x: (String | None) =
      for name in Array[String].values() do
        name
      end
    match x
    | let s: String => env.out.print("x is " + s)
    | None => env.out.print("x is None")
    end
```

<!-- And lastly, here __x__ would be `None`. -->
最后，__x__ 的值是`None`。

<!-- ## Loops -->
## Loops表达式（Loops）

<!-- `if` allows you to choose what to do, but to do something more than once you want a loop. -->
`if`允许您选择要执行的操作，但是不止一次想要循环就可以执行其他操作。

<!-- ### While -->
### While表达式（While）

<!-- Pony `while` loops are very similar to those in other languages. A condition expression is evaluated and if it's true we execute the code inside the loop. When we're done we evaluate the condition again and keep going until it's false. -->
Pony的while循环与其他语言非常相似。条件表达式将被求值，如果为真，将执行一次循环内的代码，完成后，我们将再次评估条件，并继续进行直到条件不成立为止。 

<!-- Here's an example that prints out the numbers 1 to 10: -->
这是一个打印数字1到10的示例：

```pony
var count: U32 = 1

while count <= 10 do
  env.out.print(count.string())
  count = count + 1
end
```

<!-- Just like `if` expressions `while` is also an expression. The value returned is just the value of the expression inside the loop the last time we go round it. For this example that will be the value given by `count = count + 1` when count is incremented to 11. Since Pony assignments hand back the _old_ value our `while` loop will return 10. -->
就像if表达式，while也是表达式一样。返回值是最后循环执行时的表达式返回值。在这个例子中，当count递增到11时，也就是`count = count + 1`的返回值值。Pony会将最后一次正确执行循环的 _旧值（old）_ 返回，最终`while`循环将返回10。

<!-- __But what if the condition evaluates to false the first time we try, then we don't go round the loop at all?__ In Pony `while` expressions can also have an `else` block. In general, Pony `else` blocks provide a value when the expression they are attached to doesn't. A `while` doesn't have a value to give if the condition evaluates to false the first time, so the `else` provides it instead. -->
__但是如果`while`在第一次判断就为假，那不久就不会执行循环吗？__ 在Pony中，`while`表达式也可以有一个`else`块，在没有提供`else`表达式时，Pony的`else`块会提供一个默认值`None`。注意：只有在第一次评估就为false时（也就是while循环体没机会执行时），才会返回`else`表达式的值。

<!-- __So is this like an else block on a while loop in Python?__ No, this is very different. In Python, the `else` is run when the `while` completes. In Pony the `else` is only run when the expression in the `while` isn't. -->
__所以这就像Python中`while`循环中的`else`块吗？__ 这是非常不同的。在Python中，`else`在`while`完成时运行。在Pony中，`else`仅在`while`没有机会运行时才执行。

<!-- ### Break -->
### 中止循环（Break）

<!-- Sometimes you want to stop part-way through a loop and give up altogether. Pony has the `break` keyword for this and it is very similar to its counterpart in languages like C++, C#, and Python. -->
有时，您希望在循环中途停止并跳出。 Pony为此使用了关键字`break`，它与C ++，C＃和Python等语言中的关键字非常相似。

<!-- `break` immediately exits from the innermost loop it's in. Since the loop has to return a value `break` can take an expression. This is optional and if it's missed out the value from the `else` block is returned. -->
break会立即从它所在的最里面的循环中退出。由于循环必须返回一个值，break可以采用一个表达式。这是可选的，如果错过了，则返回`else`块中的值。

<!-- Let's have an example. Suppose you want to go through a list of names you're getting from somewhere, looking for either "Jack" or "Jill". If neither of those appear you'll just take the last name you're given and if you're not given any names at all you'll use "Herbert". -->
让我们举个例子。假设您要浏览从某处获取的名称列表，查找`Jack`或`Jill`。如果这两个都没有出现，则只使用您的姓氏，如果根本不使用任何名字，则使用`Herbert`。

```pony
var name =
  while moreNames() do
    var name' = getName()
    if name' == "Jack" or name' == "Jill" then
      break name'
    end
    name'
  else
    "Herbert"
  end
```

<!-- So first we ask if there are any more names to get. If there are then we get a name and see if it's "Jack" or "Jill". If it is we're done and we break out of the loop, handing back the name we've found. If not we try again. -->
因此，首先我们要询问是否还有其他名字。如果有的话，我们得到这个名字，看看它是`Jack`还是`Jill`，如果符合条件，将跳出循环，将找到的名字递回。如果没有，我们再试一次。

<!-- The line `name'` appears at the end of the loop so that will be our value returned from the last iteration if neither "Jack" nor "Jill" is found. -->
`name`行出现在循环的末尾，因此如果找不到`Jack`或`Jill`，返回值最后一次迭代的名字。

<!-- The `else` block provides our value of "Herbert" if there are no names available at all. -->
如果一开始就发现根本没有可用名，则返回`else`块提供的`Herbert`值。

<!-- __Can I break out of multiple, nested loops like the Java labeled break?__ No, Pony does not support that. If you need to break out of multiple loops you should probably refactor your code or use a worker function. -->
__可以从多层嵌套的循环中直接跳出吗？就像Java的break那样__ 没有，Pony不支持。这是糟糕的逻辑表达方式，当你遇到需要从多层嵌套循环中直接跳出的情况，你应该重构你的代码或使用辅助函数。

<!-- ### Continue -->
### Continue表达式

<!-- Sometimes you want to stop part-way through one loop iteration and move onto the next. Like other languages, Pony uses the `continue` keyword for this. -->
有时，您希望在一次循环迭代中中途停止，然后进行下一次循环。像其他语言一样，Pony为此使用了`continue`关键字。

<!-- `continue` stops executing the current iteration of the innermost loop it's in and evaluates the condition ready for the next iteration. -->
`continue`将停止执行它所在的最内层循环的当前迭代，并评估条件以准备进行下一次迭代。

<!-- If `continue` is executed during the last iteration of the loop then we have no value to return from the loop. In this case, we use the loop's `else` expression to get a value. As with the `if` expression if no `else` expression is provided `None` is returned. -->
如果在循环的最后一次迭代中`continue`被触发了，那么我们循环就不会有返回值。在这种情况下，将返回`else`表达式的值。与`if`表达式一样，如果未提供`else`表达式，则返回`None`。

<!-- __Can I continue an outer, nested loop like the Java labeled continue?__ No, Pony does not support that. If you need to continue an outer loop you should probably refactor your code. -->
__Pony的`continue`可以直接跳到一个外部嵌套循环中继续执行吗？就像Java的`continue`。__ 不行，Pony不支持。如果需要跳转到外层循环继续执行，说明你的逻辑应该重构了。

<!-- ### For -->
### For表达式

<!-- For iterating over a collection of items Pony uses the `for` keyword. This is very similar to `foreach` in C#, `for`..`in` in Python and `for` in Java when used with a collection. It is very different to `for` in C and C++. -->
在Pony中需要便利集合，可以使用`for`表达式。当与集合一起使用时，这非常类似于C＃中的`foreach`，Python中的`for..in`和Java中的`for`。它与C和C++中的`for`非常不同。

<!-- The Pony `for` loop iterates over a collection of items using an iterator. On each iteration, round the loop, we ask the iterator if there are any more elements to process and if there are we ask it for the next one. -->
Pony的`for`循环使用迭代器遍历集合。在每次迭代中，都会询问迭代器是否还有要处理的元素。

<!-- For example to print out all the strings in an array: -->
例如，打印出数组中的所有字符串：

```pony
for name in ["Bob"; "Fred"; "Sarah"].values() do
  env.out.print(name)
end
```

<!-- Note the call to `values()` on the array, this is because the loop needs an iterator, not an array. -->
注意在数组上调用`values()`，是因为循环需一个迭代器。

<!-- The iterator does not have to be of any particular type, but needs to provide the following methods: -->
迭代器没有特定的类型需求，但需要提供以下接口方法：
```pony
  fun has_next(): Bool
  fun next(): T?
```

<!-- where T is the type of the objects in the collection. You don't need to worry about this unless you're writing your own iterators. To use existing collections, such as those provided in the standard library, you can just use `for` and it will all work. If you do write your own iterators note that we use structural typing, so your iterator doesn't need to declare that it provides any particular type. -->
其中`T`是集合中对象的类型。除非您需要编写自己的迭代器，否则无需担心。要使用现有的集合（例如标准库中提供的集合），您只需使用`for`即可。如果你编写了自己的迭代器，请使用`结构化多态`的方式满足`接口`需求，你的迭代器无需特定的类型声明。

<!-- You can think of the above example as being equivalent to: -->
您可以认为上述示例等效于：

```pony
let iterator = ["Bob"; "Fred"; "Sarah"].values()
while iterator.has_next() do
  let name = iterator.next()?
  env.out.print(name)
end
```

<!-- Note that the variable __name__ is declared _let_, you cannot assign to the control variable within the loop. -->
请注意，变量 __name__ 使用 _let_ 声明，所以不能在循环内为其重新赋值。

<!-- __Can I use break and continue with for loops?__ Yes, `for` loops can have `else` expressions attached and can use `break` and `continue` just as for `while`. -->
__我可以使用break并继续使用`for`循环吗？__ 可以的，`for`循环可以附加`else`表达式，并且可以像`while`一样使用`break`和`continue`。

<!-- ### Repeat -->
### 重复执行表达式（Repeat）

<!-- The final loop construct that Pony provides is `repeat` `until`. Here we evaluate the expression in the loop and then evaluate a condition expression to see if we're done or we should go round again. -->
Pony提供的最后一个循环表达式是`repeat``until`。在这里，我们评估循环中的表达式，然后评估条件表达式，看看是否完成或应该再次处理。

<!-- This is similar to `do` `while` in C++, C# and Java except that the termination condition is reversed, i.e. those languages terminate the loop when the condition expression is false, Pony terminates the loop when the condition expression is true. -->
这与C++，C＃和Java中的`do``while`类似，不同之处在于终止条件是相反的，即那些语言在条件表达式为false时终止循环，而Pony在条件表达式为true时终止循环。
<!-- The differences between `while` and `repeat` in Pony are: -->
Pony中的`while`和`repeat`的区别是：

<!-- 1. We always go around the loop at least once with `repeat`, whereas with `while` we may not go round at all. -->
<!-- 1. The termination condition is reversed. -->
1. `repeat`表达式至少会执行一次，而使用`while`则可能一次都不执行。 
2. 结束条件相反。

<!-- Suppose we're trying to create something and we want to keep trying until it's good enough: -->
加入我们正在尝试执行一些逻辑，并且想不断重复直到满足条件为止：

```pony
actor Main
  new create(env: Env) =>
    var counter = U64(1)
    repeat
      env.out.print("hello!")
      counter = counter + 1
    until counter > 7 end
```

<!-- Just like `while` loops the value given by a `repeat` loop is the value of the expression within the loop on the last iteration and `break` and `continue` can be used. -->
就像`while`循环一样，`repeat`循环返回的值就是上一次迭代时循环内表达式的值，并且可以使用`break`和`continue`。

<!-- __Since you always go round a repeat loop at least once do you ever need to give it an else expression?__ Yes, you may need to. A `continue` in the last iteration of a `repeat` loop needs to get a value from somewhere and an `else` expression is used for that. -->
__由于至少要执行一次重复循环，还有必要给它提供一个else表达式吗？__ 是的，您可能需要这样做。 如果`repeat`循环的最后一次迭代中触发了`continue`那就需要从某个地方获取一个值，这就需要使用`else`表达式。
