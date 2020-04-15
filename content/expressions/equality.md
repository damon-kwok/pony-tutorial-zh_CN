---
title: "比较运算（Equality in Pony）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 80
toc: true
---

Pony features two forms of equality: by structure and by identity.
小马有两种形式的平等:结构平等和身份平等。

## Identity equality

Identity equality checks in Pony are done via the `is` keyword. `is` verifies that the two items are the same.
Pony中的身份平等检查是通过‘is’关键字来完成的。' is '验证这两个项是否相同。

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

## Structural equality

Structural equality checking in Pony is done via the infix operator `==`. It verifies that two items have the same value. If the identity of the items being compared is the same, then by definition they have the same value.
小马的结构平等检查是通过中缀运算符' == '来完成的。它验证两个项目具有相同的值。如果被比较的项的标识相同，那么根据定义，它们具有相同的值。

You can define how structural equality is checked on your object by implementing `fun eq(that: box->Foo): Bool`. Remember, since `==` is an infix operator, `eq` must be defined on the left operand, and the right operand must be of type `Foo`.
你可以通过实现' fun eq(that: box->Foo): Bool '来定义如何在你的对象上检查结构是否相等。请记住，因为' == '是一个中缀操作符，' eq '必须定义在左边的操作数上，而右边的操作数必须是' Foo '类型。

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

If you don't define your own `eq`, you will inherit the default implementation that defines equal by value as being the same as by identity.
如果您不定义自己的' eq '，您将继承默认实现，该实现将equal by value定义为与by identity相同。

```pony
interface Equatable[A: Equatable[A] #read]
  fun eq(that: box->A): Bool => this is that
  fun ne(that: box->A): Bool => not eq(that)
```

## Primitives and equality

As you might remember from [Chapter 2](https://tutorial.ponylang.io/types/primitives.html), primitives are the same as classes except for two important differences:
您可能还记得在[Chapter 2](https://tutorial.ponylang.io/types/primitives.html)中，除了两个重要的区别之外，原语与类是相同的:

* A primitive has no fields.
* There is only one instance of a user-defined primitive.
* 原语没有字段。
* 用户定义的原语只有一个实例。

This means, that every primitive of a given type, is always structurally equal and equal based on identity. So, for example, None is always None.
这意味着，给定类型的每个原语，在结构上总是相等的，并且基于恒等而相等。例如，None总是None。

```pony
if None is None then
  // this is always true
end

if None == None then
  // this is also always true
end
```
