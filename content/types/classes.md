---
title: "类（Classes）"
section: "类型系统（Types）"
menu:
  toc:
    parent: "types"
    weight: 20
toc: true
---

<!-- Just like other object-oriented languages, Pony has __classes__. A class is declared with the keyword `class`, and it has to have a name that starts with a capital letter, like this: -->
和其他面向对象语言一样，Pony也有 __类__ 。声明一个类的关键字是`class`，类名首字母必须大写，就像这样：

```pony
class Wombat
```

<!-- __Do all types start with a capital letter?__ Yes! And nothing else starts with a capital letter. So when you see a name in Pony code, you will instantly know whether it's a type or not. -->
__所有的类型都必须以大写字母开头吗？__ 没错！当你阅读Pony代码时，你可以通过命名轻松判断是否是一个类型。

<!-- ## What goes in a class? -->
## 类的组成部分

<!-- A class is composed of: -->
一个类的组成：

<!-- 1. Fields. -->
1. 字段
<!-- 2. Constructors. -->
2. 构造函数
<!-- 3. Functions. -->
3. 成员函数

<!-- ### Fields -->
### 字段

<!-- These are just like fields in C structs or fields in classes in C++, C#, Java, Python, Ruby, or basically any language, really. There are three kinds of fields: `var`, `let`, and `embed` fields. A `var` field can be assigned to over and over again, but a `let` field is assigned to in the constructor and never again. Embed fields will be covered in more detail in the documentation on [variables]({{< relref "expressions/variables.md" >}}). -->
和C++、C#、Java、Python、Ruby等语言中的字段（类数据成员，类成员变量）类似。字段有三种声明方式：`var`,`let`和`embed`。`var`字段可以初始化和反复赋值，但是`let`字段初始化后无法再次赋值,`embed`字段比较复杂，详情参考[变量]({{< relref "expressions/variables.md" >}})章节。

```pony
class Wombat
  let name: String
  var _hunger_level: U64
```

<!-- Here, a `Wombat` has a `name`, which is a `String`, and a `_hunger_level`, which is a `U64` (an unsigned 64-bit integer). -->
上面例子中，类`Wombat`有一个`String`类型的`name`字段，和一个`U64`（64位无符号长整形）类型的`_hunger_level`字段。

<!-- __What does the leading underscore mean?__ It means something is __private__. A __private__ field can only be accessed by code in the same type. A __private__ constructor, function, or behaviour can only be accessed by code in the same package. We'll talk more about packages later. -->
__下划线开头是啥意思？__ 它表示 __私有__ ，一个 __私有__ 字段只能在类内部使用，外部无法访问。下划线同样可以作用于 __构造函数__ ， __成员函数__ 和 __行为__ ，标识只能在 __包__ 内部访问。稍后会讲解到 __包__ 的概念。

<!-- ### Constructors -->
### 构造函数

<!-- Pony constructors have names. Other than that, they are just like constructors in other languages. They can have parameters, and they always return a new instance of the type. Since they have names, you can have more than one constructor for a type. -->
Pony的构造函数可以起 __别名__ 。和其他语言里一样，构造函数返回一个新的类型实例，但Pony __别名__ 可以有更多的构造方式。

<!-- Constructors are introduced with the __new__ keyword. -->
声明一个构造函数需要用 __new__ 关键字。

```pony
class Wombat
  let name: String
  var _hunger_level: U64

  new create(name': String) =>
    name = name'
    _hunger_level = 0

  new hungry(name': String, hunger': U64) =>
    name = name'
    _hunger_level = hunger'
```

<!-- Here, we have two constructors, one that creates a `Wombat` that isn't hungry, and another that creates a `Wombat` that might be hungry or might not. Unlike some other languages that differentiate between constructors with method overloading, Pony won't presume to know which alternate constructor to invoke based on the arity and type of your arguments. To choose a constructor, invoke it like a method with the `.` syntax: -->
上面例子中，我们创建了两个构造函数，第一个用来创建正常状态的树袋熊（`Wombat`）,第二个用来构造具有饥饿等级的树袋熊。Pony的构造函数__别名__机制为创建实例，提供了多样性。构造对象时使用`.`就可以选择构造函数：

