---
title: "权能包含关系（Capability Subtyping）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 80
toc: true
---

<!-- Subtyping is about _substitutability_. That is, if we need to supply a certain type, what other types can we substitute instead? Reference capabilities factor into this. -->
子类型是关于_替换性的。也就是说，如果我们需要提供某一种类型，我们还可以替代什么类型?其中包括引用权能。

## Simple substitution

<!-- First, let's cover substitution without worrying about ephemeral types (`^`) or alias types (`!`). The `<:` symbol means "is a subtype of" or alternatively "can be substituted for". -->
首先，让我们讨论替换，而不必担心临时类型(`^`)或别名类型(`!`)。符号`<:`表示`是`的子类型，或者`可以被替换`。

<!-- * `iso <: trn`. An `iso` is _read and write unique_, and a `trn` is just _write unique_, so it's safe to substitute an `iso` for a `trn`. -->
<!-- * `trn <: ref`. A `trn` is mutable and also _write unique_. A `ref` is mutable but makes no uniqueness guarantees. It's safe to substitute a `trn` for a `ref`. -->
<!-- * `trn <: val`. This one is interesting. A `trn` is _write unique_ and a `val` is _globally immutable_, so why is it safe to substitute a `trn` for a `val`? The key is that, in order to do so, you have to _give up_ the `trn` you have. If you give up the _only_ variable that can write to an object, you know that no variable can write to it. That means it's safe to consider it _globally immutable_. -->
<!-- * `ref <: box`. A `ref` guarantees no _other_ actor can read from or write to the object. A `box` just guarantees no _other_ actor can write to the object, so it's safe to substitute a `ref` for a `box`. -->
<!-- * `val <: box`. A `val` guarantees _no_ actor, not even this one, can write to the object. A `box` just guarantees no _other_ actor can write to the object, so it's safe to substitute a `val` for a `box`. -->
<!-- * `box <: tag`. A `box` guarantees no other actor can write to the object, and a `tag` makes no guarantees at all, so it's safe to substitute a `box` for a `tag`. -->
* `iso <: trn`。`iso`是_read和write unique_，` trn`是_write unique_，所以用`iso`代替`trn`是安全的。
* `trn <: ref`。一个`trn`是可变的，也_write unique_。`ref`是可变的，但不能保证唯一性。用`trn`代替`ref`是安全的。
* `trn <: val`。这个很有趣。`trn`是_write unique_，` val`是全局不可变的，那么为什么用`trn`代替`val`是安全的呢?关键是，为了做到这一点，你必须放弃你所拥有的一切。如果你放弃了_only_变量可以写一个对象，你知道没有变量可以写它。这意味着它是全局不变的。
* `ref <: box`。`ref`保证没有_other_actor可以读写对象。`box`只是保证没有_other_ actor可以写对象，所以用`ref`代替`box`是安全的。
* `val <: box`。一个`val`保证_no_actor，即使是这个，也不能写对象。一个`box`只是保证没有_other_ actor可以写对象，所以用`val`代替`box`是安全的。
* `box <: tag`。`box`保证没有其他参与者可以写入对象，而`tag`则完全没有保证，所以用`box`替换`tag`是安全的。

<!-- Subtyping is _transitive_. That means that since `iso <: trn` and `trn <: ref` and `ref <: box`, we also get `iso <: box`. -->
子类型化_transitive_。这意味着，由于`iso <: trn`和`trn <: ref`和`ref <: box`，我们也得到`iso <: box`。

## Aliased substitution

<!-- Now let's consider what happens when we have an alias of a reference capability. For example, if we have some `iso` and we alias it (without doing a `consume` or a destructive read), the type we get is `iso!`, not `iso`. -->
现在让我们考虑一下当我们有一个引用权能的别名时会发生什么。例如，如果我们有一些`iso`，并对其进行别名处理(不执行`消费`或破坏性读取)，则得到的类型是`iso!`,而不是`iso`。

