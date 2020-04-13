---
title: "字面量（Literals）"
section: "表达式（Expressions）"
toc: true
menu:
  toc:
    parent: "expressions"
    weight: 10
toc: true
---

***What do we want?***

Values!

***Where do we want them?***

In our Pony programs!

**Say no more**

<!-- Every programming language has literals to encode values of certain types, and so does Pony. -->
每一种编程语言都会有字面量对数据的类型进行描述，Pony也不例外。

<!-- In Pony you can express booleans, numeric types, characters, strings and arrays as literals. -->
Pony表达式中常用的字面量有：布尔、数值、字符、字符串、数组。

<!-- ## Bool Literals -->
## Bool型

<!-- There is `true`, there is `false`. That's it. -->
布尔类型有两个可选值：`true` 和 `false`。

<!-- ## Numeric Literals -->
## 数字型

<!-- Numeric literals can be used to encode any signed or unsigned integer or floating point number. -->
数字型字面量包括：有符号整数、无符号整数和浮点数。

<!-- In most cases Pony is able to infer the concrete type of the literal from the context where it is used (e.g. assignment to a field or local variable or as argument to a method/behaviour call). -->
在大多数情况下，Pony可以从上下文中推导出具体的数据类型（这包括，分配给字段或局部变量或作为方法/行为调用的参数）。

<!-- It is possible to help the compiler determine the concrete type of the literal using a constructor of one of the numeric types: -->
可以使用以下一种数字类型的构造函数来帮助编译器确定字面量的具体类型：

 * U8, U16, U32, U64, U128, USize, ULong
 * I8, I16, I32, I64, I128, ISize, ILong
 * F32, F64

```pony
let my_explicit_unsigned: U32 = 42_000
let my_constructor_unsigned = U8(1)
let my_constructor_float = F64(1.234)
```

<!-- Integer literals can be given as decimal, hexadecimal or binary: -->
整数可以以十进制，十六进制或二进制形式给出：

```pony
let my_decimal_int: I32 = 1024 			//十进制
let my_hexadecimal_int: I32 = 0x400		//十六进制
let my_binary_int: I32 = 0b10000000000	//二进制
```

<!-- Floating Point literals are expressed as standard floating point or scientific notation: -->
浮点文字以标准浮点或科学计数法表示：

```pony
let my_double_precision_float: F64 = 0.009999999776482582092285156250 //标准浮点数
let my_scientific_float: F32 = 42.12e-4 //科学计数法
```

<!-- ## Character Literals -->
## 字符型

<!-- Character literals are enclosed with single quotes (`'`). -->
字符使用单引号`'`包裹。

<!-- Character literals, unlike String literals, encode to a single numeric value. Usually this is a single byte, a `U8`. But they can be coerced to any integer type: -->
字符和字符串有很大区别，它表示的是一个有符号数字。一个字节即是一个`U8`。但是它们可以被转换为任意整数类型：

```pony
let big_a: U8 = 'A'                 // 65
let hex_escaped_big_a: U8 = '\x41'  // 65
let newline: U32 = '\n'             // 10
```

<!-- The following escape sequences are supported: -->
下面是Pony支持的字符转义：

* `\x4F` hex escape sequence with 2 hex digits (up to 0xFF)
* `\a`, `\b`, `\e`, `\f`, `\n`, `\r`, `\t`, `\v`, `\\`, `\0`, `\'`

<!-- ### Multibyte Character literals -->
### 多重字符（Multibyte Character literals）

<!-- It is possible to have character literals that contain multiple characters. The resulting integer value is constructed byte by byte with each character representing a single byte in the resulting integer, the last character being the least significant byte: -->
Pony的字符字面量可以包含多个字符。生成的整数值是逐字节构造的，每个字符代表生成的整数中的单个字节，最后一个字符是最低有效字节：

```pony
let multiByte: U64 = 'ABCD' // 0x41424344
```


<!-- ## String Literals -->
## 字符串

