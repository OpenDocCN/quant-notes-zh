# Pandas基础（三）：数据处理常用操作 🐼

![](img/13419275b26b0adb4a71a494a0ba9a7a_1.png)

在本节课中，我们将学习Pandas中数据处理的两个核心操作：数据筛选与数据合并。掌握这些技能对于高效地组织和分析数据至关重要。

![](img/13419275b26b0adb4a71a494a0ba9a7a_3.png)

## 数据筛选 🔍

![](img/13419275b26b0adb4a71a494a0ba9a7a_5.png)

上一节我们介绍了数据的基本结构，本节中我们来看看如何从DataFrame中筛选出我们需要的特定数据。

![](img/13419275b26b0adb4a71a494a0ba9a7a_7.png)

### 单条件筛选

以下代码用于筛选出DataFrame中`close`价格小于42000的所有行。

![](img/13419275b26b0adb4a71a494a0ba9a7a_9.png)

```python
df[df['close'] < 42000]
```

执行后，可以看到筛选出的数据其`close`价格均小于42000。

![](img/13419275b26b0adb4a71a494a0ba9a7a_11.png)

### 多条件组合筛选

![](img/13419275b26b0adb4a71a494a0ba9a7a_13.png)

我们也可以根据多个条件组合来进行筛选。例如，以下代码用于筛选同时满足两个条件的数据：`close`价格小于42000，并且`timestamp`大于“2024-01-29 01:00:00”。

```python
df[(df['close'] < 42000) & (df['timestamp'] > '2024-01-29 01:00:00')]
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_15.png)

执行后，成功筛选出同时符合这两个条件的三条数据。

## 数据合并 🤝

![](img/13419275b26b0adb4a71a494a0ba9a7a_17.png)

学会了数据筛选，接下来我们学习如何将不同的数据集合并起来，这在处理多源数据时非常有用。

![](img/13419275b26b0adb4a71a494a0ba9a7a_19.png)

### 垂直合并（Append）

垂直合并是指将两个DataFrame在行方向（上下）进行拼接。

![](img/13419275b26b0adb4a71a494a0ba9a7a_21.png)

首先，我们创建两个示例DataFrame：

```python
# 创建df1，包含原DataFrame的前10行中的三列
df1 = df.iloc[:10][['timestamp', 'close', 'volume']]

# 创建df2，包含原DataFrame第5到14行中的三列
df2 = df.iloc[5:15][['timestamp', 'close', 'volume']]
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_23.png)

在较新版本的Pandas中，`append`方法已被弃用。以下是两种推荐的垂直合并方法：

![](img/13419275b26b0adb4a71a494a0ba9a7a_25.png)

**方法一：使用带下划线的`_append`方法**
```python
df_combined = df1._append(df2)
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_27.png)

**方法二：使用`pd.concat`函数**
```python
df_combined = pd.concat([df1, df2])
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_29.png)

两种方法都可以实现上下拼接。

### 水平合并（Merge）

![](img/13419275b26b0adb4a71a494a0ba9a7a_31.png)

水平合并是指将两个DataFrame在列方向（左右）进行合并，通常基于某个共同的列（键）。

以下是一个`merge`操作的例子：

![](img/13419275b26b0adb4a71a494a0ba9a7a_33.png)

```python
df_merged = pd.merge(df1, df2, on='timestamp', suffixes=('_left', '_right'))
```

这段代码将`df1`和`df2`按照`timestamp`列进行左右合并。`suffixes`参数用于在左右DataFrame有同名列时，为它们添加不同的后缀（`_left`和`_right`）以避免混淆。

![](img/13419275b26b0adb4a71a494a0ba9a7a_35.png)

## 其他常用函数 ⚙️

除了筛选和合并，Pandas还提供了许多其他实用的数据处理函数。

### 重置索引

`reset_index`函数用于重置DataFrame的索引。
*   `inplace=True`：表示直接在原DataFrame上修改。
*   `drop=True`：表示丢弃旧的索引列，不将其作为新的一列保留。

![](img/13419275b26b0adb4a71a494a0ba9a7a_37.png)

```python
df.reset_index(inplace=True, drop=True)
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_39.png)

### 重命名列

![](img/13419275b26b0adb4a71a494a0ba9a7a_41.png)

`rename`函数用于重命名DataFrame的列名。它接受一个字典作为参数，其中键是原始列名，值是新列名。

![](img/13419275b26b0adb4a71a494a0ba9a7a_42.png)

```python
df.rename(columns={'timestamp': '时间戳', 'close': '收盘价', 'volume': '成交量'}, inplace=True)
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_44.png)

执行后，DataFrame的列名被成功更改为中文。

### 判断是否为空

![](img/13419275b26b0adb4a71a494a0ba9a7a_46.png)

`empty`属性用于判断DataFrame是否为空（即没有任何数据行），返回一个布尔值（True或False）。

![](img/13419275b26b0adb4a71a494a0ba9a7a_48.png)

```python
# 检查现有DataFrame
print(df.empty)  # 输出 False

# 创建一个空的DataFrame进行检查
empty_df = pd.DataFrame()
print(empty_df.empty)  # 输出 True
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_50.png)

### 转置数据

`T`属性（或`.transpose()`方法）用于对DataFrame进行转置操作。转置是线性代数中的概念，通俗地说，就是将行变成列，列变成行，实现数据的翻转。

![](img/13419275b26b0adb4a71a494a0ba9a7a_52.png)

```python
# 查看原始DataFrame
print(df.head())

![](img/13419275b26b0adb4a71a494a0ba9a7a_54.png)

# 进行转置操作
df_transposed = df.T
print(df_transposed.head())
```

![](img/13419275b26b0adb4a71a494a0ba9a7a_56.png)

转置后，可以清晰地看到原本的列名变成了转置后DataFrame的行索引。

![](img/13419275b26b0adb4a71a494a0ba9a7a_57.png)

---

![](img/13419275b26b0adb4a71a494a0ba9a7a_59.png)

本节课中我们一起学习了Pandas数据处理的核心操作。我们掌握了如何使用条件进行数据筛选，以及如何通过`_append`/`concat`和`merge`进行数据的垂直与水平合并。此外，我们还了解了`reset_index`、`rename`、`empty`和`T`等常用函数和属性的用法。这些工具将帮助你更灵活、高效地处理和准备数据，为后续的数据分析打下坚实基础。