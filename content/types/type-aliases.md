---
title: "类型别名（Type Aliases）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 60
toc: true
---

<!-- A __type alias__ is just a way to give a different name to a type. This may sound a bit silly: after all, types already have names! However, Pony can express some complicated types, and it can be convenient to have a short way to talk about them. -->
一个 __类型别名（type alias）__ 可以给一个类型起一个不同的名字。听起来可能有点傻：毕竟类型已经有名字了，感觉有点多此一举！但有时会遇到非常复杂的类型名，起一个简短的别名是很方便的。

<!-- We'll give a couple examples of using type aliases, just to get the feel of them. -->
我们将给出几个使用类型别名的示例，以便更好的了解。

## 枚举（Enumerations）

<!-- One way to use type aliases is to express an enumeration. For example, imagine we want to say something must either be Red, Blue or Green. We could write something like this: -->
类型别名的一个很常见的应用场景是表示枚举。例如，我们想定义一组包含颜色值的枚举，：红色、蓝色或绿色。我们可以这样写:

```pony
primitive Red
primitive Blue
primitive Green

type Colour is (Red | Blue | Green)
```

<!-- There are two new concepts in there. The first is the type alias, introduced with the keyword `type`. It just means that the name that comes after `type` will be translated by the compiler to the type that comes after `is`. -->
这里有两个新概念。第一个是类型别名，它由关键字`type`引入。它只是意味着`type`后面的名称可以表示`is`后面的类型。

<!-- The second new concept is the type that comes after `is`. It's not a single type! Instead, it's a __union__ type. You can read the `|` symbol as __or__ in this context, so the type is "Red or Blue or Green". -->
第二个新概念是`is`后面的类型。它不是一个普通类型，而是一个（[类型联合体]({{< relref "type-expressions.md#Unions（类型联合体）" >}})）。在这个上下文中，您可以将`|`符号理解为`或`， 因此它的类型是`红色、蓝色或绿色`。

<!-- A union type is a form of _closed world_ type. That is, it says every type that can possibly be a member of it. In contrast, object-oriented subtyping is usually _open world_, e.g. in Java, an interface can be implemented by any number of classes. -->
`类型联合体`是 _closed world_ 类型的一种形式。也就是说，每种类型都可能是它的一个成员。相反，面向对象的子类型通常是 _open world_ ，例如在Java中，一个接口可以由任意数量的类实现。

<!-- You can also declare constants like in C or Go like this, -->
你也可以用`类型别名`机制来声明类似于C或Go语言中的常量：
```pony
primitive Red    fun apply(): U32 => 0xFF0000FF
primitive Green  fun apply(): U32 => 0x00FF00FF
primitive Blue   fun apply(): U32 => 0x0000FFFF

type Colour is (Red | Blue | Green)
```

<!-- or namespace them like this -->
或者用来表示C++中的名字空间（namespace）：
```pony
primitive Colours
  fun red(): U32 => 0xFF0000FF
  fun green(): U32 => 0x00FF00FF
```

<!-- You might also want to iterate over the enum like this to print its name for debugging purposes -->
如果你像遍历枚举列表以打印它们的名字，以便进行调试：
```pony
primitive ColourList
  fun apply(): Array[Colour] =>
    [Red; Green; Blue]

for colour in ColourList().values() do
end
```

<!-- ## Complex types -->
## 复合类型（Complex types）

<!-- If a type is complicated, it can be nice to give it a mnemonic name. For example, if we want to say that a type must implement more than one interface, we could say: -->
如果你的类型非常复杂，可以用`类型别名`来起一个简短的名字改善易读性。例如，如果想表达一个类型必须实现多个接口，我们可以这样做:

```pony
interface HasName
  fun name(): String

interface HasAge
  fun age(): U32

interface HasAddress
  fun address(): String

type Person is (HasName & HasAge & HasAddress)
```

<!-- This use of complex types applies to traits, not just interfaces: -->
这种复合类型的使用不仅适用于接口，也适用于特征:

```pony
trait HasName
  fun name(): String => "Bob"

trait HasAge
  fun age(): U32 => 42

trait HasAddress
  fun address(): String => "3 Abbey Road"

type Person is (HasName & HasAge & HasAddress)
```