```pony
let defaultWombat = Wombat("Fantastibat") // 使用默认构造函数
let hungryWombat = Wombat.hungry("Nomsbat", 12) // 使用`hunger`构造函数
```

<!-- __What's with the single quote thing, i.e. name'?__ You can use single quotes in parameter and local variable names. In mathematics, it's called a _prime_, and it's used to say "another one of these, but not the same one". Basically, it's just convenient. -->
__构造函数中`name'`参数的单引号时啥意思？__ 你可以在 __参数__ 和 __内部变量__ 命名中使用单引号，用来区分时 _外部传入_ 和 _内部定义_ 的字段（就像Python的self.name=name Java的this.nama=name C++的：this->name=name)。

<!-- Every constructor has to set every field in an object. If it doesn't, the compiler will give you an error. Since there is no `null` in Pony, we can't do what Java, C# and many other languages do and just assign either `null` or zero to every field before the constructor runs, and since we don't want random crashes, we don't leave fields undefined (unlike C or C++). -->
每一个构造函数都要为所有字段做出初始化，否则编译器会给你一个error。Pony中是不存在`null`的，我们不能像Java，C#等语言中一样将字段赋值为`null`，也没有数字不初始化默认为0的规则。Pony不希望在你运行时因为字段undefined导致崩溃（不像C和C++）。

<!-- Sometimes it's convenient to set a field the same way for all constructors. -->
有时我们希望可以为所有构造函数快速的设置字段初始值：

```pony
class Wombat
  let name: String
  var _hunger_level: U64
  var _thirst_level: U64 = 1

  new create(name': String) =>
    name = name'
    _hunger_level = 0

  new hungry(name': String, hunger': U64) =>
    name = name'
    _hunger_level = hunger'
```

<!-- Here, every `Wombat` begins a little bit thirsty, regardless of which constructor is called. -->
上面的例子中，通过为字段赋初值，默认将口渴等级字段设置为1，需要不同的值，可以在构造函数中做修改。

<!-- __Zero Argument Constructors__ -->
__无参数的构造函数__
```pony
class Hawk
  var _hunger_level: U64 = 0

class Owl
  var _hunger_level: U64

  new create() =>
    _hunger_level = 42
```

<!-- Here we have two classes, because the `Hawk` class defines no constructors, a default constructor with zero arguments called `create` is generated. The `Owl` defines it's own constructor that sets the `_hunger_level`. -->
上面例子中我们定义了两个类，`Hawk`类没有构造函数，编译器会生成一个默认的`create`构造函数。`Owl`定义了一个构造函数设置`_hunger_level`字段。

<!-- When constructing instances of classes that have zero-argument constructors, they can be constructed with just the class name: -->
创建实例时如果类拥有一个无参数构造函数，直接省略掉括号：
```pony
class Forest
  let _owl: Owl = Owl
  let _hawk: Hawk = Hawk
```
<!-- This is explained later, in more detail in the [sugar]({{< relref "expressions/sugar.md" >}}) section. -->
稍后我们会在[语法糖]({{< relref "expressions/sugar.md" >}})章节中做出详细解释。

<!-- ### Functions -->
### 成员函数

<!-- Functions in Pony are like methods in Java, C#, C++, Ruby, Python, or pretty much any other object oriented language. They are introduced with the keyword `fun`. They can have parameters like constructors do, and they can also have a result type (if no result type is given, it defaults to `None`). -->
Pony中的成员函数类似于Java，C#，C++，Ruby，Python等面向对象语言中的方法（类成员函数）。和构造函数一样它们也可以有参数和返回值类型（注意是_可以有_，不是必须。如果没有明确给出返回值类型，默认为`None`，表示没有返回值）。

```pony
class Wombat
  let name: String
  var _hunger_level: U64
  var _thirst_level: U64 = 1

  new create(name': String) =>
    name = name'
    _hunger_level = 0

  new hungry(name': String, hunger': U64) =>
    name = name'
    _hunger_level = hunger'

  fun hunger(): U64 => _hunger_level

  fun ref set_hunger(to: U64 = 0): U64 => _hunger_level = to
```