<!-- String literals are enclosed with double quotes `"` or triple-quoted `"""`. They can contain any kind of bytes and various escape sequences: -->
字符串使用`"`或`"""`包裹。可以包含任意类型的字节或转义字符：

* `\u00FE` unicode escape sequence with 4 hex digits encoding one code point
* `\u10FFFE` unicode escape sequence with 6 hex digits encoding one code point
* `\x4F` hex escape sequence for unicode letters with 2 hex digits (up to 0xFF)
* `\a`, `\b`, `\e`, `\f`, `\n`, `\r`, `\t`, `\v`, `\\`, `\0`, `\"`

<!-- Each escape sequence encodes a full character, not byte. -->
每个转义序列编码一个完整字符，而不是字节。

```pony
use "format"

actor Main
  new create(env: Env) =>

    let pony = "🐎"
    let pony_hex_escaped = "p\xF6n\xFF"
    let pony_unicode_escape = "\U01F40E"

    env.out.print(pony + " " + pony_hex_escaped + " " + pony_unicode_escape)
    for b in pony.values() do
      env.out.print(Format.int[U8](b, FormatHex))
    end

```

<!-- All string literals support multiline strings: -->
Pony字符串支持多行定义：

```pony

let stacked_ponies = "
🐎
🐎
🐎
"
```

<!-- ### String Literals and Encodings -->
### 字符串和编码

<!-- String Literals contain the bytes that were read from their source code file. Their actual value thus depends on the encoding of their source. -->
字符串的文字如果从其源代码文件中定义。那么，它们的实际值取决于其源码文件的编码格式。 

<!-- Consider the following example: -->
请看下面的示例：

```pony
let u_umlaut = "ü"
```

<!-- If the file containing this code is encoded as *UTF-8* the byte-value of `u_umlaut` will be: `\xc3\xbc`. If the file is encoded with *ISO-8559-1* (Latin-1) its value will be `\xfc`. -->
如果文件编码格式是 *UTF-8* 那么`u_umlaut` 的值是: `\xc3\xbc`。如果文件编码格式是*ISO-8559-1* (Latin-1) 它的值就是 `\xfc`。

<!-- ### Triple quoted Strings -->
### 多行字符串（Triple quoted Strings）

<!-- For embedding multi-line text in string literals, there are triple quoted strings. -->
要在字符串中嵌入多行文本，请将字符串使用三引号引起来的。

```pony
let triple_quoted_string_docs =
  """
  Triple quoted strings are the way to go for long multiline text.
  They are extensively used as docstrings which are turned into api documentation.

  They get some special treatment, in order to keep Pony code readable:

  * The string literal starts on the line after the opening triple quote.
  * Common indentation is removed from the string literal
    so it can be conveniently aligned with the enclosing indentation
    e.g. each line of this literal will get its first two whitespaces removed
  * Whitespace after the opening and before the closing triple quote will be
    removed as well. The first line will be completely removed if it only
    contains whitespace. e.g. this strings first character is `T` not `\n`.
  """
```

<!-- ### String Literal Instances -->
### 字符串示例：

<!-- When a single string literal is used several times in your Pony program, all of them will be converted to a single common instance. This means they will always be equal based on identity. -->
如果您的Pony程序中有多出使用相同的单字符字符串时，所有这些字符串都将转换为同一个实例。它们始终相等。

```pony
let pony = "🐎"
let another_pony = "🐎"
if pony is another_pony then
  // True, therefore this line will run.
end
```

<!-- ## Array Literals -->
## 数组（Array Literals）

<!-- Array literals are enclosed by square brackets. Array literal elements can be any kind of expressions. They are separated by semicolon or newline: -->
数组的值需要用方括号括起来。数组的元素可以是任何类型的表达式。元素之间用分号`;`或换行符分隔：

```pony
let my_literal_array =
  [
    "first"; "second"
    "third one on a new line"
  ]
```

<!-- ### Type inference -->
### 类型接口（Type inference）

