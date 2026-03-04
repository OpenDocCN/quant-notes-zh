# 量化交易Python入门之数据分析：2-2：Pandas DataFrame 📊

在本节课中，我们将要学习Pandas库的核心数据结构——DataFrame。DataFrame类似于Excel表格，是一种二维的、大小可变且可以包含异构类型列的表格型数据结构。我们将学习如何创建DataFrame，以及如何对其进行索引、切片、转置和增删列等基本操作。

---

上一节我们介绍了Pandas Series，它是一种一维数据结构。本节中我们来看看如何将多个Series组合成二维的表格数据，即DataFrame。

## 创建DataFrame

首先，我们需要创建三个Pandas Series，分别代表顾客姓名、购买物品和价格。

```python
import pandas as pd

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_1.png)

purchase_1 = pd.Series({'Name': 'John', 'Item': 'Keyboard', 'Cost': 105})
purchase_2 = pd.Series({'Name': 'Mary', 'Item': 'Mouse', 'Cost': 25})
purchase_3 = pd.Series({'Name': 'Scott', 'Item': 'Monitor', 'Cost': 599.5})
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_3.png)

为了将这些单列数据组合成一个类似Excel的面板数据，我们使用 `pd.DataFrame()` 函数。通常将DataFrame对象命名为 `df`。

```python
df = pd.DataFrame([purchase_1, purchase_2, purchase_3], index=['Store 1', 'Store 1', 'Store 2'])
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_5.png)

这段代码创建了一个DataFrame。`index` 参数用于指定每一行数据的索引标签。这里，John和Mary在Store 1购物，而Scott在Store 2购物。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_7.png)

执行后，`df` 的内容如下：

|         | Name  | Item     | Cost  |
| :------ | :---- | :------- | :---- |
| Store 1 | John  | Keyboard | 105   |
| Store 1 | Mary  | Mouse    | 25    |
| Store 2 | Scott | Monitor  | 599.5 |

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_9.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_10.png)

## 数据查询与定位

创建DataFrame后，我们需要从中提取特定数据。以下是几种常用的定位方法。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_12.png)

### 使用 `.loc[]` 按标签索引

`.loc[]` 主要用于通过行和列的**标签名**进行数据选择。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_14.png)

如果想查询在 `Store 2` 发生的交易：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_15.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_16.png)

```python
df.loc['Store 2']
```
输出将是一个Series，包含Scott的购买信息。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_17.png)

如果想查询 `Store 1` 的所有交易：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_19.png)

```python
df.loc['Store 1']
```
输出将是一个DataFrame，包含John和Mary的购买信息。当 `.loc[]` 返回多行数据时，其类型是DataFrame；返回单行时，类型是Series。

### 使用 `.iloc[]` 按位置索引

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_21.png)

`.iloc[]` 通过行和列的**整数位置**（从0开始）进行数据选择。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_22.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_23.png)

例如，要选择第一行（索引0）的数据：

```python
df.iloc[0]
```
要选择第一行、第三列（`Cost`列，索引2）的具体数值：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_25.png)

```python
df.iloc[0, 2]  # 输出: 105.0
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_26.png)

### 直接按列名选择

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_28.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_29.png)

我们也可以直接通过列名来选择整列数据。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_31.png)

例如，选择所有价格数据：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_33.png)

```python
df['Cost']
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_35.png)

### 组合选择：选择特定行和列

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_37.png)

以下是选择所有行，但只选择 `Name` 和 `Cost` 两列的方法：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_39.png)

```python
df.loc[:, ['Name', 'Cost']]
```
这里的冒号 `:` 表示选择所有行。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_41.png)

如果想选择 `Store 1` 的 `Name` 和 `Cost` 信息：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_43.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_45.png)

```python
df.loc['Store 1', ['Name', 'Cost']]
```

## 数据操作

除了查询，我们还可以对DataFrame进行变形和修改。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_47.png)

### 转置 DataFrame

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_49.png)

使用 `.T` 属性可以对DataFrame进行转置，即行变列，列变行。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_51.png)

```python
df_transposed = df.T
```
转置后，原来的行索引（Store 1, Store 2）变成列名，原来的列名（Name, Item, Cost）变成行索引。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_53.png)

### 删除行或列

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_55.png)

使用 `.drop()` 方法可以删除指定的行或列。**默认情况下，`.drop()` 返回一个新对象，而不改变原DataFrame。**

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_57.png)

删除 `Store 1` 这一行：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_59.png)

```python
# 返回删除后的新DataFrame，原df不变
new_df = df.drop('Store 1')
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_61.png)

删除 `Item` 这一列（需要指定 `axis=1` 或 `axis='columns'`）：

```python
# 返回删除列后的新DataFrame
new_df = df.drop('Item', axis=1)
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_63.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_64.png)

**注意**：如果希望直接在原DataFrame上修改，需要设置 `inplace=True` 参数，或者将结果赋值回原变量。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_66.png)

```python
df.drop('Item', axis=1, inplace=True)  # 方式一：原地修改
# 或
df = df.drop('Item', axis=1)           # 方式二：重新赋值
```

### 添加新列

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_68.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_69.png)

为DataFrame添加新列非常简单，只需像字典赋值一样操作即可。

例如，添加一个名为 `Location` 的新列，并暂时用 `None` 填充：

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_71.png)

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_73.png)

```python
df['Location'] = None
```
执行后，`df` 会增加一列全为 `None` 的 `Location` 列。

### 使用 `.copy()` 进行深度拷贝

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_75.png)

在对DataFrame进行操作时，有时我们需要保留原始数据。直接赋值（如 `df2 = df`）创建的是浅拷贝，修改 `df2` 可能会影响 `df`。为了创建完全独立的副本，应使用 `.copy()` 方法进行深度拷贝。

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_77.png)

```python
df_copy = df.copy()
# 现在对 df_copy 的任何操作都不会影响原始的 df
df_copy.drop('Store 1', inplace=True)
```

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_78.png)

---

![](img/57d7b6c45ec4d2b7fb41327cbf5fb818_80.png)

本节课中我们一起学习了Pandas DataFrame的基础知识。我们掌握了如何从Series创建DataFrame，如何使用 `.loc[]` 和 `.iloc[]` 进行数据定位与切片，以及如何对DataFrame进行转置、删除行列和添加新列等基本操作。理解并熟练运用DataFrame是进行金融数据分析的关键一步。在下一节中，我们将学习如何对DataFrame中的数据进行更复杂的清洗与处理。