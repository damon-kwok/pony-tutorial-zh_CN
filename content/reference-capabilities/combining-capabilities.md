---
title: "权能合并（Combining Capabilities）"
section: "引用权能（Reference Capabilities）"
menu:
  toc:
    parent: "reference-capabilities"
    weight: 90
toc: true
---

<!-- When a field of an object is read, its reference capability depends both on the reference capability of the field and the reference capability of the __origin__, that is, the object the field is being read from. -->
当一个对象的一个字段被读取时，它的引用权能既取决于字段的引用权能，也取决于_origin__的引用权能，也就是说，从该字段被读取的对象。

<!-- This is because all the guarantees that the __origin__ reference capability makes have to be maintained for its fields as well. -->
这是因为对其字段所做的所有保证都必须对其字段进行维护。

<!-- # Viewpoint adaptation -->
# 观点适应（Viewpoint adaptation）

<!-- The process of combining origin and field capabilities is called __viewpoint adaptation__. That is, the __origin__ has a __viewpoint__, and can "see" its fields only from that __viewpoint__. -->
将原始权能和字段权能相结合的过程称为 __视角适应__ 。也就是说，__origin__ 有一个 __viewpoint__ ，并且只能从那个 __viewpoint__ 中`访问`它的字段。

<!-- Let's start with a table. This shows how __fields__ of each capability "look" to __origins__ of each capability. -->
下表展示了不同权能的 __字段__ 看起来是如何与每个权能的 __源__ 相匹配的。

---

| &#x25B7;        | iso field | trn field | ref field | val field | box field | tag field |
|-----------------|-----------|-----------|-----------|-----------|-----------|-----------|
| __iso origin__  | iso       | tag       | tag       | val       | tag       | tag       |
| __trn origin__  | iso       | trn       | box       | val       | box       | tag       |
| __ref origin__  | iso       | trn       | ref       | val       | box       | tag       |
| __val origin__  | val       | val       | val       | val       | val       | tag       |
| __box origin__  | tag       | box       | box       | val       | box       | tag       |
| __tag origin__  | n/a       | n/a       | n/a       | n/a       | n/a       | n/a       |

---

<!-- For example, if you have a `trn` origin and you read a `ref` field, you get a `box` result: -->
例如，如果你有一个' trn '原点，你读了一个' ref '字段，你会得到一个' box '结果:

```pony
class Foo
  var x: String ref

class Bar
  fun f() =>
    var y: Foo trn = get_foo_trn()
    var z: String box = y.x
```

<!-- ## Explaining why -->
## 解释了为什么（Explaining why）

<!-- That table will seem totally natural to you, eventually. But probably not yet. To help it seem natural, let's walk through each cell in the table and explain why it is the way it is. -->
最终，你会觉得这张桌子很自然。但可能还没有。为了让它看起来更自然，让我们遍历表中的每个单元格并解释为什么它是这样的。

<!-- ### Reading from an `iso` variable -->
### 读取`iso`变量

<!-- Anything read through an `iso` origin has to maintain the isolation guarantee that the origin has. The key thing to remember is that the `iso` can be sent to another actor and it can also become any other reference capability. So when we read a field, we need to get a result that won't ever break the isolation guarantees that the origin makes, that is, _read and write uniqueness_. -->
任何通过“iso”来源读取的内容都必须保持来源的隔离保证。要记住的关键一点是，“iso”可以发送给另一个参与者，它也可以成为任何其他引用权能。因此，当我们读取一个字段时，我们需要得到一个结果，这个结果永远不会打破源所做的隔离保证，即_read和write uniqueness_。

<!-- An `iso` field makes the same guarantees as an `iso` origin, so that's fine to read. A `val` field is _globally immutable_, which means it's always ok to read it, no matter what the origin is (well, other than `tag`). -->
“iso”字段与“iso”源字段具有相同的保证，所以读起来没有问题。“val”字段是_global immutable_，这意味着它总是可以读取，不管它的来源是什么(好吧，除了“标签”)。

<!-- Everything else, though, can break our isolation guarantees. That's why other reference capabilities are seen as `tag`: it's the only type that is neither readable nor writable. -->
然而，其他一切都可能打破我们的隔离保障。这就是为什么其他引用权能被视为“标记”:它是唯一一种既不可读又不可写的类型。

<!-- ### Reading from a `trn` variable -->
### 读取`trn`变量

<!-- This is like `iso`, but with a weaker guarantee (_write uniqueness_ as opposed to _read and write uniqueness_). That makes a big difference since now we can return something readable when we enforce our guarantees. -->
这就像“iso”，但有一个较弱的保证(_write uniqueness_而不是_read和write uniqueness_)。这是一个很大的不同，因为现在我们可以返回一些可读的东西，当我们执行我们的保证。

