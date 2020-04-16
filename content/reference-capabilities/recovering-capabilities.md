---
title: "权能借用（Recovering Capabilities）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 50
toc: true
---

<!-- A `recover` expression lets you "lift" the reference capability of the result. A mutable reference capability (`iso`, `trn`, or `ref`) can become _any_ reference capability, and an immutable reference capability (`val` or `box`) can become any immutable or opaque reference capability. -->
`借用（recover）`表达式允许你`提升（lift）`结果的引用权能。可变引用权能(`iso`、`trn`或`ref`)可以成为_any_引用权能，而不可变引用权能(`val`或`box`)可以成为任何不可变或不透明的引用权能。

## Why is this useful?

<!-- This most straightforward use of `recover` is to get an `iso` that you can pass to another actor. But it can be used for many other things as well, such as: -->
`借用（recover）`最直接的用法是获得一个可以传递给另一个参与者的`iso`。但是它也可以用在很多其他的事情上，比如:

<!-- * Creating a cyclic immutable data structure. That is, you can create a complex mutable data structure inside a `recover` expression, "lift" the resulting `ref` to a `val`. -->
<!-- * "Borrow" an `iso` as a `ref`, do a series of complex mutable operations on it, and return it as an `iso` again. -->
<!-- * "Extract" a mutable field from an `iso` and return it as an `iso`. -->
*创建循环的不可变数据结构。也就是说，你可以在`借用（recover）`表达式中创建一个复杂的可变数据结构，将结果`ref`提升为`val`。
*`借（Borrow）`一个`iso`作为`参考`，对它进行一系列复杂的可变操作，然后再次以`iso`的形式返回。
*从`iso`中`提取（Extract）`一个可变字段，并将其作为`iso`返回。

## What does this look like?

<!-- The `recover` expression wraps a list of expressions and is terminated by an `end`, like this: -->
`借用（recover）`表达式包装了一个表达式列表，并以`end`结尾，就像这样:

```pony
recover Array[String].create() end
```

<!-- This expression returns an `Array[String] iso`, instead of the usual `Array[String] ref` you would get. The reason it is `iso` and not any of the other mutable reference capabilities is because there is a default reference capability when you don't specify one. The default for any mutable reference capability is `iso` and the default for any immutable reference capability is `val`. -->
借用表达式返回一个`Array[String] iso`，而不是`Array[String] ref`。它是`iso`而不是其他可变引用权能的原因是，当你不指定一个时，会有一个默认的引用权能。任何可变引用权能的默认值是`iso`，而任何不可变引用权能的默认值是`val`。

<!-- Here's a more complicated example from the standard library: -->
下面是一个来自标准库的更复杂的例子:

```pony
recover
  var s = String((prec + 1).max(width.max(31)))
  var value = x

  try
    if value == 0 then
      s.push(table(0)?)
    else
      while value != 0 do
        let index = ((value = value / base) - (value * base))
        s.push(table(index.usize())?)
      end
    end
  end

  _extend_digits(s, prec')
  s.append(typestring)
  s.append(prestring)
  _pad(s, width, align, fill)
  s
end
```

<!-- That's from `format/_FormatInt`. It creates a `String ref`, does a bunch of stuff with it, and finally returns it as a `String iso`. -->
从`format/ _FormatInt`。它创建了一个`String ref`，用它做了很多事情，最后返回一个`String iso`类型。

<!-- You can also give an explicit reference capability: -->
你也可以给出一个明确的引用权能:

```pony
let key = recover val line.substring(0, i).>strip() end
```

<!-- That's from `net/http/_PayloadBuilder`. We get a substring of `line`, which is a `String iso^`, then we call strip on it, which returns itself. But since strip is a `ref` function, it returns itself as a `String ref^` - so we use a `recover val` to end up with a `String val`. -->
这是来自'net/http/_PayloadBuilder`。我们得到一个`line`的子字符串，它是`String iso^`然后我们调用strip，它会返回自身。但是因为strip是一个`ref`函数，所以它以`String ref^`的形式返回自己——所以我们用`recover val`来结束一个`String val`。

## How does this work?

