---
title: "方法（Methods）"
section: "表达式（Expressions）"
menu:
  toc:
    parent: "expressions"
    weight: 60
toc: true
---

<!-- All Pony code that actually does something, rather than defining types etc, appears in named blocks which are referred to as methods. There are three kinds of methods: functions, constructors, and behaviours. All methods are attached to type definitions (e.g. classes) - there are no global functions. -->
在Pony中，所有的执行逻辑（代码）都被放在方法中。方法有三种：函数、构造函数和行为。所有的方法都要写在类型定义中（例如：类、基元类、actor等），不存在全局函数或全局行为。

<!-- Behaviours are used for handling asynchronous messages sent to actors, which we've seen in the "Types" chapter [when we talked about actors]({{< relref "types/actors.md#behaviours" >}}). -->
行为用于处理发送给参与者的异步消息，我们在`类型系统`章节中已经讲解过[行为]({{< relref "types/actors.md#behaviours" >}})。

<!-- __Can I have some code outside of any methods like I do in Python?__ No. All Pony code must be within a method. -->
__可以像Python那样在任何方法之外添加一些代码吗？__ 不行。所有的Pony代码都必须在一个方法中。

<!-- ## Functions -->
## 函数（Functions）

<!-- Pony functions are quite like functions (or methods) in other languages. They can have 0 or more parameters and 0 or 1 return values. If the return type is omitted then the function will have a return value of `None`. -->
Pony的函数与其他语言中的函数（或方法）非常相似。它们可以具有0个或多个参数，以及0个或1个返回值（如果需要像Go语言的多返回值可以使用`元组`）。如果省略了返回类型，则该函数的返回值将为`None`。

```pony
class C
  fun add(x: U32, y: U32): U32 =>
    x + y

  fun nop() =>
    add(1, 2)  // Pointless, we ignore the result
```

<!-- The function parameters (if any) are specified in parentheses after the function name. Functions that don't take any parameters still need to have the parentheses. -->
函数参数（如果有）在函数名称后的括号中指定。即便没有任何参数的函数定义时，括号也不能省略。

<!-- Each parameter is given a name and a type. In our example function `add` has 2 parameters, `x` and `y`, both of which are type `U32`. The values passed to a function call (the `1` and `2` in our example) are called arguments and when the call is made they are evaluated and assigned to the parameters. Parameters may not be assigned to within the function - they are effectively declared `let`. -->
每个参数都有一个名称和类型。在我们的示例函数中，`add`函数有两个参数`x`和`y`，它们都是`U32`类型。传递给函数调用的值（示例中为`1`和` 2`）称为`实参（arguments）`，并且在进行调用时会对其求值并分配给`形参（parameters）`。形参虽然没有明确给出声明的方式，但实际上它们被声明为`let`。

<!-- After the parameters comes the return type. If nothing will be returned this is simply omitted. -->
形参后面的的是返回类型。如果不需要返回值，可以省略。

<!-- After the return value, there's a `=>` and then finally the function body. The value returned is simply the value of the function body (remember that everything is an expression), which is simply the value of the last command in the function. -->
返回值后面是`=>`，最后是函数体。返回值是函数体表达式的执行结果（请记住，所有内容都是表达式），也就是函数中最后一条表达式的值。

<!-- If you want to exit a function early then use the `return` command. If the function has a return type then you need to provide a value to return. If the function does not have a return type then `return` should appear on its own, without a value. -->
如果想从函数中提前返回，可以使用`return`表达式。如果函数具有返回类型，需要为`return`提供一个值。如果该函数没有返回类型，单独用`return`就行。

<!-- __Can I overload functions by argument type?__ No, you cannot have multiple methods with the same name in the same type. -->
__可以按参数类型重载函数吗？__ 不行，同一个类型中不允许出现重名的方法。

<!-- ## Constructors -->
## 构造函数（Constructors）

<!-- Pony constructors are used to initialise newly created objects, as in many languages. However, unlike many languages, Pony constructors are named so you can have as many as you like, taking whatever parameters you like. By convention, the main constructor of each type (if there is such a thing for any given type) is called `create`. -->
跟其他语言一样，Pony的构造函数也是用于初始化对象。不同的是，Pony构造函数可以自定义函数名字，也可以使用任意数量和类型的参数。Pony的默认构造函数（所有的类型定义都一样）为`create`。

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x
```

<!-- The purpose of a constructor is to set up the internal state of the object being created. To ensure this is done constructors must initialise all the fields in the object being constructed. -->
构造函数的作用，是为了在创建对象时初始化对象内部状态。Pony中构造函数必须对所有字段做出初始化。

<!-- __Can I exit a constructor early?__ Yes. Just then use the `return` command without a value. The object must already be in a legal state to do this. -->
__可以从构造函数中提前返回吗？__ 是的。使用不带值的`return`命令。但是要确保`return`前所有字段都已经进行了初始化。

<!-- ## Calling -->
## 方法调用（Calling）

<!-- As in many other languages, methods in Pony are called by providing the arguments within parentheses after the method name. The parentheses are required even if there are no arguments being passed to the method. -->
方法的调用与其他语言一样，通过在方法名称后的括号内提供参数来调用。即使没有参数需要传递，也需要括号。

```pony
class Foo
  fun hello(name: String): String =>
    "hello " + name

  fun f() =>
    let a = hello("Fred")
