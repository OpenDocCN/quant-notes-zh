# 数据分析之量化案例：P17：缺失值的清洗 📊

![](img/ca55439b951a72f3d4d111d25573dca0_1.png)

![](img/ca55439b951a72f3d4d111d25573dca0_3.png)

在本节课中，我们将要学习数据分析中一个至关重要的环节——数据清洗，特别是如何处理数据中的缺失值。数据清洗是确保分析结果准确可靠的必要前提。

![](img/ca55439b951a72f3d4d111d25573dca0_5.png)

## 概述：为什么需要数据清洗？

![](img/ca55439b951a72f3d4d111d25573dca0_7.png)

![](img/ca55439b951a72f3d4d111d25573dca0_9.png)

原始数据中常常会存在一些问题，这些问题会干扰我们最终的分析结果。数据清洗主要针对以下三种情况进行处理：
1.  **缺失值**：数据中的空值。
2.  **重复值**：完全相同的数据行。
3.  **异常值**：明显偏离正常范围的数据点。

![](img/ca55439b951a72f3d4d111d25573dca0_11.png)

![](img/ca55439b951a72f3d4d111d25573dca0_13.png)

本节课，我们将重点探讨第一种情况：缺失值的识别与处理。

![](img/ca55439b951a72f3d4d111d25573dca0_15.png)

![](img/ca55439b951a72f3d4d111d25573dca0_17.png)

## 缺失值的两种形式：`None` 与 `NaN`

![](img/ca55439b951a72f3d4d111d25573dca0_19.png)

在Python中，空值有两种常见表示：`None` 和 `NaN`。理解它们的区别对数据分析至关重要。

![](img/ca55439b951a72f3d4d111d25573dca0_21.png)

`None` 是一个Python对象，其类型为 `NoneType`。
```python
type(None)  # 输出：NoneType
```

![](img/ca55439b951a72f3d4d111d25573dca0_23.png)

`NaN`（Not a Number）则是一个特殊的浮点数，来自 `numpy` 库。
```python
import numpy as np
type(np.nan)  # 输出：float
```

![](img/ca55439b951a72f3d4d111d25573dca0_25.png)

### 为什么数据分析中常用 `NaN`？

![](img/ca55439b951a72f3d4d111d25573dca0_27.png)

![](img/ca55439b951a72f3d4d111d25573dca0_29.png)

![](img/ca55439b951a72f3d4d111d25573dca0_31.png)

关键在于它们参与运算的能力不同。`None` 是对象，无法直接参与数值运算。
```python
None + 1  # 会引发 TypeError
```

![](img/ca55439b951a72f3d4d111d25573dca0_33.png)

![](img/ca55439b951a72f3d4d111d25573dca0_35.png)

而 `NaN` 是浮点数，可以参与运算，但结果仍是 `NaN`，这不会中断整个计算流程。
```python
np.nan + 1  # 输出：nan
```

![](img/ca55439b951a72f3d4d111d25573dca0_37.png)

![](img/ca55439b951a72f3d4d111d25573dca0_39.png)

在数据分析中，我们经常需要对整列或整行数据进行运算。如果原始数据中的空值是 `NaN` 形式，运算过程可以继续而不会报错；如果是 `None`，则可能导致运算中断。因此，`NaN` 是更合适的选择。

![](img/ca55439b951a72f3d4d111d25573dca0_41.png)

![](img/ca55439b951a72f3d4d111d25573dca0_43.png)

> **注意**：在 `pandas` 中，如果遇到 `None` 形式的空值，`pandas` 会自动将其转换为 `NaN` 形式。但我们仍需理解这两种空值的本质区别。

![](img/ca55439b951a72f3d4d111d25573dca0_45.png)

## 实战：处理 `DataFrame` 中的缺失值

![](img/ca55439b951a72f3d4d111d25573dca0_47.png)

上一节我们介绍了缺失值的概念和 `NaN` 的重要性，本节中我们来看看如何在 `pandas` 的 `DataFrame` 中实际操作。