<!-- * `iso! <: tag`. This is a pretty big change. Instead of being a subtype of everything like `iso`, the only thing an `iso!` is a subtype of is `tag`. This is because the `iso` still exists, and is still _read and write unique_. Any alias can neither read from nor write to the object. That means an `iso!` can only be a subtype of `tag`. -->
<!-- * `trn! <: box`. This is a change too, but not as big a change. Since `trn` is only _write unique_, it's ok for aliases to read from the object, but it's not ok for aliases to write to the object. That means we could have `box` or `val` aliases - except `val` guarantees that _no_ alias can write to the object! Since our `trn` still exists and can write to the object, a `val` alias would break the guarantees that `val` makes. So a `trn!` can only be a subtype of `box` (and, transitively, `tag` as well). -->
<!-- * `ref! <: ref`. Since a `ref` only guarantees that _other_ actors can neither read from nor write to the object, it's ok to make more `ref` aliases within the same actor. -->
<!-- * `val! <: val`. Since a `val` only guarantees that _no_ actor can write to the object, its ok to make more `val` aliases since they can't write to the object either. -->
<!-- * `box! <: box`. A `box` only guarantees that _other_ actors can't write to the object. Both `val` and `ref` make that guarantee too, so why can `box` only alias as `box`? It's because we can't make _more_ guarantees when we alias something. That means `box` can only alias as `box`. -->
<!-- * `tag! <: tag`. A `tag` doesn't make any guarantees at all. Just like with a `box`, we can't make more guarantees when we make a new alias, so a `tag` can only alias as a `tag`. -->
*`iso !<:tag`。这是一个相当大的变化。而不是像`iso`这样的所有东西的子类型，唯一的东西是`iso!`是tag的子类型。这是因为`iso`仍然存在，并且仍然是_read和write unique_。任何别名既不能从对象中读取，也不能从对象中写入。这意味着`iso!`'只能是`tag`的子类型。
*`trn!<:box`。这也是一个变化，但并不是很大的变化。因为`trn`只是_write unique_，所以别名可以从对象中读取，但是别名不能写入对象。这意味着我们可以有`box`或`val`别名-除了`val`保证_no_ alias可以写入对象!因为我们的`trn`仍然存在，并可以写入对象，`val`别名将打破`val`作出的保证。所以一个`ref`!只能是`box`的子类型(及及物动词`tag`)。
*`ref!<:ref`。由于`ref`只保证_other_ actor既不能从对象中读取也不能从对象中写入，所以可以在同一个actor中创建更多`ref`别名。
*`val!<:val`。因为`val`只能保证_no_ actor可以写入对象，所以可以使用更多的`val`别名，因为它们也不能写入对象。
*`box!<:box`。一个`框`只能保证_other_ actor不能写对象。`val`和`ref`都做了保证，那么为什么`box`只能别名为`box`呢?这是因为我们不能做更多的保证当我们别名的东西。这意味着`box`只能别名为`box`。
*`tag!<:tag`。一个`tag`根本不能保证什么。就像`框`一样，我们不能在创建新别名时做更多的保证，因此`tag`只能作为`tag`的别名。

## Ephemeral substitution

<!-- The last case to consider is when we have an ephemeral reference capability. For example, if we have some `iso` and we `consume` it or do a destructive read, the type we get is `iso^`, not `iso`. -->
最后要考虑的情况是当我们有临时引用权能时。例如，如果我们有一些`iso`，我们'消费'它或做一个破坏性的阅读，我们得到的类型是`iso^`，而不是`iso`。

<!-- * `iso^ <: iso`. This is pretty simple. When we give an `iso^` a name, by assigning it to something or passing it as an argument to a method, it loses the `^` and becomes a plain old `iso`. We know we gave up our previous `iso`, so it's safe to have a new one. -->
<!-- * `trn^ <: trn`. This works exactly like `iso^`. The guarantee is weaker (_write uniqueness_ instead of _read and write uniqueness_), but it works the same way. -->
<!-- * `ref^ <: ref^` and `ref^ <: ref` and `ref <: ref^`. Here, we have another case. Not only is a `ref^` a subtype of a `ref`, it's also a subtype of a `ref^`. What's going on here? The reason is that an ephemeral reference capability is a way of saying "a reference capability that, when aliased, results in the base reference capability". Since a `ref` can be aliased as a `ref`, that means `ref` and `ref^` are completely interchangeable. -->
<!-- * `val^`, `box^`, `tag^`. These all work the same way as `ref`, that is, they are interchangeable with the base reference capability. It's for the same reason: all of these reference capabilities can be aliased as themselves. -->
* `iso^ <: iso`。这很简单。当我们给一个`iso^`一个名字时，通过把它赋值给某个东西或者把它作为参数传递给某个方法，它就会失去`^`而变成一个普通的旧的`iso`。我们知道我们已经放弃了之前的`iso`，所以换一个新的是安全的。
* `trn^ <: trn`。这和`iso^` 完全一样。保证是较弱的(_write uniqueness_而不是_read和write uniquess_)，但它的工作方式是一样的。
* `ref^ <: ref` 和`ref ^ <: ref`和`ref <: ref ^。这里，我们有另一个例子。a` ref`不仅是a` ref`的子类型，它也是a` `ref`的子类型。这是怎么回事?原因是临时引用权能是`在别名时产生基本引用权能的引用权能`的一种说法。由于`ref`可以别名为`ref`，这意味着`ref`和`ref^`是完全可以互换的。
* ` val^`，` box^`，` tag^`。它们的工作方式都与`ref`相同，即可以与基本引用权能互换。原因是一样的:所有这些引用权能都可以作为它们自己的别名。

<!-- __Why do `ref^`, `val^`, `box^`, and `tag^` exist if they are interchangeable with their base reference capabilities?__ It's for two reasons: __reference capability recovery__ and __generics__. We'll cover both of those later. -->
如果`ref^`、`val^`、`box^`和`tag^`可以与它们的基本引用权能互换，那么为什么还存在`ref^`、`val^`、`box^`和`tag^`呢?有两个原因:引用权能恢复__和一般__。这两个我们后面都会讲到。