<!-- If the type of the array is not specified, the resulting type of the literal array expression is `Array[T] ref` where `T` (the type of the elements) is inferred as the union of all the element types: -->
如果没有指定数组的类型，数组表达式的返回类型默认为`Array [T] ref`，并且，将T（元素的类型）推导为所有元素类型的并集：

```pony
let my_heterogenous_array =
  [
    U64(42)
    "42"
    U64.min_value()
  ]
```

<!-- In the above example the resulting array type will be `Array[(U64|String)] ref` because the array contains `String` and `U64` elements. -->
在上面的示例中，由于数组包含String和U64元素，因此生成的数组类型为`Array [(U64 | String)] ref`。

<!-- If the variable or call argument the array literal is assigned to has a type, the literal is coerced to that type: -->
如果将分配给数组的元素是其他类型，Pony将会强制转换为数组的定义类型（译者注：不用担心，如果传入的类型无法被转换编译时就会报错）：

```pony
let my_stringable_array: Array[Stringable] ref =
  [
    U64(0xA)
    "0xA"
  ]
```

<!-- Here `my_stringable_array` is coerced to `Array[Stringable] ref`. This works because `Stringable` is a trait that both `String` and `U64` implement. -->
这里，`my_stringable_array`被强制转换为`Array [Stringable] ref`。之所以能编译通过，是因为String和U64都实现了`Stringable`特征。

<!-- It is also possible to return an array with a different [Reference Capability](/reference-capabilities.html) than `ref` just by specifying it on the type: -->
通过在数组上指定类型说明符`val`，可以与[ref]不同的[引用权能（Reference Capability）](/reference-capabilities.html)的数组：

```pony
let my_immutable_array: Array[Stringable] val =
  [
    U64(0xBEEF)
    "0xBEEF"
  ]
```

<!-- This way array literals can be used for creating arrays of any [Reference Capability](/reference-capabilities.html). -->
你可以创建任何[引用权能（Reference Capability）](/reference-capabilities.html)的数组类型。

<!-- ### `As` Expression -->
### as表达式

<!-- It is also possible to give the literal a hint on what kind of type it should coerce the array elements to using an `as` Expression. The expression with the desired array element type needs to be added right after the opening square bracket, delimited by a colon: -->
也有可能使用`as`表达式来提示数组元素的类型。提示表达式需要在方括号后添加，还要加上冒号：

```pony
let my_as_array =
  [ as Stringable:
    U64(0xFFEF)
    "0xFFEF"
    U64(1 + 1)
  ]
```

<!-- This array literal is coerced to be an `Array[Stringable] ref` according to the `as` expression. -->
这个as表达式，约束此数组类型为：`Array [Stringable] ref`。

<!-- If a type is specified on the left-hand side, it needs to exactly match the type in the `as` expression. -->
如果在左侧指定了类型，则需要数组元素与`as`表达式中的类型完全匹配。

<!-- ### Arrays and References -->
### 数组和引用

<!-- Constructing an array with a literal creates new references to its elements. Thus, to be 100% technically correct, array literal elements are inferred to be the alias of the actual element type. If all elements are of type `T` the array literal will be inferred as `Array[T!] ref` that is as an array of aliases of the type `T`. -->
用字面量构造一个数组会创建对其元素的新引用。因此，为了确保100%的正确性，会将数组元素推导为实际元素类型的别名。如果所有元素的类型均为T，则将数组的北行会被推导为`Array [T!] ref`，即类型别名为T的数组。

<!-- It is thus necessary to use elements that can have more than one reference of the same type (e.g. types with `val` or `ref` capability) or use ephemeral types for other capabilities (as returned from [constructors](/types/classes.html#constructors) or the [consume expression](/reference-capabilities/consume-and-destructive-read.html)). -->
要使用具有多种引用权能的数组（例如val和ref引用权能类型）可以参考下（[构造函数](/types/classes.html#constructors)和[所有权转让](/reference-capabilities/consume-and-destructive-read.html)章节）。