<!-- The first function, `hunger`, is pretty straight forward. It has a result type of `U64`, and it returns `_hunger_level`, which is a `U64`. The only thing a bit different here is that no `return` keyword is used. This is because the result of a function is the result of the last expression in the function, in this case, the value of `_hunger_level`. -->
我们从`hunger`函数说起：它有一个`U64`返回类型，返回树袋熊当前的口渴等级。这里我们没有使用`return`关键字，因为我们要返回的`_hunger_level`是函数的最后一个表达式。

<!-- __Is there a `return` keyword in Pony?__ Yes. It's used to return "early" from a function, i.e. to return something right away and not keep running until the last expression. -->
__Pony中有`return`关键字吗？__ 当然有，当你需要在函数中"提前"返回时，也就是说你不希望函数运行到最后一个表达式的时候，就需要用到`return`关键字。

<!-- The second function, `set_hunger`, introduces a _bunch_ of new concepts all at once. Let's go through them one by one. -->
再来看第二个函数`set_hunger`，这里出现了 _一堆_ 新概念，让我们来逐步了解一下这些概念。

<!-- * The `ref` keyword right after `fun` -->
* `fun`后面的`ref`关键字

<!-- This is a __reference capability__. In this case, it means the _receiver_, i.e. the object on which the `set_hunger` function is being called, has to be a `ref` type. A `ref` type is a __reference type__, meaning that the object is __mutable__. We need this because we are writing a new value to the `_hunger_level` field. -->
这是一个 __引用权能__ 。在这里，因为我们需要修改`_hunger_level`字段。

<!-- __What's the receiver reference capability of the `hunger` method?__ The default receiver reference capability if none is specified is `box`, which means "I need to be able to read from this, but I won't write to it". -->
__`hunger`方法的接收者参考能力是什么？__ 如果未指定，默认的接收者引用权能是`box`，这意味着“我只需要能读取，不需要修改”。

<!-- __What would happen if we left the `ref` keyword off the `set_hunger` method?__ The compiler would give you an error. It would see you were trying to modify a field and complain about it. -->
 __如果我们将set_hunger方法中的`ref`关键字保留下来会怎样？__ 编译器会给您一个错误。它会显示您正在尝试修改字段并抱怨它。

<!-- * The `= 0` after the parameter `to` -->
* 参数`to`后面的`= 0`

<!-- This is a __default argument__. It means that if you don't include that argument at the call site, you will get the default argument. In this case, `to` will be zero if you don't specify it. -->
这是一个 __默认参数__ 。当你调用这个函数时没有传入参数，就会默认为这个值，在这里它的默认值被设置为0。

<!-- * What does the function return? -->
* 函数实际返回了什么？

<!-- It returns the _old_ valuzahunger_level -->
它返回修改前的 _旧值_ 。

<!-- __Wait, seriously? The _old_ value?__ Yes. Innment is an expression rather than a statement. That means it has a result. This is true of a lot of languages, but they tend to return the _new_ value. In other words, given `a = b`, in most languages, the value of that is the value of `b`s the _old_ value of `a`. -->
__等等，开什么玩笑？_旧值_？__ 没错，在Pony中赋值操作是一个表达式，这意味着它有一个返回值，在很多数语言里赋值语句会返回_新值_，换句话说`a=b`在大多数语言里都会返回`b`，但是在Pony中返回`a`。

<!-- __...why?__ It's called a "destructive read", and it lets you do awesome things with a capabilities-secure type system. We'll talk about that later. For now, we'll just mention that you can also use it to implement a _swap_ operation. In most languages, to swap the values of `a` and `b` you need to do something like: -->
__...为什么？__ 这被称为"破坏性读取"，它使您可以使用功能安全类型系统来完成出色的工作，我们稍后再讨论。现在，我们只只需要知道可以使用它来实现_swap_操作。在其他语言里要交换`a`和`b`的值你通常需要这么做：