<!-- An `iso` field makes stronger guarantees than a `trn` origin, and a `trn` field makes the same guarantees, so they're fine to read. A `val` field is _globally immutable_, so that's fine too. A `box` field is readable, and we only guarantee _write uniqueness_, so that's fine too. -->
“iso”字段比“trn”字段提供了更强的保证，而“trn”字段也提供了相同的保证，所以可以阅读它们。一个“val”字段是全局不可变的，所以这也是可以的。“box”字段是可读的，我们只保证_write的独特性，所以这也是可以的。

<!-- A `ref` field, though, would allow writing. So instead we return a `box`. -->
不过，“ref”字段允许写入。因此我们返回一个“box”。

<!-- ### Reading from a `ref` variable -->
### 读取`ref`变量

<!-- A `ref` origin doesn't modify its fields at all. This is because a `ref` origin doesn't make any guarantees that are incompatible with its fields. -->
“ref”源根本不修改它的字段。这是因为' ref '源不做任何与它的字段不兼容的保证。

<!-- ### Reading from a `val` variable -->
### 读取`val`变量

<!-- A `val` origin is deeply and globally immutable, so all of its fields are also `val`. The only exception is a `tag` field. Since we can't read from it, we also can't guarantee that nobody can write to it, so it stays `tag`. -->
“val”源是深度的、全局不可变的，所以它的所有字段也是“val”。唯一的例外是' tag '字段。因为我们不能读它，我们也不能保证没有人可以写它，所以它保持'标签'。

<!-- ### Reading from a `box` variable -->
### 读取`box`变量

<!-- A `box` variable is locally immutable. This means it's possible that it may be mutated through some other variable (a `trn` or a `ref`), but it's also possible that our `box` variable is an alias of some `val` variable. -->
一个“box”变量是局部不可变的。这意味着它可能通过其他变量(' trn '或' ref ')发生突变，但也有可能我们的' box '变量是某个' val '变量的别名。

<!-- When we read a field, we need to return a reference capability that is compatible with the field but is also locally immutable. -->
当我们读取一个字段时，我们需要返回一个与该字段兼容但也是局部不可变的引用权能。

<!-- An `iso` field is returned as a `tag` because no locally immutable reference capability can maintain its isolation guarantees. A `val` field is returned as a `val` because global immutability is a stronger guarantee than local immutability. A `box` field makes the same local immutability guarantee as its origin, so that's also fine. -->
“iso”字段作为“标记”返回，因为没有本地不可变引用权能可以维护它的隔离保证。“val”字段作为“val”返回，因为全局不变性比局部不变性更能保证全局不变性。一个“box”字段与它的起源具有相同的局部不变性保证，所以这也是可以的。

<!-- For `trn` and `ref` we need to return a locally immutable reference capability that doesn't violate any guarantees the field makes. In both cases, we can return `box`. -->
一个“box”变量是局部不可变的。这意味着它可能通过其他变量(' trn '或' ref ')发生突变，但也有可能我们的' box '变量是某个' val '变量的别名。

<!-- ### Reading from a `tag` variable -->
### 读取`tag`变量

<!-- This one is easy: `tag` variables are opaque! They can't be read from. -->
这个很简单:' tag '变量是不透明的!它们不能被读出来。

<!-- ## Writing to the field of an object -->
## 为对象的字段赋值

<!-- Like reading the field of an object, writing to a field depends on the reference capability of the object reference being stored and the reference capability of the origin object containing the field. The reference capability of the object being stored must not violate the guarantees made by the origin object's reference capability. For example, a val object reference can be stored in an iso origin. This is because the val reference capability guarantees that no alias to that object exists which could violate the guarantees that the iso capability makes. -->
与读取字段一样，字段的赋值取决于存储的对象使用的`引用权能`和包含该字段的对象所属类型的`引用权能`类型。存储的对象的引用权能不能违反源对象的引用权能所作的保证。例如，val对象引用可以存储在iso源文件中。这是因为val引用权能保证不存在任何可能违反iso权能保证的对象别名。

<!-- Here's a simplified version of the table above that shows which reference capabilities can be stored in the field of an origin object. -->
下面是上面表格的简化版本，它显示了哪些引用权能可以存储在原始对象的字段中。

---

| &#x25C1;       | iso object | trn object | ref object | val object | box object | tag object |
|----------------|------------|------------|------------|------------|------------|------------|
| __iso origin__ | &#x2714;   |            |            | &#x2714;   |            | &#x2714;   |
| __trn origin__ | &#x2714;   | &#x2714;   |            | &#x2714;   |            | &#x2714;   |
| __ref origin__ | &#x2714;   | &#x2714;   | &#x2714;   | &#x2714;   | &#x2714;   | &#x2714;   |
| __val origin__ |            |            |            |            |            |            |
| __box origin__ |            |            |            |            |            |            |
| __tag origin__ |            |            |            |            |            |            |

---

<!-- The bottom half of this chart is empty, since only origins with a mutable capability can have their fields modified. -->
这个图表的下半部分是空的，因为只有具有可变权能的起源才能修改它们的字段。