```

<!-- Constructors are usually called "on" a type, by specifying the type that is to be created. To do this just specify the type, followed by a dot, followed by the name of the constructor you want to call. -->
要实例化一个类型，需要调用这个类型的构造函数。指定类型，后跟一个点，然后是要调用的构造函数名：

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x

class Bar
  fun f() =>
    var a: Foo = Foo.create()
    var b: Foo = Foo.from_int(3)
```

<!-- Functions are always called on an object. Again just specify the object, followed by a dot, followed by the name of the function to call. If the object to call on is omitted then the current object is used (i.e. `this`). -->
调用对象的函数：对象名后跟一个点，然后是要调用的函数名。如果省略了要调用的对象，则使用当前对象（即`this`）。

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x

  fun get(): U32 =>
    _x

class Bar
  fun f() =>
    var a: Foo = Foo.from_int(3)
    var b: U32 = a.get()
    var c: U32 = g(b)

  fun g(x: U32): U32 =>
    x + 1

```

<!-- Constructors can also be called on an expression. Here an object is created of the same type as the specified expression - this is equivalent to directly specifying the type. -->
构造函数也可以直接调用。相当于用`this`在调用，创建的对象类型就是当前类型。

```pony
class Foo
  var _x: U32

  new create() =>
    _x = 0

  new from_int(x: U32) =>
    _x = x

class Bar
  fun f() =>
    var a: Foo = Foo.create()
    var b: Foo = a.from_int(3)
```

<!-- We can even reuse the variable name in the assignment expression to call the constructor. -->
我们甚至可以在赋值表达式中用另一个实例名来调用构造函数（上面的示例中）。

```pony
class Bar
  fun f() =>
    var a: Foo = a.create()
```

<!-- Here we specify that `var a` is type `Foo`, then proceed to use `a` to call the constructor, `create()`, for objects of type `Foo`. -->
来一个更骚的操作，上面例子中，我们用`var a`声明了一个`Foo`类型，然后直接使用`a`调用`Foo`的构造函数`create()`创建实例赋值给`a`。

<!-- ## Default arguments -->
## 参数默认值（Default arguments）

<!-- When defining a method you can provide default values for any of the arguments. The caller then has the choice to use the values you have provided or to provide their own. Default argument values are specified with a `=` after the parameter name. -->
在定义方法时，可以为参数提供默认值。调用者可以选择使用默认值，或者提供自己的值。参数的默认值在形参名后面用`=`指定。

```pony
class Coord
  var _x: U32
  var _y: U32

  new create(x: U32 = 0, y: U32 = 0) =>
    _x = x
    _y = y

class Bar
  fun f() =>
    var a: Coord = Coord.create()     // Contains (0, 0)
    var b: Coord = Coord.create(3)    // Contains (3, 0)
    var c: Coord = Coord.create(3, 4) // Contains (3, 4)
```

<!-- __Do I have to provide default values for all of my arguments?__ No, you can provide defaults for as many, or as few, as you like. -->
__我必须为所有的参数提供默认值吗?__ 不需要，可以自己决定为哪些参数设置默认值。

<!-- ## Named arguments -->
## 命名传参（Named arguments）

<!-- So far, when calling methods we have always given all the arguments in order. This is known as using __positional__ arguments. However, we can also specify the arguments in any order by specifying their names. This is known as using __named__ arguments. -->
到目前为止，在调用方法时，我们总是按顺序给出所有参数。这被称为使用 __`位置传参`或`顺序传参`（positional arguments）__ 。但是，我们也可以通过指定参数的名称来指定传参顺序。这被称为 __`命名传参`或`无序传参`（named arguments）__。

<!-- To call a method using named arguments the `where` keyword is used, followed by the named arguments and their values. -->
要使用`命名传参`的方式调用方法，需要使用`where`关键字，然后是形参名和值。

```pony
class Coord
  var _x: U32
  var _y: U32

  new create(x: U32 = 0, y: U32 = 0) =>
    _x = x
    _y = y

class Bar
  fun f() =>
    var a: Coord = Coord.create(3, 4) // Contains (3, 4)
    var b: Coord = Coord.create(where y = 4, x = 3) // Contains (3, 4)
