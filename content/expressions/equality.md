---
title: "比较（Equality in Pony）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 80
toc: true
---

<!-- Pony features two forms of equality: by structure and by identity. -->
Pony有两种形式的平等:结构平等和身份平等。

<!-- ## Identity equality -->
## 类型比较（Identity equality）

<!-- Identity equality checks in Pony are done via the `is` keyword. `is` verifies that the two items are the same. -->
Pony中的类型检查是通过`is`关键字来完成的。`is`可以判断两个类型是否相同。

```pony
if None is None then
  // TRUE!
  // There is only 1 None so the identity is the same
end

let a = Foo("hi")
let b = Foo("hi")

if a is b then
  // NOPE. THIS IS FALSE
end

let c = a
if a is c then
  // YUP! TRUE!
end
```

<!-- ## Structural equality -->
## 结构化比较（Structural equality）

<!-- Structural equality checking in Pony is done via the infix operator `==`. It verifies that two items have the same value. If the identity of the items being compared is the same, then by definition they have the same value. -->
Pony的结构化比较是通过中缀运算符`==`来完成的。它可以判断同的数据是否具有相同的值。如果被比较结果为`true`，可以认为它们具有相同的值。

<!-- You can define how structural equality is checked on your object by implementing `fun eq(that: box->Foo): Bool`. Remember, since `==` is an infix operator, `eq` must be defined on the left operand, and the right operand must be of type `Foo`. -->
可以通过实现`fun eq(that: box->Foo): Bool`来自定义对比逻辑。注意，因为`==`是一个中缀操作符，`eq`必须定义在左边的操作数上，而右边的操作数必须是`Foo`类型。

```pony
class Foo
  let _a: String

  new create(a: String) =>
    _a = a

  fun eq(that: box->Foo): Bool =>
    this._a == that._a

actor Main
  new create(e: Env) =>
    let a = Foo("hi")
    let b = Foo("bye")
    let c = Foo("hi")

    if a == b then
      // won't print
      e.out.print("1")
    end

    if a == c then
      // will print
      e.out.print("2")
    end

    if a is c then
      // won't print
      e.out.print("3")
    end
```

<!-- If you don't define your own `eq`, you will inherit the default implementation that defines equal by value as being the same as by identity. -->
如果没有定义自己的`eq`，Pony将会提供一个默认实现，该实现只是简单的对比类型是否相同。

```pony
interface Equatable[A: Equatable[A] #read]
  fun eq(that: box->A): Bool => this is that
  fun ne(that: box->A): Bool => not eq(that)
```

<!-- ## Primitives and equality -->
## 基元类的类型比较（Primitives and equality）

<!-- As you might remember from [Chapter 2]({{< relref "types/primitives.md" >}}), primitives are the same as classes except for two important differences: -->
你可能还记得在[第二章：基元类]({{< relref "types/primitives.md" >}})中讲过，除了两个重要的区别之外，基元类与类有两个重要区别:

<!-- * A primitive has no fields. -->
<!-- * There is only one instance of a user-defined primitive. -->
* 基元类没有字段。
* 所有用户自定义的基元类只会有一个实例。

<!-- This means, that every primitive of a given type, is always structurally equal and equal based on identity. So, for example, None is always None. -->
这意味着，同类型的基元类实例在结构上总是相等的，并且是完全相等。例如，所有的`None`都是同一个实例。

```pony
if None is None then
  // this is always true
end

if None == None then
  // this is also always true
end
```