首先，我们导入必要的库并创建一组包含缺失值的示例数据。

![](img/ca55439b951a72f3d4d111d25573dca0_49.png)

![](img/ca55439b951a72f3d4d111d25573dca0_51.png)

```python
import pandas as pd
import numpy as np
from pandas import DataFrame, Series

![](img/ca55439b951a72f3d4d111d25573dca0_53.png)

![](img/ca55439b951a72f3d4d111d25573dca0_55.png)

# 创建一个8行6列的随机整数DataFrame
df = DataFrame(np.random.randint(0, 100, size=(8, 6)))
# 人为制造几个缺失值
df.iloc[2, 3] = None        # 第3行，第4列
df.iloc[4, 4] = np.nan      # 第5行，第5列
df.iloc[5, 2] = None        # 第6行，第3列
print(df)
```

![](img/ca55439b951a72f3d4d111d25573dca0_57.png)

![](img/ca55439b951a72f3d4d111d25573dca0_59.png)

![](img/ca55439b951a72f3d4d111d25573dca0_61.png)

现在，我们的 `df` 中已经有了三个缺失值（显示为 `NaN`）。

![](img/ca55439b951a72f3d4d111d25573dca0_63.png)

### 方法一：删除含有缺失值的行 🗑️

![](img/ca55439b951a72f3d4d111d25573dca0_65.png)

![](img/ca55439b951a72f3d4d111d25573dca0_67.png)

最直接的处理方式是将包含缺失值的整行数据删除。在 `pandas` 中，我们可以组合使用 `isnull()`、`notnull()`、`any()` 和 `all()` 方法来实现。

以下是操作步骤：

![](img/ca55439b951a72f3d4d111d25573dca0_69.png)

首先，使用 `isnull()` 检测每个值是否为空。
```python
df.isnull()
```

![](img/ca55439b951a72f3d4d111d25573dca0_71.png)

![](img/ca55439b951a72f3d4d111d25573dca0_73.png)

![](img/ca55439b951a72f3d4d111d25573dca0_75.png)

接着，结合 `any(axis=1)` 判断每一行中是否存在 `True`（即是否存在空值）。
```python
# 标记出包含空值的行
has_null = df.isnull().any(axis=1)
print(has_null)
```

![](img/ca55439b951a72f3d4d111d25573dca0_77.png)

然后，我们可以将这组布尔值作为行索引，筛选出需要删除的行。
```python
# 获取包含空值的行数据
rows_with_null = df.loc[has_null]
print(rows_with_null)
# 获取这些行的索引
index_to_drop = rows_with_null.index
print(index_to_drop)  # 例如 Int64Index([2, 4, 5], dtype='int64')
```

![](img/ca55439b951a72f3d4d111d25573dca0_79.png)

![](img/ca55439b951a72f3d4d111d25573dca0_81.png)

![](img/ca55439b951a72f3d4d111d25573dca0_83.png)

![](img/ca55439b951a72f3d4d111d25573dca0_85.png)

最后，使用 `drop` 方法删除这些行。
```python
df_cleaned = df.drop(labels=index_to_drop, axis=0)
print(df_cleaned)
```

![](img/ca55439b951a72f3d4d111d25573dca0_87.png)

> **小技巧**：`isnull()` 通常与 `any()` 搭配使用，而 `notnull()` 通常与 `all()` 搭配使用，来达到相同的筛选目的。

![](img/ca55439b951a72f3d4d111d25573dca0_89.png)

![](img/ca55439b951a72f3d4d111d25573dca0_91.png)

### 方法二：使用 `dropna()` 快速删除 🚀

![](img/ca55439b951a72f3d4d111d25573dca0_93.png)

![](img/ca55439b951a72f3d4d111d25573dca0_95.png)

![](img/ca55439b951a72f3d4d111d25573dca0_97.png)

`pandas` 提供了一个更便捷的函数 `dropna()` 来直接删除缺失值。

![](img/ca55439b951a72f3d4d111d25573dca0_99.png)