<!-- Inside the `recover` expression, your code only has access to __sendable__ values from the enclosing lexical scope. In other words, you can only use `iso`, `val` and `tag` things from outside the `recover` expression. -->
在`借用（recover）`表达式中，你的代码只能从封闭的词法作用域访问。换句话说，你只能使用`iso`、`val`和`tag`来自`借用（recover）`表达式外部的内容。

<!-- This means that when the `recover` expression finishes, any aliases to the result of the expression other than `iso`, `val` and `tag` ones won't exist anymore. That makes it safe to "lift" the reference capability of the result of the expression. -->
这意味着当`借用（recover）`表达式结束时，除了`iso`、`val`和`tag`之外，表达式结果的任何别名都将不复存在。这使得`提升`表达式结果的引用权能变得安全。

<!-- If the `recover` expression could access __non-sendable__ values from the enclosing lexical scope, "lifting" the reference capability of the result wouldn't be safe. Some of those values could "leak" into an `iso` or `val` result, and result in data races. -->
如果`借用（recover）`表达式可以从封闭的词法范围中访问非sendable__值，那么`提升`结果的引用权能就不安全了。其中一些值可能会`泄漏`到`iso`或`val`结果中，导致数据竞争。

## Automatic receiver recovery

<!-- When you have an `iso` or `trn` receiver, you normally can't call `ref` methods on it. That's because the receiver is also an argument to a method, which means both the method body and the caller have access to the receiver at the same time. And _that_ means we have to alias the receiver when we call a method on it. The alias of an `iso` is a `tag` (which isn't a subtype of `ref`) and the alias of a `trn` is a `box` (also not a subtype of `ref`). -->
当你有一个`iso`或`trn`接收器，你通常不能调用`ref`方法。这是因为接收方也是方法的一个参数，这意味着方法主体和调用方可以同时访问接收方。_that意味着当我们调用一个方法时，我们必须别名化接收者。`iso`的别名是`tag`(不是`ref`的子类型)，`trn`的别名是`box`(也不是`ref`的子类型)。

<!-- But we can get around this! If all the arguments to the method (other than the receiver, which is the implicit argument being recovered) _at the call-site_ are __sendable__, and the return type of the method is either __sendable__ or isn't used _at the call-site_, then we can "automatically recover" the receiver. That just means we don't have to alias the receiver - and _that_ means we can call `ref` methods on an `iso` or `trn`, since `iso` and `trn` are both subtypes of `ref`. -->
但我们可以避开这个!如果方法的所有参数(接收方除外，接收方是正在被借用（recover）的隐式参数)_at call-site_都是_sendable__，并且方法的返回类型要么是_sendable__，要么在call-site_没有被使用，那么我们可以`自动借用（recover）`接收方。这只是意味着我们不需要别名接收器-这意味着我们可以调用`ref`方法的`iso`或`trn`，因为`iso`和`trn`都是`ref`的子类型。

<!-- Notice that this technique looks mostly at the call-site, rather than at the definition of the method being called. That makes it more flexible. For example, if the method being called wants a `ref` argument, and we pass it an `iso` argument, that's __sendable__ at the call-site, so we can still do automatic receiver recovery. -->
注意，这种技术主要关注调用站点，而不是被调用方法的定义。这使得它更加灵活。例如，如果被调用的方法需要一个`ref`参数，而我们给它传递了一个`iso`参数，那么在调用站点上它是_sendable__，因此我们仍然可以执行自动接收方借用（recover）。

<!-- This may sound a little complicated, but in practice, it means you can write code that treats an `iso` mostly like a `ref`, and the compiler will complain when it's wrong. For example: -->
这听起来可能有点复杂，但在实践中，这意味着你可以编写将`iso`主要视为`ref`的代码，当它出错时，编译器将发出抱怨。例如:

```pony
let s = recover String end
s.append("hi")
```

<!-- Here, we create a `String iso` and then append some text to it. The append method takes a `ref` receiver and a `box` parameter. We can automatically recover the `iso` receiver since we pass a `val` parameter, so everything is fine. -->
在这里，我们创建一个`String iso`，然后附加一些文本到它。append方法接受一个`ref`接收器和一个`box`参数。我们可以自动借用（recover）`iso`接收器，因为我们传递了一个`val`参数，所以一切都很好。
