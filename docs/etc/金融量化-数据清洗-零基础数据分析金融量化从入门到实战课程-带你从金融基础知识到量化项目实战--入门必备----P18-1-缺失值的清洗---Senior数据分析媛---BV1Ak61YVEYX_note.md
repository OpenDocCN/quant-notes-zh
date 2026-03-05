# 数据分析+金融量化+数据清洗：P18：缺失值的清洗 📊

![](img/a05407e66ebc8af255ce2083094b1de7_1.png)

![](img/a05407e66ebc8af255ce2083094b1de7_3.png)

在本节课中，我们将要学习在Python数据分析中，如何使用pandas模块进行数据清洗，特别是针对缺失值的处理。数据清洗是数据分析的必要前提，因为原始数据中可能存在缺失值、重复值或异常值，这些都会干扰最终的分析结果。本节我们将重点探讨缺失值的识别与处理方法。

![](img/a05407e66ebc8af255ce2083094b1de7_5.png)

## 缺失值概述

在数据分析中，缺失值通常指的是数据中的空值。这些空值在某些分析需求下没有意义，并且会干扰分析结果的准确性。因此，我们需要对它们进行清洗。在pandas中，我们主要处理三种需要清洗的值：**缺失值**、**重复值**和**异常值**。本节我们先从缺失值开始。

![](img/a05407e66ebc8af255ce2083094b1de7_7.png)

![](img/a05407e66ebc8af255ce2083094b1de7_9.png)

## 缺失值的类型：NaN vs None

![](img/a05407e66ebc8af255ce2083094b1de7_11.png)

![](img/a05407e66ebc8af255ce2083094b1de7_13.png)

在开始清洗之前，我们需要了解数据分析中使用的空值类型。Python中有两种常见的空值表示：`None`和`NaN`（Not a Number）。它们在本质上有重要区别。

![](img/a05407e66ebc8af255ce2083094b1de7_15.png)

![](img/a05407e66ebc8af255ce2083094b1de7_17.png)

![](img/a05407e66ebc8af255ce2083094b1de7_19.png)

`None`是一个Python对象，其类型为`NoneType`。

```python
type(None)  # 输出: NoneType
```

![](img/a05407e66ebc8af255ce2083094b1de7_21.png)

而`NaN`是一个特殊的浮点数，由NumPy库提供。

![](img/a05407e66ebc8af255ce2083094b1de7_23.png)

```python
import numpy as np
type(np.nan)  # 输出: float
```

![](img/a05407e66ebc8af255ce2083094b1de7_25.png)

为什么数据分析中更常用`NaN`呢？关键在于它们参与运算的能力。`None`是对象类型，无法直接参与算术运算。

```python
None + 1  # 会引发TypeError
```

![](img/a05407e66ebc8af255ce2083094b1de7_27.png)

![](img/a05407e66ebc8af255ce2083094b1de7_29.png)

![](img/a05407e66ebc8af255ce2083094b1de7_31.png)

而`NaN`是浮点类型，可以参与运算，但结果仍然是`NaN`。

![](img/a05407e66ebc8af255ce2083094b1de7_33.png)

![](img/a05407e66ebc8af255ce2083094b1de7_35.png)

```python
np.nan + 1  # 输出: nan
```

![](img/a05407e66ebc8af255ce2083094b1de7_37.png)

![](img/a05407e66ebc8af255ce2083094b1de7_39.png)

![](img/a05407e66ebc8af255ce2083094b1de7_41.png)

这意味着，如果原始数据中的空值是`NaN`形式，在进行数据运算时不会中断整个流程。相反，如果使用`None`，则可能导致运算错误。因此，在数据分析中，我们应使用`NaN`形式的空值。

![](img/a05407e66ebc8af255ce2083094b1de7_43.png)

> **注意**：在pandas中，如果遇到`None`形式的空值，pandas会自动将其强制转换为`NaN`形式。但了解两者的根本区别仍然很重要。

![](img/a05407e66ebc8af255ce2083094b1de7_45.png)

## 缺失值的清洗方法

![](img/a05407e66ebc8af255ce2083094b1de7_47.png)

上一节我们介绍了缺失值的概念和类型，本节中我们来看看如何具体清洗这些缺失值。我们将通过一个包含缺失值的示例数据集来演示。

![](img/a05407e66ebc8af255ce2083094b1de7_49.png)

