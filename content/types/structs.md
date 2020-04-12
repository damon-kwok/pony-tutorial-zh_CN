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
`结构体`和`类`有几个非常重要的区别。平时的Pony程序开发中一般都使用类，很少会使用结构。我们将在本教程的[C-FFI章](/c-ffi.html)中更深入地讨论结构。这里只做简短介绍。

<!-- ## Structs are "classes for FFI" -->
## 结构体是用来做外部交互的

<!-- A `struct` is a class like mechanism used to pass data back and forth with C code via Pony's Foreign Function Interface. -->
`struct`和类的机制一样，但是它可以通过Pony的FFI接口与C代码交互来传递数据。

<!-- Like classes, Pony structs can contain both fields and methods. Unlike classes, Pony structs have the same binary layout as C structs and can be transparently used in C functions.  Structs do not have a type descriptor, which means they cannot be used in algebraic types or implement traits/interfaces. -->
Pony结构体和类一样也可以包含字段和方法。与类不同的是：Pony的结构体与C结构体具有相同的内存布局，并且可以在C函数互相调用。结构体没有类型描述符，所以它们不能被用于算数类型，也不能用来实现`特征`和`接口`。

## What goes in a struct?

<!-- The same as a class! A struct is composed of some combination of: -->
结构的构成和类一样：

<!-- 1. Fields -->
<!-- 2. Constructors -->
<!-- 3. Functions -->
1.字段
2.构造函数
3.函数

<!-- ### Fields -->
### 字段

<!-- Pony struct fields are defined in the same way as they are for Pony classes, using `embed`, `let`, and `var`.  An embed field is embedded in its parent object, like a C struct inside C struct. A var/let field is a pointer to an object allocated separately. -->
Pony struct字段的定义方式与Pony类的定义方式相同，可以使用`embed`，`let`和` var`。embed字段嵌入在其父对象中，就像C结构内部的C结构一样。 var和let字段是指向单独分配的对象的指针。

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
与类的构造函数一样，结构体的构造函数也可以设置名称。之前讲过的有关Pony类构造函数的所有知识都适用于结构构造函数。

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
这里有两个构造函数。第一个创建一个新的空Pointer，第二个创建分配了内存空间的Pointer。
<!-- 如果您不遵循上面示例中看到的所有内容，请不要担心。重要的部分是，它基本上应该看起来像类构造函数示例[我们先前看到的](/types/classes.html＃what-goes-in-a-class)。 -->

<!-- ### Functions -->
### 成员函数

<!-- Like Pony classes, Pony structs can also have functions. Everything you know about functions on Pony classes applies to structs as well. -->
和类一样，结构体也可以有函数。对Pony类成员函数的所有内容也适用于结构。

## We'll see structs again

<!-- Structs play an important role in Pony's interactions with code written using C. We'll see them again in [C-FFI section](/c-ffi.html) of the tutorial. We probably won't see too much about structs until then. -->
在Pony与C代码的交互中，结构体扮演着重要的角色。我们将在教程的[C-FFI部分](/c-ffi.html)中详细讲解结构体。现在，我们不需要过多关注结构体。
