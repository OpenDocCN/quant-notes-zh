# 数据分析+金融量化+数据清洗：P40：DataFrame合并-02 📊

![](img/f1fba616fb5a0b31b0a0b2b927313f40_1.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_3.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_4.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_6.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_8.png)

在本节课中，我们将深入学习Pandas中`merge`函数的高级用法和关键参数。我们将探讨合并的前提条件、不同合并方式的含义，以及如何处理列标签不同或涉及行索引的复杂合并场景。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_10.png)

## 合并的前提条件

![](img/f1fba616fb5a0b31b0a0b2b927313f40_12.png)

上一节我们介绍了`merge`函数的基本用法，本节中我们来看看使用`merge`进行数据合并时必须满足的几个前提条件。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_14.png)

*   `merge`函数只支持基于列的合并。所有合并操作都以列为基准进行。
*   参与合并的列必须满足**一对一**、**一对多**或**多对多**关系中的至少一种。
*   参与合并的列应优先选择**离散型数据**（如分类、ID），而非连续型数据（如温度、股价）。离散型数据的取值是固定的几个类别，而连续型数据可以在一个范围内随机取值。

## 核心参数详解

![](img/f1fba616fb5a0b31b0a0b2b927313f40_16.png)

理解了合并的前提后，我们来看看`merge`函数中几个决定合并行为的关键参数。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_18.png)

### 合并方式：`how`参数

![](img/f1fba616fb5a0b31b0a0b2b927313f40_20.png)

`how`参数决定了最终的合并方式，即如何组合两个表格的数据。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_22.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_24.png)

以下是`how`参数的主要选项：
*   `inner`：内连接，只保留两个表格中**键值匹配的行**（取交集）。
*   `outer`：外连接，保留两个表格中**所有的行**，不匹配的位置用NaN填充（取并集）。
*   `left`：左连接，以**左表**为基准，保留其所有行，右表只保留匹配的行。
*   `right`：右连接，以**右表**为基准，保留其所有行，左表只保留匹配的行。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_26.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_28.png)

### 指定合并列：`on`参数

![](img/f1fba616fb5a0b31b0a0b2b927313f40_30.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_32.png)

`on`参数用于通过列标签来指定参与合并的列。当两个表格中有**多列标签相同**，但您只想选择其中部分列进行合并时，可以使用`on`参数来指定。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_34.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_36.png)

```python
pd.merge(df_left, df_right, on='key_column')
pd.merge(df_left, df_right, on=['key1', 'key2']) # 指定多个列
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_38.png)

### 分别指定左右合并列：`left_on`与`right_on`

![](img/f1fba616fb5a0b31b0a0b2b927313f40_40.png)

当两个表格中参与合并的**列标签不同**时，需要使用`left_on`和`right_on`参数分别指定。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_42.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_44.png)

```python
pd.merge(df_left, df_right, left_on='left_key', right_on='right_key')
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_46.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_48.png)

### 处理重复列名：`suffixes`参数

![](img/f1fba616fb5a0b31b0a0b2b927313f40_50.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_51.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_52.png)

当两个表格中存在**列标签相同但未参与合并的列**时，`suffixes`参数可以为这些列添加后缀以区分。它常与`on`参数结合使用。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_54.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_55.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_57.png)

```python
pd.merge(df_left, df_right, on='key', suffixes=('_left', '_right'))
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_59.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_61.png)

### 使用索引进行合并：`left_index`与`right_index`

![](img/f1fba616fb5a0b31b0a0b2b927313f40_63.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_65.png)

有时，一个表格的合并键是它的**行索引**，而不是某一列。这时就需要使用`left_index`或`right_index`参数。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_67.png)

首先，我们可以使用`set_index`方法将某一列设置为行索引：
```python
df.set_index('column_name', inplace=True)
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_69.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_71.png)

