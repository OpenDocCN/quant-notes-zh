# Python金融量化投资分析与股票交易：P25：pandas常用函数 📊

在本节课中，我们将学习pandas库中一些常用的数据处理函数，包括计算平均值、求和以及数据排序。掌握这些函数是进行金融数据清洗和分析的基础。

## 平均值计算

![](img/eaf0148ea73ab8b52f993f90f51e96c0_1.png)

上一节我们介绍了如何处理缺失值，本节中我们来看看如何计算数据的平均值。pandas中的`.mean()`方法用于计算平均值。

![](img/eaf0148ea73ab8b52f993f90f51e96c0_3.png)

对于DataFrame对象，`.mean()`方法默认按列计算平均值，返回一个Series对象，其中包含每一列的平均值。例如，对于一个包含两列的DataFrame，`.mean()`会分别计算两列的平均值。

**代码示例**：
```python
import pandas as pd
import numpy as np

# 创建一个示例DataFrame，包含缺失值
data = {'one': [5, np.nan, 7, 6],
        'two': [1, 2, 3, 4]}
df = pd.DataFrame(data, index=['a', 'b', 'c', 'd'])
print(df)

# 计算每列的平均值
column_mean = df.mean()
print("按列计算的平均值：")
print(column_mean)
```

如果希望按行计算平均值，需要指定参数`axis=1`。

**代码示例**：
```python
# 计算每行的平均值
row_mean = df.mean(axis=1)
print("按行计算的平均值：")
print(row_mean)
```

## 求和计算

与求平均值类似，`.sum()`方法用于计算数据的总和。默认情况下，它也是按列进行求和。

**代码示例**：
```python
# 计算每列的总和
column_sum = df.sum()
print("按列计算的总和：")
print(column_sum)

# 计算每行的总和
row_sum = df.sum(axis=1)
print("按行计算的总和：")
print(row_sum)
```

## 数据排序

pandas提供了两种主要的排序方式：按值排序和按索引排序。

### 按值排序

按值排序使用`.sort_values()`方法。你需要通过`by`参数指定按照哪一列的值进行排序。

以下是按值排序的步骤：
1.  使用`by`参数指定排序依据的列名。
2.  使用`ascending`参数控制升序（`True`）或降序（`False`）。

**代码示例**：
```python
# 按‘two’列的值进行升序排序
sorted_by_value_asc = df.sort_values(by='two', ascending=True)
print("按‘two’列升序排序：")
print(sorted_by_value_asc)

# 按‘two’列的值进行降序排序
sorted_by_value_desc = df.sort_values(by='two', ascending=False)
print("按‘two’列降序排序：")
print(sorted_by_value_desc)
```

当排序依据的列存在缺失值（NaN）时，包含缺失值的行不会参与排序比较，它们会被统一放置在结果的最后。

### 按索引排序

按索引排序使用`.sort_index()`方法。它可以对行索引或列索引进行排序。

以下是按索引排序的说明：
*   对行索引排序：直接调用`.sort_index()`，默认对行标签（如‘a’， ‘b’， ‘c’， ‘d’）进行排序。
*   对列索引排序：调用`.sort_index(axis=1)`，对列名（如‘one’， ‘two’）进行排序。

**代码示例**：
```python
# 按行索引降序排序
sorted_by_index_row = df.sort_index(ascending=False)
print("按行索引降序排序：")
print(sorted_by_index_row)

# 按列索引降序排序
sorted_by_index_column = df.sort_index(axis=1, ascending=False)
print("按列索引降序排序：")
print(sorted_by_index_column)
```

## 其他通用函数

除了上述方法，之前在NumPy中学习过的许多通用函数同样适用于pandas，例如计算标准差（`.std()`）、方差（`.var()`）、最大值（`.max()`）和最小值（`.min()`）等。它们的调用方式与`.mean()`和`.sum()`类似。

**代码示例**：
```python
# 计算标准差
print("每列的标准差：")
print(df.std())

# 计算最大值
print("每列的最大值：")
print(df.max())
```

![](img/eaf0148ea73ab8b52f993f90f51e96c0_5.png)

本节课中我们一起学习了pandas中几个核心的数据处理函数：计算平均值的`.mean()`、计算总和的`.sum()`，以及用于数据排序的`.sort_values()`和`.sort_index()`。理解并熟练运用这些函数，能够帮助你高效地完成金融数据的初步整理与分析，为后续更复杂的量化策略打下坚实基础。