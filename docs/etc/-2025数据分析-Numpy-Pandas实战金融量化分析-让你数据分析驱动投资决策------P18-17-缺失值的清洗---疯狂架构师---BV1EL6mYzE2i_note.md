# 2025数据分析：P18：17.缺失值的清洗 🧹

![](img/a1c6183b1ed856cab25db61b1a7574b1_1.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_3.png)

在本节课中，我们将要学习在数据分析中如何处理缺失值。数据清洗是数据分析的必要前提，因为原始数据中可能存在的缺失值、重复值和异常值会干扰最终的分析结果。本节课我们将重点探讨缺失值的识别与清洗方法。

## 数据清洗概述

![](img/a1c6183b1ed856cab25db61b1a7574b1_5.png)

数据清洗是使用pandas模块进行数据分析时常用的操作。原始数据中可能存在缺失值，即空值。在某些分析需求下，这些空值没有意义，反而会干扰分析结果的准确性。因此，我们需要对原始数据进行清洗。

![](img/a1c6183b1ed856cab25db61b1a7574b1_7.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_9.png)

除了缺失值，原始数据中还可能存在重复数据和异常值。重复值没有必要进行多次分析，需要过滤掉。异常值由于数据采集手段不同而产生，同样会干扰分析结论。因此，在pandas数据清洗中，我们需要处理以下三种类型的值：
1.  缺失值
2.  重复值
3.  异常值

![](img/a1c6183b1ed856cab25db61b1a7574b1_11.png)

上一节我们介绍了数据清洗的必要性，本节中我们来看看如何具体处理缺失值。

![](img/a1c6183b1ed856cab25db61b1a7574b1_13.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_15.png)

## 缺失值的类型：NaN vs None

![](img/a1c6183b1ed856cab25db61b1a7574b1_17.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_19.png)

首先，我们需要了解数据分析中使用的空值类型。在Python中，有两种常见的空值表示：`None`和`NaN`。

`None`是一个Python对象，其类型为`NoneType`。在数据分析中，我们主要使用`NaN`（Not a Number），它来自NumPy库，类型为浮点型（`float`）。

![](img/a1c6183b1ed856cab25db61b1a7574b1_21.png)

以下是两种空值的区别演示：

![](img/a1c6183b1ed856cab25db61b1a7574b1_23.png)

```python
import numpy as np

# 检查None的类型
print(type(None))  # 输出: <class 'NoneType'>

![](img/a1c6183b1ed856cab25db61b1a7574b1_25.png)

# 检查NaN的类型
print(type(np.nan))  # 输出: <class 'float'>
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_27.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_29.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_31.png)

为什么数据分析中要使用`NaN`而不是`None`呢？关键在于它们参与运算的能力。

![](img/a1c6183b1ed856cab25db61b1a7574b1_33.png)

```python
# None 无法参与算术运算
result = None + 1  # 会引发 TypeError

![](img/a1c6183b1ed856cab25db61b1a7574b1_35.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_37.png)

# NaN 可以参与算术运算，但结果仍是 NaN
result = np.nan + 1  # 结果为 nan
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_39.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_41.png)

在数据分析中，常常需要对原始数据进行各种运算。如果原始数据中的空值是`NaN`形式，它不会中断整个运算流程，运算结果会返回`NaN`。而如果空值是`None`形式，则在运算过程中会引发错误，导致运算中断。

![](img/a1c6183b1ed856cab25db61b1a7574b1_43.png)

**核心概念**：在pandas中，如果遇到了`None`形式的空值，pandas会自动将其强制转换为`NaN`形式。因此，我们无需手动转换，但理解这两种空的本质区别很重要。

![](img/a1c6183b1ed856cab25db61b1a7574b1_45.png)

## 缺失值的清洗方法

![](img/a1c6183b1ed856cab25db61b1a7574b1_47.png)

现在，我们来看如何在pandas中清洗缺失值。首先，我们创建一组包含缺失值的示例数据。

![](img/a1c6183b1ed856cab25db61b1a7574b1_49.png)

```python
import pandas as pd
import numpy as np
from pandas import DataFrame, Series

# 创建一组8行6列的随机整数数据
df = DataFrame(np.random.randint(0, 100, size=(8, 6)))

![](img/a1c6183b1ed856cab25db61b1a7574b1_51.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_53.png)

# 人为制造三个缺失值
df.iloc[2, 3] = None        # 第2行，第3列
df.iloc[4, 4] = np.nan      # 第4行，第4列
df.iloc[5, 2] = None        # 第5行，第2列

print(df)
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_55.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_57.png)

### 方法一：删除缺失值所在的行

![](img/a1c6183b1ed856cab25db61b1a7574b1_59.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_61.png)

对缺失值进行过滤，通常指的是删除包含缺失值的整行数据。在DataFrame中，我们通常删除行而不是列，以保持数据结构（类似于数据库表）的稳定。

![](img/a1c6183b1ed856cab25db61b1a7574b1_63.png)

以下是实现删除的步骤，主要使用`isnull()`、`notnull()`结合`any()`或`all()`方法。

![](img/a1c6183b1ed856cab25db61b1a7574b1_65.png)

首先，使用`isnull()`检测缺失值：

![](img/a1c6183b1ed856cab25db61b1a7574b1_67.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_69.png)

```python
# 检测每个元素是否为缺失值，返回布尔型DataFrame
print(df.isnull())
```

`isnull()`返回一个布尔值DataFrame，其中`True`表示该位置是缺失值。为了找出哪些行包含缺失值，我们需要在行方向上进行判断。

```python
# 使用 any(axis=1) 检查每一行中是否存在 True（即缺失值）
# 如果某行存在至少一个True，则返回True
rows_with_nan = df.isnull().any(axis=1)
print(rows_with_nan)
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_71.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_73.png)

