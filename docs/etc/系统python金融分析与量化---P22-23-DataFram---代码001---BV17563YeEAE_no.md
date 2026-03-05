# 量化交易教程：P22：DataFrame常用属性

在本节课中，我们将学习Pandas中DataFrame对象的一些常用属性。这些属性可以帮助我们快速了解DataFrame的结构和数据概况。

上一节我们介绍了DataFrame对象的创建方式，本节中我们来看看它有哪些常用的属性。

## 常用属性详解

与Series对象类似，DataFrame也有`index`和`values`属性。

*   **`index`**：此属性用于获取DataFrame的行索引。行索引是数据最左侧垂直排列的标签。
*   **`values`**：此属性用于获取DataFrame中的值。但与Series返回一维数组不同，DataFrame的`values`属性返回的是一个二维数组，其中每一行数据构成一个一维数组。

以下是演示代码：

```python
import pandas as pd
import numpy as np

# 创建一个示例DataFrame
data = {'one': [1, 2, np.nan, 4], 'two': [5, 6, 7, 8]}
df = pd.DataFrame(data, index=['A', 'B', 'C', 'D'])
print("原始DataFrame:")
print(df)
print("\n行索引 (df.index):")
print(df.index)
print("\n值数组 (df.values):")
print(df.values)
print("值数组类型:", type(df.values))
```

除了上述属性，DataFrame作为二维数据结构，还有其特有的属性。

*   **`columns`**：此属性用于获取DataFrame的列索引，即数据顶部的水平标签。
*   **`T`**：此属性表示转置。它将DataFrame的行和列进行交换，行变为列，列变为行。其公式表示为：若原DataFrame为 *D* (m×n)，则转置后为 *D^T* (n×m)。

以下是相关演示：

```python
print("\n列索引 (df.columns):")
print(df.columns)
print("\n转置 (df.T):")
print(df.T)
```

**注意**：在转置操作中，由于原DataFrame中`‘one’`列包含一个浮点类型的`NaN`（非数字）值，Pandas为了保证列内数据类型一致，会将整型数据自动转换为浮点型。因此转置后所有数值都显示为浮点数（如1.0, 2.0）。可以使用`astype`方法进行类型转换。

DataFrame还提供了一个非常有用的方法，用于快速获取数据的描述性统计信息。

*   **`describe()`**：这是一个方法（注意需要加括号调用），它返回一个DataFrame，其中包含了对原始DataFrame每一列（数值列）的多个统计指标。

以下是`describe()`方法的输出示例：

```python
print("\n描述性统计 (df.describe()):")
print(df.describe())
```

![](img/a727bba16627d1625c3510e06357bc36_1.png)

返回的统计信息通常包括：
*   `count`: 非空值的数量
*   `mean`: 平均值
*   `std`: 标准差
*   `min`: 最小值
*   `25%`: 第一四分位数
*   `50%`: 中位数
*   `75%`: 第三四分位数
*   `max`: 最大值

## 本节总结

![](img/a727bba16627d1625c3510e06357bc36_3.png)

本节课中我们一起学习了DataFrame对象的几个核心属性与方法：
1.  **`index`**：获取行索引。
2.  **`columns`**：获取列索引。
3.  **`values`**：以二维数组形式获取数据值。
4.  **`T`**：获取转置后的DataFrame。
5.  **`describe()`**：获取数据的描述性统计摘要。

![](img/a727bba16627d1625c3510e06357bc36_5.png)

掌握这些属性是进行数据查看和初步分析的基础，在后续的数据处理中会频繁使用。