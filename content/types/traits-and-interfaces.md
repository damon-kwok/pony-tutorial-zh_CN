---
title: "特征和接口（Traits and Interfaces）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 50
toc: true
---

<!-- Like other object-oriented languages, Pony has __subtyping__. That is, some types serve as _categories_ that other types can be members of. -->
与其他面向对象的语言一样，Pony具有 __多态性（subtyping）__ 。也就是说，某些类型充当 _categories_ ，其他类型可以成为其成员。

<!-- There are two kinds of __subtyping__ in programming languages: __nominal__ and __structural__. They're subtly different, and most programming languages only have one or the other. Pony has both! -->
编程语言的实现上有两种 __多态（subtyping）__ 类型： __声明式（nominal）多态__ 和 __结构化（structural）多态__ 。它们有细微的不同，大多数编程语言只会选择一种实现。但是Pony两种都实现了！


<!-- ## Nominal subtyping -->
## 声明式多态（Nominal subtyping）

<!-- This kind of subtyping is called __nominal__ because it is all about __names__. -->
这种多态的实现被称为 __声明式__ ，因为它需要你明确指出你要实现的 __类型名__ 。

<!-- If you've done object-oriented programming before, you may have seen a lot of discussion about _single inheritance_, _multiple inheritance_, _mixins_, _traits_, and similar concepts. These are all examples of __nominal subtyping__. -->
如果你有过面向对象编程经验，你应该熟悉 _继承（单）_ 、 _多重继承_ 、 _组合_ 、 _特征_ 或类似的概念，所有的这些都属于 __声明式多态__

<!-- The core idea is that you have a type that declares it has a relationship to some category type. In Java, for example, a __class__ (a concrete type) can __implement__ an __interface__ (a category type). In Java, this means the class is now in the category that the interface represents. The compiler will check that the class actually provides everything it needs to. -->
核心思想是您有一个声明与某种类别类型有关系的类型。例如，在Java中，类（具体类型）可以实现某个接口（抽象类型）。在Java中，这表示这个类必须要实现指定抽象接口。编译器将检查该类是否实现了此抽象类型需要的所有接口。

<!-- ## Traits: nominal subtyping -->
## Pony的特征（Traits）：声明式多态（Nominal subtyping）

<!-- Pony has nominal subtyping, using __traits__. A __trait__ looks a bit like a __class__, but it uses the keyword `trait` and it can't have any fields. -->
Pony使用 __特征（trait）__ 来实现声明式多态。__trait__ 的定义看上去有点像 __class__ ，但是它使用关键字trait，并且不能有任何字段。

```pony
trait Named
  fun name(): String => "Bob"

class Bob is Named
```

<!-- Here, we have a trait `Named` that has a single function `name` that returns a String. It also provides a default implementation of `name` that returns the string literal "Bob". -->
上面代码中，我们定义了一个名为`Named`的特征，他又一个名为`name()`的成员函数，返回类型是字符串。它还提供了`name()`函数的默认实现，返回字符串"Bob"。

<!-- We also have a class `Bob` that says it `is Named`. This means `Bob` is in the `Named` category. In Pony, we say _Bob provides Named_, or sometimes simply _Bob is Named_. -->
接着定义了类名为`Bob`的类。`Bob在`明确的声明自己实现了`Named`特征。在Pony中，我们把这成为 _Bob拥有Named特征_ ，或者说 _Bob就是Named_ 。

<!-- Since `Bob` doesn't have its own `name` function, it uses the one from the trait. If the trait's function didn't have a default implementation, the compiler would complain that `Bob` had no implementation of `name`. -->
我们看到`Bob`并没有定义`name()`函数，所以它会使用特征中的默认实现。如果特征对`name()`函数没有做出默认实现，则编译器就会报一个错告知：`Bob`没有实现`name()`。
 
```pony
trait Named
  fun name(): String => "Bob"

trait Bald
  fun hair(): Bool => false

 class Bob is (Named & Bald)
 ```
<!-- It is possible for a class to have relationships with multiple categories. In the above example, the class `Bob` _provides both Named and Bald_. -->
一个类可以同时具备多个特征。在上面的示例中，`Bob`类同时提供了Named和Bald特征。