首先，导入必要的库并创建一个包含缺失值的DataFrame。

```python
import pandas as pd
import numpy as np
from pandas import DataFrame, Series

![](img/a05407e66ebc8af255ce2083094b1de7_51.png)

![](img/a05407e66ebc8af255ce2083094b1de7_53.png)

# 创建一个8行6列的随机整数DataFrame
df = DataFrame(np.random.randint(0, 100, size=(8, 6)))
# 人为制造一些缺失值
df.iloc[2, 3] = None
df.iloc[4, 4] = np.nan
df.iloc[5, 2] = None
print(df)
```

![](img/a05407e66ebc8af255ce2083094b1de7_55.png)

现在，我们的数据`df`中已经包含了三个缺失值（显示为`NaN`）。接下来，我们将学习两种主要的处理方式：删除和填充。

![](img/a05407e66ebc8af255ce2083094b1de7_57.png)

![](img/a05407e66ebc8af255ce2083094b1de7_59.png)

### 方法一：删除缺失值 🗑️

![](img/a05407e66ebc8af255ce2083094b1de7_61.png)

![](img/a05407e66ebc8af255ce2083094b1de7_63.png)

对缺失值进行过滤通常指的是删除包含缺失值的整行数据。在数据分析中，我们通常按行删除，因为列代表数据结构，随意删除可能改变数据含义。

![](img/a05407e66ebc8af255ce2083094b1de7_65.png)

以下是使用`isnull()`、`notnull()`结合`any()`、`all()`函数进行删除的步骤。

![](img/a05407e66ebc8af255ce2083094b1de7_67.png)

首先，使用`isnull()`检测缺失值。它会返回一个布尔值DataFrame，`True`表示该位置是缺失值。

![](img/a05407e66ebc8af255ce2083094b1de7_69.png)

```python
df.isnull()
```

为了找出哪些**行**包含缺失值，我们需要在行方向上进行判断。`any(axis=1)`方法会检查每一行，如果该行中存在任何一个`True`（即存在缺失值），则返回`True`。

![](img/a05407e66ebc8af255ce2083094b1de7_71.png)

![](img/a05407e66ebc8af255ce2083094b1de7_73.png)

```python
# 找出包含缺失值的行
rows_with_nan = df.isnull().any(axis=1)
print(rows_with_nan)
```

![](img/a05407e66ebc8af255ce2083094b1de7_75.png)

![](img/a05407e66ebc8af255ce2083094b1de7_77.png)

现在，`rows_with_nan`是一个布尔序列，`True`对应的行索引就是包含缺失值的行。我们可以直接使用这个布尔序列来索引原数据，从而**保留**那些没有缺失值的行。

![](img/a05407e66ebc8af255ce2083094b1de7_79.png)

```python
# 方法1A：使用 isnull().any() 保留非缺失行
df_cleaned_isnull = df.loc[~df.isnull().any(axis=1)]
print(df_cleaned_isnull)
```

![](img/a05407e66ebc8af255ce2083094b1de7_81.png)

![](img/a05407e66ebc8af255ce2083094b1de7_83.png)

同样，我们也可以使用`notnull()`和`all()`来实现。`notnull()`返回`True`表示不是缺失值。`all(axis=1)`会检查每一行是否**全部**为`True`（即该行完全没有缺失值）。

![](img/a05407e66ebc8af255ce2083094b1de7_85.png)

![](img/a05407e66ebc8af255ce2083094b1de7_87.png)

![](img/a05407e66ebc8af255ce2083094b1de7_89.png)

```python
# 方法1B：使用 notnull().all() 保留非缺失行
df_cleaned_notnull = df.loc[df.notnull().all(axis=1)]
print(df_cleaned_notnull)
```

![](img/a05407e66ebc8af255ce2083094b1de7_91.png)

> **核心搭配**：
> *   `df.isnull().any(axis=1)`：用于找出包含缺失值的行。
> *   `df.notnull().all(axis=1)`：用于找出完全不包含缺失值的行。
> 两者结合布尔索引，即可实现删除缺失行的操作。

![](img/a05407e66ebc8af255ce2083094b1de7_93.png)

![](img/a05407e66ebc8af255ce2083094b1de7_95.png)

当然，pandas提供了更简洁的方法：`dropna()`函数。

![](img/a05407e66ebc8af255ce2083094b1de7_97.png)

