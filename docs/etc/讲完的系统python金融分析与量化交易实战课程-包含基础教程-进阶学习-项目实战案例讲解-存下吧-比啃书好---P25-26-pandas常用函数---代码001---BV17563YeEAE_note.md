# 量化交易教程：P25：pandas常用函数

## 概述
在本节课中，我们将学习pandas库中一些常用的数据处理函数，包括计算平均值、求和以及数据排序。这些功能是金融数据分析和量化交易中数据清洗与预处理的基础。

---

![](img/fe67af7b012c5eba8c0c31ac8d7945ab_1.png)

## 求平均值：`.mean()`方法

上一节我们介绍了如何处理缺失值，本节中我们来看看如何计算数据的平均值。

![](img/fe67af7b012c5eba8c0c31ac8d7945ab_3.png)

pandas的`.mean()`方法用于计算平均值。对于一个`DataFrame`对象，`.mean()`方法默认会对**每一列**分别计算平均值，并返回一个`Series`对象。

**公式/代码示例**：
```python
import pandas as pd
import numpy as np

# 创建一个包含缺失值的DataFrame
df = pd.DataFrame({
    ‘one‘: [5, 6, 7, np.nan],
    ‘two‘: [1, 2, 3, 4]
})

# 计算每列的平均值
column_means = df.mean()
print(column_means)
```
执行上述代码，`df.mean()`会忽略第一列中的缺失值`np.nan`，对`[5, 6, 7]`求平均得到`6.0`；对第二列`[1, 2, 3, 4]`求平均得到`2.5`。结果是一个包含两列平均值的`Series`。

如果想按行计算平均值，需要指定`axis`参数。

**公式/代码示例**：
```python
# 计算每行的平均值
row_means = df.mean(axis=1)
print(row_means)
```
此时，`.mean(axis=1)`会对每一行的数值进行平均计算。

---

## 求和：`.sum()`方法

了解了平均值计算后，求和是另一个基础且常用的操作。

`.sum()`方法用于对数据进行求和运算。与`.mean()`类似，默认按列求和（`axis=0`）。若需按行求和，则需设置参数`axis=1`。

**公式/代码示例**：
```python
# 按列求和
column_sums = df.sum()
print(column_sums)

# 按行求和
row_sums = df.sum(axis=1)
print(row_sums)
```

---

## 数据排序

数据排序是整理和分析数据的关键步骤。pandas提供了两种主要的排序方式：按值排序和按索引排序。

### 按值排序：`.sort_values()`

按值排序是指根据某一列或某一行具体的数值大小进行排序。

以下是`.sort_values()`方法的核心参数和使用步骤：

*   **`by`**：指定用于排序的列名（按列排序时）或索引（按行排序时）。
*   **`ascending`**：控制排序顺序。`True`为升序（默认），`False`为降序。
*   **`axis`**：指定排序的轴。`0`或`‘index‘`为按列排序，`1`或`‘columns‘`为按行排序。

**公式/代码示例（按列排序）**：
```python
# 按‘two‘列的值进行升序排序
df_sorted_by_column = df.sort_values(by=‘two‘)
print(df_sorted_by_column)

# 按‘two‘列的值进行降序排序
df_sorted_by_column_desc = df.sort_values(by=‘two‘, ascending=False)
print(df_sorted_by_column_desc)
```

**公式/代码示例（按行排序）**：
```python
# 按索引为‘A‘的这一行数值进行降序排序（假设df有行索引‘A‘, ‘B‘, ‘C‘, ‘D‘）
df_sorted_by_row = df.sort_values(by=‘A‘, axis=1, ascending=False)
print(df_sorted_by_row)
```
按行排序在实际应用中较少使用，它意味着根据指定行的数值大小来调整列的排列顺序。

**处理缺失值**：当排序依据的列中存在缺失值`NaN`时，包含`NaN`的行不会参与排序比较，无论升序降序，它们都会被统一放置在结果的最后。

### 按索引排序：`.sort_index()`

按索引排序则是根据行索引或列索引的标签顺序进行排序。

**公式/代码示例**：
```python
# 按行索引标签排序（例如A, B, C, D）
df_sorted_by_index = df.sort_index()
print(df_sorted_by_index)

# 按行索引标签降序排序
df_sorted_by_index_desc = df.sort_index(ascending=False)
print(df_sorted_by_index_desc)

# 按列索引标签排序（例如‘one‘, ‘two‘）
df_sorted_by_columns = df.sort_index(axis=1)
print(df_sorted_by_columns)
```
例如，如果列索引是`[‘two‘, ‘one‘]`，按列索引升序排序后，列的顺序会变为`[‘one‘, ‘two‘]`。

---

## 其他通用统计函数

除了本节课重点介绍的方法，之前在NumPy中学过的许多通用统计函数同样适用于pandas。

以下是部分同样可用的函数列表：

*   `.std()`：计算标准差
*   `.var()`：计算方差
*   `.max()`：查找最大值
*   `.min()`：查找最小值
*   `.median()`：计算中位数

这些函数的使用方式与`.mean()`和`.sum()`类似，默认按列计算，也可以通过`axis`参数指定计算方向。

---

![](img/fe67af7b012c5eba8c0c31ac8d7945ab_5.png)

## 总结
本节课我们一起学习了pandas中几个核心的常用函数。我们掌握了如何使用`.mean()`和`.sum()`进行平均值与求和计算，并理解了`axis`参数如何控制计算方向（按行或按列）。接着，我们深入探讨了两种数据排序方法：`.sort_values()`用于根据数据值排序，`.sort_index()`用于根据索引标签排序，并学会了如何处理排序中的缺失值。这些函数是构建数据分析和量化交易策略的重要基石。