```pony
trait Named
  fun name(): String => "Bob"

trait Bald is Named
  fun hair(): Bool => false

 class Bob is Bald
 ```
<!-- It is also possible to combine categories together. In the example above, all `Bald` classes are automatically `Named`. Consequently, the `Bob` class has access to both hair() and name() default implementation of their respective trait. One can think of the `Bald` category to be more specific than the `Named` one. -->
特征是支持组合的。在上面的示例中，所有`Bald`特征类被声明为包含`Named`特征。因此，`Bob`类可以访问两个特征类中的hair()和name()函数的默认实现。从语义上来说，我们可以认为Bald是一个更加明确的特征：Named泛指一个人的基础特征拥有一个名字，Bald更加明确的去描述是否有头发，然后我们使用这套规则描述一个没有头发的老头`Bob`。

```pony
class Larry
  fun name(): String => "Larry"
```

<!-- Here, we have a class `Larry` that has a `name` function with the same signature. But `Larry` does __not__ provide `Named`! -->
这个示例中，我们定义了一个名为`Larry`的类，该类也具备`name()`函数。但是`Larry`不是一个`Named`。

<!-- __Wait, why not?__ Because `Larry` doesn't say it `is Named`. Remember, traits are __nominal__: a type that wants to provide a trait has to explicitly declare that it does. And `Larry` doesn't -->
 __等等，为什么不是呢？__ 因为它并没有声明自己具备`Named`特征！ 请记住，特征需要 __声明（nominal）__ ：想要提供特征的类型必须显式的声明。`Larry`确实没有声明。

<!-- ## Structural subtyping -->
## 结构化多态（Structural subtyping）

<!-- There's another kind of subtyping, where the name doesn't matter. It's called __structural subtyping__, which means that it's all about how a type is built, and nothing to do with names. -->
多态还有另一种实现方式，无需明确声明，这种被称为 __结构化多态__ ，这意味着这与类型的构建方式有关，与名称无关。

<!-- A concrete type is a member of a structural category if it happens to have all the needed elements, no matter what it happens to be called. -->
结构化多态的实现思路是，如果一个类型A恰好具备一个结构类别B所有必需的成员，那么A就是B。

<!-- If you've used Go, you'll recognise that Go interfaces are structural types. -->
如果你用过Go语言，你会自然而然的想到Go的`interface`就是结构化多态的实现。

<!-- ## Interfaces: structural subtyping -->
## Pony的接口（Interfaces）: 结构化多态（structural subtyping）

<!-- Pony has structural subtyping too, using __interfaces__. Interfaces look like traits, but they use the keyword `interface`. -->
Pony也支持结构化多态，用 _接口（interfaces）__ 就可以做到。接口看起来和特征差不多，不过它用`interface`定义。

```pony
interface HasName
  fun name(): String
```

<!-- Here, `HasName` looks a lot like `Named`, except it's an interface instead of a trait. This means both `Bob` and `Larry` provide `HasName`! The programmers that wrote `Bob` and `Larry` don't even have to be aware that `HasName` exists. -->
上面的示例中，我们看到`HasName`接口和之前的`Named`特征，除了定义用的关键字不同外，似乎没多大区别。还记得`Larry`吗？"`Larry`不是一个`Named`"，但是"`Larry`却是是一个`HasName`"，"`Bob`也是一个`HasName`"。但是程序中没有任何地方声明`Bob`和`Larry`与`HasName`有什么关联。

<!-- Pony interfaces can have functions with default implementations as well. A type will only pick those up if it explicitly declares that it `is` that interface. -->
Pony的接口也可以具有默认实现的功能。如果类型明确声明它是该接口，则该类型将仅接受这些类型。

<!-- __Should I use traits or interfaces in my own code?__ Both! Interfaces are more flexible, so if you're not sure what you want, use an interface. But traits are a powerful tool as well: they stop _accidental subtyping_. -->
__我应该在自己的代码中使用特征还是接口？__ 相信我，你两种都用的着！接口更加灵活，因此，如果不确定自己想要什么，请使用接口。但是特征也是一个强大的工具：它可以帮你检查子类型实现的完整性。