返回的布尔序列中，`True`对应的行索引就是包含缺失值的行。我们可以直接利用这个布尔序列进行索引，来查看这些行：

![](img/a1c6183b1ed856cab25db61b1a7574b1_75.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_77.png)

```python
# 查看包含缺失值的行
print(df.loc[rows_with_nan])
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_79.png)

要删除这些行，可以使用`drop`方法：

![](img/a1c6183b1ed856cab25db61b1a7574b1_81.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_83.png)

```python
# 获取要删除的行索引
index_to_drop = df.loc[rows_with_nan].index
# 删除这些行
df_dropped = df.drop(labels=index_to_drop, axis=0)
print(df_dropped)
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_85.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_87.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_89.png)

**固定搭配**：
*   `df.isnull().any(axis=1)`：用于找出包含缺失值的行。
*   `df.notnull().all(axis=1)`：作用相同，用于找出不全是非空值的行（即包含缺失值的行）。`notnull()`返回`False`的位置是缺失值，`all()`要求整行都为`True`才返回`True`，因此包含`False`的行会返回`False`。

![](img/a1c6183b1ed856cab25db61b1a7574b1_91.png)

```python
# 使用 notnull() 和 all() 达到同样效果
rows_to_keep = df.notnull().all(axis=1) # False对应的行是含缺失值的行
df_cleaned = df.loc[rows_to_keep]
print(df_cleaned)
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_93.png)

### 方法二：使用 dropna() 函数直接删除

![](img/a1c6183b1ed856cab25db61b1a7574b1_95.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_97.png)

pandas提供了一个更简便的函数`dropna()`来直接删除缺失值。

![](img/a1c6183b1ed856cab25db61b1a7574b1_99.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_101.png)

```python
# 删除包含缺失值的行 (axis=0 表示行)
df_cleaned_simple = df.dropna(axis=0)
print(df_cleaned_simple)

![](img/a1c6183b1ed856cab25db61b1a7574b1_103.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_105.png)

# 删除包含缺失值的列 (axis=1 表示列)
# df_cleaned_cols = df.dropna(axis=1)
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_107.png)

`dropna()`函数一步到位，是处理缺失值最直接的方法。但理解方法一中的`isnull()`、`any()`等操作对于深入理解数据筛选逻辑很有帮助。

![](img/a1c6183b1ed856cab25db61b1a7574b1_109.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_111.png)

### 方法三：填充缺失值

![](img/a1c6183b1ed856cab25db61b1a7574b1_113.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_115.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_117.png)

除了删除，另一种处理缺失值的方法是填充。使用`fillna()`函数可以用指定的值替换缺失值。

![](img/a1c6183b1ed856cab25db61b1a7574b1_119.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_121.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_123.png)

最简单的填充是用一个固定值：

![](img/a1c6183b1ed856cab25db61b1a7574b1_125.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_127.png)

```python
# 用固定值666填充所有缺失值
df_filled = df.fillna(value=666)
print(df_filled)
```

![](img/a1c6183b1ed856cab25db61b1a7574b1_129.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_131.png)

然而，用固定值填充往往缺乏合理性。更常见的做法是使用相邻的值进行填充，这称为前向填充或后向填充。

![](img/a1c6183b1ed856cab25db61b1a7574b1_133.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_135.png)

```python
# 前向填充：用缺失值前一个有效值填充 (method='ffill' 或 ‘pad’)
# axis=0 表示沿列方向（垂直方向）寻找前一个值
df_ffill = df.fillna(method='ffill', axis=0)
print(df_ffill)

# 后向填充：用缺失值后一个有效值填充 (method='bfill' 或 ‘backfill’)
df_bfill = df.fillna(method='bfill', axis=0)
print(df_bfill)

# 也可以沿行方向（水平方向）填充
df_ffill_row = df.fillna(method='ffill', axis=1)
print(df_ffill_row)
```

**何时选择删除或填充？**
*   **删除**：当缺失值数量较少，删除后对整体数据量影响不大时，优先选择删除。这是最干净的处理方式。
*   **填充**：当缺失值较多，直接删除会导致数据量损失过大（成本太高）时，可以考虑填充。填充时，根据数据特性选择合理的策略（如用均值、中位数、前后值填充）。

## 总结

本节课中我们一起学习了数据清洗中缺失值的处理方法。

![](img/a1c6183b1ed856cab25db61b1a7574b1_137.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_139.png)

我们首先区分了`NaN`和`None`两种空值，理解了在数据分析中使用`NaN`的原因是其可以参与运算而不中断流程。然后，我们重点探讨了三种处理缺失值的方法：
1.  **删除**：使用`isnull().any()`或`notnull().all()`定位并删除包含缺失值的行，或直接使用便捷的`dropna()`函数。
2.  **填充**：使用`fillna()`函数，可以用固定值填充，也可以用前向(`ffill`)/后向(`bfill`)填充等更合理的方式。

![](img/a1c6183b1ed856cab25db61b1a7574b1_141.png)

![](img/a1c6183b1ed856cab25db61b1a7574b1_143.png)

核心在于根据数据情况选择策略：删除成本低时优先删除，删除成本高时考虑填充。掌握这些方法，你就能有效地清洗数据中的缺失值，为后续的数据分析打下坚实的基础。