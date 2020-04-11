---
title: "结构体（Structs）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 55
toc: true
---

<!-- A `struct` is similiar to a `class`. There's a couple very important differences. You'll use classes throughout your Pony code. You'll rarely use structs. We'll discuss structs in more depth in the [C-FFI chapter](/c-ffi.html) of the tutorial. In the meantime, here's a short introduction to the basics of structs. -->
一个“结构”类似于一个“类”。有几个非常重要的区别。您将在整个Pony代码中使用类。您将很少使用结构。我们将在本教程的[C-FFI章]（/ c-ffi.html）中更深入地讨论结构。同时，这是对结构基础的简短介绍。

## Structs are "classes for FFI"

A `struct` is a class like mechanism used to pass data back and forth with C code via Pony's Foreign Function Interface.

<!-- Like classes, Pony structs can contain both fields and methods. Unlike classes, Pony structs have the same binary layout as C structs and can be transparently used in C functions.  Structs do not have a type descriptor, which means they cannot be used in algebraic types or implement traits/interfaces. -->
struct是类似于类的机制，用于通过Pony的外函数接口与C代码来回传递数据。 像类一样，Pony结构可以包含字段和方法。与类不同，Pony结构与C结构具有相同的二进制布局，并且可以在C函数中透明使用。结构没有类型描述符，这意味着它们不能用于代数类型或实现特征/接口。

## What goes in a struct?

<!-- The same as a class! A struct is composed of some combination of: -->
和上课一样！结构由以下几种组合组成：

<!-- 1. Fields -->
<!-- 2. Constructors -->
<!-- 3. Functions -->
1.字段
2.构造函数
3.函数

<!-- ### Fields -->
### 字段

<!-- Pony struct fields are defined in the same way as they are for Pony classes, using `embed`, `let`, and `var`.  An embed field is embedded in its parent object, like a C struct inside C struct. A var/let field is a pointer to an object allocated separately. -->
Pony struct字段的定义方式与Pony类的定义方式相同，分别使用“ embed”，“ let”和“ var”。嵌入字段嵌入在其父对象中，就像C结构内部的C结构一样。 var / let字段是指向单独分配的对象的指针。

<!-- For example: -->
例如：

```pony
struct Inner
  var x: I32 = 0

struct Outer
  embed inner_embed: Inner = Inner
  var inner_var: Inner = Inner

```

<!-- ### Constructors -->
### 构造函数

<!-- Struct constructors, like class constructors, have names. Everything you previously learned about Pony class constructors applies to struct constructors. -->
与类的构造函数一样，结构体的构造函数也可以设置名称。您先前了解的有关Pony类构造函数的所有知识都适用于结构构造函数。

```pony
struct Pointer[A]
  """
  A Pointer[A] is a raw memory pointer. It has no descriptor and thus can't be
  included in a union or intersection, or be a subtype of any interface. Most
  functions on a Pointer[A] are private to maintain memory safety.
  """
  new create() =>
    """
    A null pointer.
    """
    compile_intrinsic

  new _alloc(len: USize) =>
    """
    Space for len instances of A.
    """
    compile_intrinsic
```

<!-- Here we have two constructors. One that creates a new null Pointer, and another creates a Pointer with space for many instances of the type the Pointer is pointing at. Don't worry if you don't follow everything you are seeing in the above example. The important part is, it should basically look like the class constructor example [we saw earlier](/types/classes.html#what-goes-in-a-class). -->
这里我们有两个构造函数。一个创建一个新的空Pointer，另一个创建一个Pointer，并为该Pointer指向的类型的许多实例提供空间。如果您不遵循上面示例中看到的所有内容，请不要担心。重要的部分是，它基本上应该看起来像类构造函数示例[我们先前看到的]（/ types / classes.html＃what-goes-in-a-class）。

<!-- ### Functions -->
### 函数

<!-- Like Pony classes, Pony structs can also have functions. Everything you know about functions on Pony classes applies to structs as well. -->
像类一样，结构体也可以有函数。您对Pony类上的函数所了解的所有内容也适用于结构。

## We'll see structs again

<!-- Structs play an important role in Pony's interactions with code written using C. We'll see them again in [C-FFI section](/c-ffi.html) of the tutorial. We probably won't see too much about structs until then. -->
在Pony与使用C编写的代码的交互中，结构扮演着重要的角色。我们将在教程的[C-FFI部分]（/ c-ffi.html）中再次看到结构。在那之前，我们可能不会对结构有太多了解。
