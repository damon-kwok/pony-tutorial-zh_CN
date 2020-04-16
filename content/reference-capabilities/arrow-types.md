---
title: "箭头的用法（Arrow Types aka Viewpoints）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 100
toc: true
---

<!-- When we talked about __reference capability composition__ and __viewpoint adaptation__, we dealt with cases where we know the reference capability of the origin. However, sometimes we don't know the precise reference capability of the origin. -->
当我们讨论了 _引用权能组合_ 和 _观点适应_ 时，我们处理了我们知道来源的引用权能的情况。然而，有时我们并不知道原点的精确引用权能。

<!-- When that happens, we can write a __viewpoint adapted type__, which we call an __arrow type__ because we write it with an `->`. -->
当这种情况发生时，我们可以编写一个适合类型为 __的视角__ ，我们称其为一个 __型箭头__ ，因为我们用一个`->`来编写它。

## Using `this->` as a viewpoint

<!-- A function with a `box` receiver can be called with a `ref` receiver or a `val` receiver as well since those are both subtypes of `box`. Sometimes, we want to be able to talk about a type to take this into account. For example: -->
带有`box`接收器的函数也可以用`ref`接收器或`val`接收器来调用，因为它们都是`box`的子类型。有时，我们希望能够讨论一种类型来考虑这一点。例如:

```pony
class Wombat
  var _friend: Wombat

  fun friend(): this->Wombat => _friend
```

<!-- Here, we have a `Wombat`, and every `Wombat` has a friend that's also a `Wombat` (lucky `Wombat`). In fact, it's a `Wombat ref`, since `ref` is the default reference capability for a `Wombat` (since we didn't specify one). We also have a function that returns that friend. It's got a `box` receiver (because `box` is the default receiver reference capability for a function if we don't specify it). -->
在这里，我们有一个`袋熊`，每个`袋熊`都有一个同样是`袋熊`的朋友(幸运的`袋熊`)。事实上，它是一个`Wombat ref`，因为`ref`是`Wombat`的默认引用权能(因为我们没有指定一个)。我们还有一个函数返回那个朋友。它有一个`box`接收器(因为`box`是一个函数的默认接收器引用权能，如果我们不指定它的话)。

<!-- So the return type would normally be a `Wombat box`. Why's that? Because, as we saw earlier, when we read a `ref` field from a `box` origin, we get a `box`. In this case, the origin is the receiver, which is a `box`. -->
所以返回类型通常是`袋熊盒子`。为什么?因为，正如我们前面看到的，当我们从一个`box`源读取一个`ref`字段时，我们会得到一个`box`。在本例中，原点是接收器，它是一个`方框`。

<!-- But wait! What if we want a function that can return a `Wombat ref` when the receiver is a `ref`, a `Wombat val` when the receiver is a `val`, and a `Wombat box` when the receiver is a `box`? We don't want to have to write the function three times. -->
但是等等!如果我们想要一个函数，在接收方是`ref`时返回`Wombat ref`，在接收方是`val`时返回`Wombat val`，在接收方是`box`时返回`Wombat box`，那该怎么办?我们不想把这个函数写三遍。

<!-- We use `this->`! In this case, `this->Wombat`. It means "a `Wombat ref` as seen by the receiver". -->
我们使用`this->`！在这种情况下，`this->Wombat`。它的意思是"接收者看到的`Wombat ref`"。

<!-- We know at the _call site_ what the real reference capability of the receiver is. So when the function is called, the compiler knows everything it needs to know to get this right. -->
我们知道在 _call站点上_ ，接收方的实际引用权能是什么。因此，当函数被调用时，编译器知道它需要知道的一切，以使其正确。

## Using a type parameter as a viewpoint

<!-- We haven't covered generics yet, so this may seem a little weird. We'll cover this again when we talk about generics (i.e. parameterised types), but we're mentioning it here for completeness. -->
我们还没有涉及泛型，所以这看起来有点奇怪。当我们讨论泛型(即参数化类型)时，我们将再次讨论这个问题，但是我们在这里提到它是为了完整性。

<!-- Another time we don't know the precise reference capability of something is if we are using a type parameter. Here's an example from the standard library: -->
另一种情况是，我们不知道某个东西的精确引用权能，即我们是否使用了类型参数。下面是来自标准库的一个例子:

```pony
class ListValues[A, N: ListNode[A] box] is Iterator[N->A]
```

<!-- Here, we have a `ListValues` type that has two type parameters, `A` and `N`. In addition, `N` has a constraint: it has to be a subtype of `ListNode[A] box`. That's all fine and well, but we also say the `ListValues[A, N]` provides `Iterator[N->A]`. That's the interesting bit: we provide an interface that let's us iterate over values of the type `N->A`. -->
这里，我们有一个`ListValues`类型，它有两个类型参数`a`和`N`。此外，`N`还有一个约束:它必须是`ListNode[a] box`的子类型。这很好，但我们也说`ListValues[A, N]`提供了`Iterator[N->A]`。这就是有趣的地方:我们提供了一个接口，让我们对类型`N->A`的值进行迭代。

<!-- That means we'll be returning objects of the type `A`, but the reference capability will be the same as an object of type `N` would see an object of type `A`. -->
这意味着我们将返回类型为`A`的对象，但是引用权能将与类型为`N`的对象看到类型为`A`的对象相同。

## Using `box->` as a viewpoint

<!-- There's one more way we use arrow types, and it's also related to generics. Sometimes we want to talk about a type parameter as it is seen by some unknown type, _as long as that type can read the type parameter_. -->
还有一种使用箭头类型的方法，它也与泛型有关。有时，我们希望讨论某个类型参数，因为它是由某个未知类型看到的，_as long as该类型可以读取type parameter_ 。

<!-- In other words, the unknown type will be a subtype of `box`, but that's all we know. Here's an example from the standard library: -->
换句话说，未知类型将是`box`的子类型，但我们只知道这些。下面是来自标准库的一个例子:

```pony
interface Comparable[A: Comparable[A] box]
  fun eq(that: box->A): Bool => this is that
  fun ne(that: box->A): Bool => not eq(that)
```

<!-- Here, we say that something is `Comparable[A]` if and only if it has functions `eq` and `ne` and those functions have a single parameter of type `box->A` and return a `Bool`. In other words, whatever `A` is bound to, we only need to be able to read it. -->
在这里，我们说一个东西是‘Comparable[A]’，当且仅当它有函数‘eq’和‘ne’，而这些函数有一个类型为‘box->A’的参数并返回一个‘Bool’时。换句话说，不管A与什么有关，我们只需要能够读懂它。