```

<!-- Note how in `b` above, the arguments were given out of order by using `where` followed using the name of the arguments. -->
注意，上面示例中的`b`实例的创建，演示了如何使用`where`的用法。

<!-- __Should I specify `where` for each named argument?__ No. There must be only one `where` in a method call. -->
__我需要为每个参数都指定一个`where`吗?__ 不需要。方法调用时只需要一个`where`。

<!-- Named and positional arguments can be used together in a single call. Just start with the positional arguments you want to specify, then a `where` and finally the named arguments. But be careful, each argument must be specified only once. -->
在方法的调用中可以同时使用`命名传参`和`位置传参`。从需要的位置开始，使用`where`关键字就可以了。但是要注意，每个参数只能指定一次。

<!-- Default arguments can also be used in combination with positional and named arguments - just miss out any for which you want to use the default. -->
`默认参数`也可以与`位置传参`和`命名传参`结合使用——只需要忽略传递默认参数即可。

```pony
class Foo
  fun f(a: U32 = 1, b: U32 = 2, c: U32 = 3, d: U32 = 4, e: U32 = 5): U32  =>
    0

  fun g() =>
    f(6, 7 where d = 8)
    // Equivalent to:
    f(6, 7, 3, 8, 5)
```

<!-- __Can I call using positional arguments but miss out the first one?__ No. If you use positional arguments they must be the first ones in the call. -->
__使用`位置传参`调用方法是，是否忽略第一个参数?__ 不行。如果使用位置传参，没有设置默认值的参数必须放在前面。（注意：`位置传参`方式配合`默认参数`使用时，如果为方法的某个参数设置了默认值，后续的参数都必须给出默认值）。

<!-- ## Chaining -->
## 连续调用Chaining

<!-- Method chaining allows you to chain calls on an object without requiring the method to return its receiver. The syntax to call a method and chain the receiver is `object.>method()`, which is roughly equivalent to `(object.method() ; object)`. Chaining a method discards its normal return value. -->
方法链接允许您将对对象的调用链接起来，而不需要方法返回其接收器。调用一个方法并链接接收器的语法是`object.>method()`，它大致相当于`(object.method() ; object)`。连续调用方法时会丢弃它的正常返回值：

```pony
primitive Printer
  fun print_two_strings(out: StdStream, s1: String, s2: String) =>
    out.>print(s1).>print(s2)
    // 等同于：
    out.print(s1)
    out.print(s2)
    out
```

<!-- Note that the last `.>` in a chain can be a `.` if the return value of the last call matters. -->
注意，如果最后一次调用的返回值很重要的话，可以把`.>`换成`.`。

```pony
interface Factory
  fun add_option(o: Option)
  fun make_object(): Object

primitive Foo
  fun object_wrong(f: Factory, o1: Option, o2: Option): Object =>
    f.>add_option(o1).>add_option(o2).>make_object() // 错误! 这个表达式返回的是`Facyory`

  fun object_right(f: Factory, o1: Option, o2: Option): Object =>
    f.>add_option(o1).>add_option(o2).make_object() // 正确。 这个表达式返回了一个`Object`
```

<!-- ## Anonymous methods -->
## 匿名方法（Anonymous methods）

<!-- Pony has anonymous methods (or Lambdas). They look like this: -->
Pony中可以使用`匿名方法`（或者叫Lambdas）。看一下示例:

```pony
use "collections"

actor Main
  new create(env: Env) =>
    let list_of_numbers = List[U32].from([1; 2; 3; 4])
    let is_odd = {(n: U32): Bool => (n % 2) == 1}
    let list_of_odd_numbers = list_of_numbers.filter(is_odd)
    for odd_number in list_of_odd_numbers.values() do
      env.out.print(odd_number.string())
    end
```

<!-- They are presented more in-depth in the [Object Literals section](/expressions/object-literals.html). -->
在[Object Literals section](/expressions/object-literals.html)中有更详细的介绍。

<!-- ## Privacy -->
## 私有方法（Privacy）

<!-- In Pony, method names start either with a lower case letter or with an underscore followed by a lowercase letter. Methods with a leading underscore are private. This means they can only be called by code within the same package. Methods without a leading underscore are public and can be called by anyone. -->
在Pony中，方法名只能以小写字母或下划线后跟小写字母开头。下划线开头的方法是私有的。它们只能被当前包中的代码调用。非下划线开头的方法是公有的，可以在外部(其他包)调用。

<!-- __Can I start my method name with 2 (or more) underscores?__ No. If the first character is an underscore then the second one MUST be a lower case letter. -->
__我可以用2个(或更多)下划线来开始我的方法名吗?__ 不行。如果第一个字符是下划线，那么第二个字符`必须`是小写字母。

<!-- ## Precedence -->
## 调用优先级(Precedence)

<!-- We have talked about [precedence of operators](/expressions/ops.html#precedence) before, and in Pony, method calls and field accesses have higher precedence than any operators. -->
我们之前已经讨论过[运算符符优先级](/expressions/ops.html#precedence)，在Pony中，`方法调用`和`字段访问`的优先级比所有的运算符都要高。

<!-- To sum up, in complex expressions, -->
我们来总结回顾一下,在复杂的表达式中:

<!-- 1. Method calls and field accesses have higher precedence than any operators. -->
<!-- 2. Unary operator have higher precedence than infix operators. -->
<!-- 3. When mixing infix operators in complex expressions, we must use parentheses to specify precedences explicitly. -->
1. 方法调用和字段访问的优先级高于任何运算符符。
2. 一元运算符具有比中缀运算符更高的优先级。
3. 在复杂表达式中混合中缀运算符时，我们必须使用括号来显式地指定前缀。
