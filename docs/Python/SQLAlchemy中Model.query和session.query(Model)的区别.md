
>今天在看以前的代码的时候发现了一个问题，就是在进行数据查询的时候有的地方用到的写法不一致，虽然处理的结果都没有问题，但是秉着一致的原则（也可能是强迫症...）还是决定将用法统一一下。
> 
>首先用到的两种写法分别是：
>1. result = SomeModel.query.filter(...)
>2. result = session.query(SomeModel).filter(...)
>
>由于当时写代码的时候也没有太在意，今天回过头来看时，发现它们两个还是有些差别的，正好趁着这个机会查了一下它们的区别，在此总结一下，以便之后复习回忆。

### 1. 首先我们假定有这么一个需求：

我们需要 Table_A、Table_B、Table_C多个表关联查询，但是我们最终只需要Table_A中的所有字段，这时该怎样进行查询？

**方式一：**

```
result = session.query(Model_A.col1,Model_A.col2... ...,Model_B).join(Model_B,Model_B.id == Model_A.id).filter(...)
```
这样你只需要把要查询的所有字段全部明确列出即可，但是这样可能会有个问题，如果表中的字段特别多的话那是不是要在这里写一大堆东西，这岂不是很麻烦？这时使用第二种方式会方便很多。

**方式二：**

```
result = Model_A.query.join(Model_B,Model_B.id == Model_A.id).filter(...)
```
这种方式会返回一个Model_A的对象，包含该表中所有字段，不包含其他表中的任何字段，而且你不需要再将需要的字段一一列出，是不是方便很多。
当然，你一可以根据需要只查询指定的字段，这时你可以使用下面这种方式：

**查询指定字段：**

```
result = Model_A.query.join(Model_B,Model_B.id == Model_A.id).filter(...).with_entities(Model_A.col1, Model_A.col2，Model_B.col1，Model_B.col2)
```
这种方式返回的一个元组