![](img/ca55439b951a72f3d4d111d25573dca0_101.png)

以下是 `dropna()` 的用法：
- `axis=0`（默认）：删除包含缺失值的行。
- `axis=1`：删除包含缺失值的列。
- `how=‘any’`（默认）：只要该行/列有缺失值就删除。
- `how=‘all’`：只有当该行/列全部为缺失值时才删除。

![](img/ca55439b951a72f3d4d111d25573dca0_103.png)

![](img/ca55439b951a72f3d4d111d25573dca0_105.png)

```python
# 删除含有缺失值的行
df_cleaned_quick = df.dropna(axis=0)
print(df_cleaned_quick)
```

![](img/ca55439b951a72f3d4d111d25573dca0_107.png)

![](img/ca55439b951a72f3d4d111d25573dca0_109.png)

![](img/ca55439b951a72f3d4d111d25573dca0_111.png)

### 方法三：填充缺失值 ✨

![](img/ca55439b951a72f3d4d111d25573dca0_113.png)

![](img/ca55439b951a72f3d4d111d25573dca0_115.png)

![](img/ca55439b951a72f3d4d111d25573dca0_117.png)

![](img/ca55439b951a72f3d4d111d25573dca0_119.png)

在某些情况下，直接删除数据会导致样本量过小（删除成本高）。这时，我们可以选择用合理的值来填充缺失值。`pandas` 提供了 `fillna()` 方法。

![](img/ca55439b951a72f3d4d111d25573dca0_121.png)

![](img/ca55439b951a72f3d4d111d25573dca0_123.png)

![](img/ca55439b951a72f3d4d111d25573dca0_125.png)

最简单的填充是使用一个固定值。
```python
# 用0填充所有缺失值
df_filled = df.fillna(value=0)
print(df_filled)
```

![](img/ca55439b951a72f3d4d111d25573dca0_127.png)

![](img/ca55439b951a72f3d4d111d25573dca0_129.png)

![](img/ca55439b951a72f3d4d111d25573dca0_131.png)

![](img/ca55439b951a72f3d4d111d25573dca0_133.png)

但更合理的做法是使用相邻的数据进行填充，这能最大程度保留数据的趋势。
- `method=‘ffill’` 或 `pad`：用前一个有效值向前填充。
- `method=‘bfill’` 或 `backfill`：用后一个有效值向后填充。
- `axis`：指定填充方向（0为沿列向下/上，1为沿行向右/左）。

![](img/ca55439b951a72f3d4d111d25573dca0_135.png)

```python
# 沿列方向，用上一个有效值填充
df_filled_ffill = df.fillna(method='ffill', axis=0)
print(df_filled_ffill)

# 沿行方向，用下一个有效值填充
df_filled_bfill = df.fillna(method='bfill', axis=1)
print(df_filled_bfill)
```

**何时删除？何时填充？**
- **删除**：当缺失数据比例很小，删除后对整体分析影响不大时。
- **填充**：当缺失数据比例较高，删除会导致信息严重丢失时。填充时需根据数据背景选择最合理的策略（如均值、中位数、前后值等）。

## 总结

![](img/ca55439b951a72f3d4d111d25573dca0_137.png)

![](img/ca55439b951a72f3d4d111d25573dca0_139.png)

本节课中我们一起学习了数据清洗中缺失值的处理。我们首先了解了 `None` 与 `NaN` 的区别，明白了 `NaN` 在数据分析中的优势。然后，我们掌握了三种处理 `DataFrame` 中缺失值的方法：
1.  使用 `isnull()`/`any()` 等组合方法定位并删除缺失行。
2.  使用高效的 `dropna()` 函数直接删除。
3.  使用 `fillna()` 函数，根据数据情况选择固定值或前后值进行填充。

![](img/ca55439b951a72f3d4d111d25573dca0_141.png)

![](img/ca55439b951a72f3d4d111d25573dca0_143.png)

理解并熟练运用这些方法，是进行高质量数据分析的基础。下一节，我们将继续学习数据清洗的另一个主题：重复值的处理。