然后，在合并时，如果右表的合并键是索引，可以这样操作：
```python
pd.merge(df_left, df_right, left_on='key_column', right_index=True)
```
同理，如果左表的合并键是索引，则使用`left_index=True`。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_73.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_75.png)

## 实战练习

![](img/f1fba616fb5a0b31b0a0b2b927313f40_77.png)

下面我们通过几个例子来巩固所学的参数用法。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_79.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_81.png)

### 练习1：合并同名学生的成绩

![](img/f1fba616fb5a0b31b0a0b2b927313f40_83.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_85.png)

假设有两个班级的成绩单，都有“张三”和“李四”，但成绩不同，需要根据“张三”的成绩进行合并，并为两个“李四”的成绩添加后缀。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_87.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_89.png)

```python
import pandas as pd

![](img/f1fba616fb5a0b31b0a0b2b927313f40_90.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_91.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_93.png)

# 构造两个DataFrame
df1 = pd.DataFrame({'张三': [150, 148, 130], '李四': [110, 123, 145]})
df2 = pd.DataFrame({'张三': [150, 148, 130], '李四': [34, 23, 45]})

![](img/f1fba616fb5a0b31b0a0b2b927313f40_95.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_97.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_99.png)

# 根据‘张三’列合并，并为两个‘李四’列添加后缀
result = pd.merge(df1, df2, on='张三', suffixes=('_一班', '_二班'))
print(result)
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_101.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_103.png)

### 练习2：合并包含新学生的成绩单

![](img/f1fba616fb5a0b31b0a0b2b927313f40_105.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_107.png)

第一张表有张三、李四、王老五的成绩，第二张表有张三和赵小六的成绩。需要将赵小六合并到总表中。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_109.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_111.png)

```python
df3 = pd.DataFrame({'张三': [150, 148, 130], '李四': [110, 123, 145], '王老五': [90, 95, 100]})
df4 = pd.DataFrame({'张三': [150, 148, 130], '赵小六': [88, 92, 85]})

![](img/f1fba616fb5a0b31b0a0b2b927313f40_113.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_115.png)

# 默认根据所有同名列（此处为‘张三’）合并
result2 = pd.merge(df3, df4)
print(result2)
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_117.png)

### 练习3：处理列名错误的合并

![](img/f1fba616fb5a0b31b0a0b2b927313f40_119.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_120.png)

如果第二张表中“张三”的名字被错误地打成了“张13”，我们需要指定左右表不同的列名进行合并，并在合并后删除错误的列名。

![](img/f1fba616fb5a0b31b0a0b2b927313f40_122.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_124.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_126.png)

```python
df5 = pd.DataFrame({'张三': [150, 148, 130], '李四': [110, 123, 145]})
df6 = pd.DataFrame({'张13': [150, 148, 130], '赵小六': [88, 92, 85]})

![](img/f1fba616fb5a0b31b0a0b2b927313f40_128.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_130.png)

# 使用 left_on 和 right_on 指定对应的列
result3 = pd.merge(df5, df6, left_on='张三', right_on='张13')

![](img/f1fba616fb5a0b31b0a0b2b927313f40_132.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_134.png)

# 删除错误的‘张13’列
result3.drop(labels='张13', axis=1, inplace=True)
print(result3)
```

![](img/f1fba616fb5a0b31b0a0b2b927313f40_136.png)

## 总结

![](img/f1fba616fb5a0b31b0a0b2b927313f40_138.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_140.png)

![](img/f1fba616fb5a0b31b0a0b2b927313f40_141.png)

本节课中我们一起学习了`DataFrame`合并操作的高级技巧。我们明确了`merge`函数使用的三个前提条件，并深入讲解了`how`、`on`、`left_on/right_on`、`suffixes`以及`left_index/right_index`这几个核心参数的作用和适用场景。通过三个实战练习，我们演练了如何根据指定列合并、如何处理重复列名以及如何纠正列名错误进行合并。掌握这些参数，能够帮助你灵活应对实际数据分析中各种复杂的表格合并需求。