![](img/a05407e66ebc8af255ce2083094b1de7_99.png)

```python
# 方法1C：使用 dropna() 直接删除包含缺失值的行
df_dropped = df.dropna(axis=0) # axis=0 表示按行删除
print(df_dropped)
```

![](img/a05407e66ebc8af255ce2083094b1de7_101.png)

![](img/a05407e66ebc8af255ce2083094b1de7_103.png)

`dropna(axis=0)`会直接删除任何包含缺失值的行。如果你想删除包含缺失值的列，可以将参数设置为`axis=1`。

![](img/a05407e66ebc8af255ce2083094b1de7_105.png)

![](img/a05407e66ebc8af255ce2083094b1de7_107.png)

### 方法二：填充缺失值 🔧

![](img/a05407e66ebc8af255ce2083094b1de7_109.png)

![](img/a05407e66ebc8af255ce2083094b1de7_111.png)

除了删除，另一种处理缺失值的方法是进行填充。这在删除成本过高时（例如，数据量很小，删除后所剩无几）尤为有用。填充的目标是用一个合理的值替换缺失值。

![](img/a05407e66ebc8af255ce2083094b1de7_113.png)

![](img/a05407e66ebc8af255ce2083094b1de7_115.png)

![](img/a05407e66ebc8af255ce2083094b1de7_117.png)

最简单的填充是用一个固定值，例如0。

![](img/a05407e66ebc8af255ce2083094b1de7_119.png)

![](img/a05407e66ebc8af255ce2083094b1de7_121.png)

![](img/a05407e66ebc8af255ce2083094b1de7_123.png)

```python
# 用固定值0填充所有缺失值
df_filled_zero = df.fillna(value=0)
print(df_filled_zero)
```

![](img/a05407e66ebc8af255ce2083094b1de7_125.png)

![](img/a05407e66ebc8af255ce2083094b1de7_127.png)

但固定值填充往往不够合理。更常见的做法是使用**邻近的值**进行填充。`fillna()`方法提供了`method`参数来实现。

![](img/a05407e66ebc8af255ce2083094b1de7_129.png)

![](img/a05407e66ebc8af255ce2083094b1de7_131.png)

![](img/a05407e66ebc8af255ce2083094b1de7_133.png)

*   **向前填充 (`ffill` 或 `pad`)**：用缺失值前一个有效值来填充。
*   **向后填充 (`bfill` 或 `backfill`)**：用缺失值后一个有效值来填充。

![](img/a05407e66ebc8af255ce2083094b1de7_135.png)

你还需要指定填充的轴向：
*   `axis=0`：在列方向上，用上一行或下一行的值填充。
*   `axis=1`：在行方向上，用前一列或后一列的值填充。

```python
# 使用列方向上的向前填充（用上一行的值填充）
df_filled_ffill = df.fillna(method='ffill', axis=0)
print(df_filled_ffill)

# 使用行方向上的向后填充（用后一列的值填充）
df_filled_bfill = df.fillna(method='bfill', axis=1)
print(df_filled_bfill)
```

**何时选择删除或填充？**
*   **选择删除**：当缺失值数量较少，删除后对整体数据影响不大时。
*   **选择填充**：当数据非常珍贵，删除会导致样本量严重不足时。填充时，应尽量使用有业务逻辑依据的方法（如时间序列中用前后时刻的值填充）。

## 总结

![](img/a05407e66ebc8af255ce2083094b1de7_137.png)

![](img/a05407e66ebc8af255ce2083094b1de7_139.png)

本节课中我们一起学习了数据清洗中缺失值的处理。我们首先了解了数据分析中应使用`NaN`而非`None`来表示空值的原因。接着，我们掌握了两种处理缺失值的核心方法：
1.  **删除**：使用`isnull()`/`notnull()`结合`any()`/`all()`进行条件筛选，或直接使用`dropna()`函数删除包含缺失值的行或列。
2.  **填充**：使用`fillna()`函数，可以用固定值填充，也可以用向前(`ffill`)或向后(`bfill`)填充法，利用邻近的数据进行更合理的填充。

![](img/a05407e66ebc8af255ce2083094b1de7_141.png)

![](img/a05407e66ebc8af255ce2083094b1de7_143.png)

理解并熟练运用这些方法，是保证后续数据分析结果准确可靠的重要基础。下一节，我们将继续学习数据清洗的另一个主题：重复值的处理。