```pony
var temp = a
a = b
b = temp
```

<!-- In Pony, you can just do: -->
用Pony这样就可以了：

```pony
a = b = a
```

<!-- ### Finaliser -->
### 销毁函数

<!-- Finalisers are special functions. They are named `_final`, take no parameters and have a receiver reference capability of `box`. In other words, the definition of a finaliser must be `fun _final()`. -->
销毁函数是一个特殊函数，函数名为`_final`，`box`。函数必须被定义为`fun _final()`。

<!-- The finaliser of an object is called before the object is collected by the GC. Functions may still be called on an object after its finalisation, but only from within another finaliser. Messages cannot be sent from within a finaliser. -->
在GC收集对象之前，将调用对象的销毁函数。对象在销毁后成员函数依然可以被调用，但只能从另一个销毁函数内调用。另外需要注意一点：无法从销毁函数中发送消息。

<!-- Finalisers are usually used to clean up resources allocated in C code, like file handles, nework sockets, etc. -->
销毁函数通常用于清理以C代码分配的资源，例如文件句柄，网络套接字等。

<!-- ## What about inheritance? -->
## 关于继承？

<!-- In some object-oriented languages, a type can _inherit_ from another type, like how in Java something can __extend__ something else. Pony doesn't do that. Instead, Pony prefers _composition_ to _inheritance_. In other words, instead of getting code reuse by saying something __is__ something else, you get it by saying something __has__ something else. -->
在很多面向对象的语言中，一种类型可以继承另一种类型，例如在Java中某种东西可以“扩展”其他某种东西。Pony不这样做。相反，Pony的做法是 _组合_ 而不是 _继承_ 。换句话说，在Pony中你的逻辑需要去表达 __has__ ，而不是 __is__ 。

<!-- On the other hand, Pony has a powerful __trait__ system (similar to Java 8 interfaces that can have default implementations) and a powerful __interface__ system (similar to Go interfaces, i.e. structurally typed). -->
另一方面，Pony具有强大的 __trait（特征）__ 系统（类似于具有默认实现的Java 8 接口）和强大的 __interface（接口）__ 系统（类似于Go语言的接口，也就是结构化类型）。

<!-- We'll talk about all that stuff in detail later. -->
稍后我们将详细讨论这些内容。

<!-- ## Naming rules -->
## 命名规则

<!-- All names in Pony, such as type names, method names, and variable names may contain only [__ASCII__](https://en.wikipedia.org/wiki/ASCII) characters. -->
Pony中的所有命名，包括类型名，方法名和变量名等，都只能包含 [__ASCII__](https://en.wikipedia.org/wiki/ASCII)字符。

<!-- In fact all elements of Pony code are required to be ASCII, except string literals, which happily accept any kind of bytes directly from the source file, be it UTF-8 encoded or ISO-8859-2 and represent them in their encoded form. -->
实际上，Pony代码的所有的元素都必须为ASCII，字符串文内容除外，字符串可以愉快地直接从源文件中接受任何类型的字节（无论是UTF-8编码还是ISO-8859-2并以其编码形式表示）。

<!-- A Pony type, whether it's a class, actor, trait, interface, primitive, or type alias, must start with an uppercase letter. After an underscore for private or special _methods_ (behaviors, constructors, and functions), any method or variable, including parameters and fields, must start with a lowercase letter. In all cases underscores in a row or at the end of a name are not allowed, but otherwise, any combination of letters and numbers is legal. -->
Pony的类型，无论是class，actor，trait，interface，primitive还是类型别名，都必须以大写字母开头。在私有或特殊 _methods_ （行为，构造函数和普通函数）的下划线之后，任何方法或变量（包括参数和字段）都必须以小写字母开头。在所有情况下，都不允许在一行中或名称末尾加下划线，但否则，字母和数字的任何组合都是合法的。

<!-- In fact, numbers may use single underscores inside as a separator too! But only valid variable names can end in primes. -->
实际上，数字也可以使用单个下划线作为分隔符！但是，只有有效的变量名才能以质数结尾。