<!-- There's another new concept here: the type has a `&` in it. This is similar to the `|` of a __union__ type: it means this is an __intersection__ type. That is, it's something that must be _all_ of `HasName`, `HasAge` _and_ `HasAddress`. -->
这里还有一个新概念：类型中有一个`&`。这类似于一个 __类型联合体（union）__ 的`|`：`&`意味着这是一个[类型集合]({{< relref "type-expressions.md#Intersections（类型集合）" >}}) 。也就是说，它的类型是`HasName`、`HasAge`和`HasAddress`的组合。

<!-- But the use of `type` here is exactly the same as the enumeration example above, it's just providing a name for a type that is otherwise a bit tedious to type out over and over. -->
但是这里使用的`type`的作用与上面的枚举示例完全相同，它只是为类型提供了一个名称，否则反复输入这么长的类型名会有点无聊。

<!-- Another example, this time from the standard library, is `SetIs`. Here's the actual definition: -->
有一个来自标准库的例子`SetIs`。这是它的定义:

```pony
type SetIs[A] is HashSet[A, HashIs[A!]]
```

<!-- Again there's something new here. After the name `SetIs` comes the name `A` in square brackets. That's because `SetIs` is a __generic type__. That is, you can give a `SetIs` another type as a parameter, to make specific kinds of set. If you've used Java or C#, this will be pretty familiar. If you've used C++, the equivalent concept is templates, but they work quite differently. -->
这里有一些新的东西。名称`SetIs`后面是方括号中的名称`A`。这是因为SetIs是一个`泛型`类型。也就是说可以为`SetIs`提供一种类型参数，以生成特定类型的set。如果您使用过C++，那么它等价于`模板`，但是它们的工作方式有很大差别。

<!-- And again the use of `type` just provides a more convenient way to refer to the type we're aliasing: -->
上面的代码用`类型别名`提供了一个更简短优雅的名字`SetIs[A]`指代了复合类型名：

```pony
HashSet[A, HashIs[A!]]
```

<!-- That's another __generic type__. It means a `SetIs` is really a kind of `HashSet`. Another concept has snuck in, which is `!` types. This is a type that is the __alias__ of another type. That's tricky stuff that you only need when writing complex generic types, so we'll leave it for later. -->
这是也是一个`泛型`。它表示`SetIs`实际上是一种`HashSet`。另一个概念悄然而至，那就是类型后面的`!`符号。这是另一种`类型别名`用法。这是只在编写复杂泛型类型时才需要的写法，所以我们将其留到以后讨论。

<!-- One more example, again from the standard library, is the `Map` type that gets used a lot. It's actually a type alias. Here's the real definition of `Map`: -->
另一个例子，同样来自标准库，是经常使用的`Map`类型。它实际上也是一个类型别名。以下是`Map`的定义:

```pony
type Map[K: (Hashable box & Comparable[K] box), V] is HashMap[K, V, HashEq[K]]
```

<!-- Unlike our previous example, the first type parameter, `K`, has a type associated with it. This is a __constraint__, which means when you parameterise a `Map`, the type you pass for `K` must be a subtype of the constraint. -->
与前面的示例不同，第一个类型参数`K`具有与之关联的类型。这是一个`泛型约束（constraint`，这意味着当你参数化一个`Map`时，传递给`K`的类型必须是泛型约束条件要求的类型。

<!-- Also, notice that `box` appears in the type. This is a __reference capability__. It means there is a certain class of operations we need to be able to do with a `K`. We'll cover this in more detail later. -->
另外，请注意`box`出现在类型中。这是一个`引用权能`。英文我们我们需要使用用K来做某一类的运算。稍后我们将对此进行更详细的讨论。

<!-- Just like our other examples, all this really means is that `Map` is really a kind of `HashMap`. -->
跟前面的其他例子一样，这里的`Map`实际上是`HashMap`的一个类型别名。

<!-- ## Other stuff -->
## 其他补充（Other stuff）

<!-- Type aliases get used for a lot of things, but this gives you the general idea. Just remember that a type alias is always a convenience: you could replace every use of the type alias with the full type after the `is`. -->
类型别名有很多用途，但这里只是大致的介绍了它概念。只需记住，类型别名可以提供一种便利：每次使用`type`后的别名替换`is`之后的完整类型名。

<!-- In fact, that's exactly what the compiler does. -->
Pony对于类型别名替的换是在编译时进行的。
