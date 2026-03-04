# Python金融量化分析：P18：缺失值的清洗 📊

![](img/da5f65d72238fcae6e2d3808739ff41b_1.png)

在本节课中，我们将学习数据分析中一个至关重要的环节——数据清洗，特别是如何处理数据中的缺失值。数据清洗是确保分析结果准确可靠的前提，因为原始数据中常常存在缺失值、重复值和异常值，这些都会干扰最终的分析结论。

![](img/da5f65d72238fcae6e2d3808739ff41b_3.png)

## 数据清洗概述

![](img/da5f65d72238fcae6e2d3808739ff41b_5.png)

数据清洗是pandas模块中常用的分析形式。在进行数据分析前，我们需要对原始数据进行清洗。原始数据中可能存在缺失值（即空值）。在某些需求下，这些空值没有意义，反而会干扰分析结果的产生。因此，我们需要将这些缺失值进行清洗，例如删除。除了缺失值，数据中还可能存在重复数据和异常值，这些同样需要被过滤和清洗。

![](img/da5f65d72238fcae6e2d3808739ff41b_7.png)

在pandas数据清洗过程中，我们需要处理以下三种类型的值：
1.  缺失值
2.  重复值
3.  异常值

![](img/da5f65d72238fcae6e2d3808739ff41b_9.png)

本节课，我们首先来看缺失值的处理方式。

![](img/da5f65d72238fcae6e2d3808739ff41b_11.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_13.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_15.png)

## 缺失值的两种形式：`None` 与 `np.nan`

![](img/da5f65d72238fcae6e2d3808739ff41b_17.png)

在数据分析中，我们遇到的空值通常是`np.nan`形式，而不是`None`形式。接下来我们看看这两种空值的区别。

首先，`None`的类型是`NoneType`，它是一个对象类型。

![](img/da5f65d72238fcae6e2d3808739ff41b_19.png)

```python
type(None)
```

![](img/da5f65d72238fcae6e2d3808739ff41b_21.png)

而在数据分析中使用的`np.nan`，其类型是浮点型。

```python
import numpy as np
type(np.nan)
```

![](img/da5f65d72238fcae6e2d3808739ff41b_23.png)

为什么数据分析中需要使用浮点型的空值呢？让我们通过一个运算示例来理解。

![](img/da5f65d72238fcae6e2d3808739ff41b_25.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_27.png)

对象类型的`None`无法参与算术运算：
```python
None + 1  # 会报错
```

![](img/da5f65d72238fcae6e2d3808739ff41b_29.png)

而浮点型的`np.nan`可以参与运算，但结果仍是空值：
```python
np.nan + 1  # 结果为 nan
```

![](img/da5f65d72238fcae6e2d3808739ff41b_31.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_33.png)

因此，在数据分析中，如果原始数据中的空值是`np.nan`形式，它不会中断整个运算流程。而如果空值是`None`形式，则可能在运算过程中导致中断。

![](img/da5f65d72238fcae6e2d3808739ff41b_35.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_37.png)

在pandas中有一个重要特性：如果遇到了`None`形式的空值，pandas会自动将其强制转换为`np.nan`形式。尽管如此，了解这两种空值的本质区别仍然很重要。

![](img/da5f65d72238fcae6e2d3808739ff41b_39.png)

## 缺失值的清洗操作

![](img/da5f65d72238fcae6e2d3808739ff41b_41.png)

接下来，我们看看在使用pandas进行数据分析时，如果产生了空值，应如何进行清洗。

首先，导入必要的模块并创建一组包含空值的示例数据。

![](img/da5f65d72238fcae6e2d3808739ff41b_43.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_45.png)

```python
import pandas as pd
import numpy as np
from pandas import DataFrame, Series

# 创建一组8行6列的随机数据
df = DataFrame(np.random.randint(0, 100, size=(8, 6)))
# 人为制造三个空值
df.iloc[2, 3] = None
df.iloc[4, 4] = np.nan
df.iloc[5, 2] = None
```

![](img/da5f65d72238fcae6e2d3808739ff41b_47.png)

现在，我们得到了一组包含三个空值的数据。我们的目标是将这些空值过滤掉。在DataFrame中，过滤通常意味着删除包含空值的整行数据，因为删除单个单元格或整列可能会破坏数据结构。

![](img/da5f65d72238fcae6e2d3808739ff41b_49.png)

### 方式一：使用 `isnull()`/`notnull()` 结合 `any()`/`all()`

![](img/da5f65d72238fcae6e2d3808739ff41b_51.png)

这种技术使用`isnull()`（或`notnull()`）与`any()`和`all()`函数来识别并过滤包含空值的行。

![](img/da5f65d72238fcae6e2d3808739ff41b_53.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_55.png)

`isnull()`方法用于检测数据中的每个元素是否为空值，返回布尔值（True表示空值）。

![](img/da5f65d72238fcae6e2d3808739ff41b_57.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_59.png)

```python
df.isnull()
```

![](img/da5f65d72238fcae6e2d3808739ff41b_61.png)

`notnull()`方法与`isnull()`相反。

![](img/da5f65d72238fcae6e2d3808739ff41b_63.png)

```python
df.notnull()
```

![](img/da5f65d72238fcae6e2d3808739ff41b_65.png)

为了找出哪些行包含空值，我们可以结合使用`any()`方法。`any(axis=1)`会检查每一行中是否存在True（即空值），如果有则返回True。

```python
# 找出包含空值的行
condition = df.isnull().any(axis=1)
condition
```

![](img/da5f65d72238fcae6e2d3808739ff41b_67.png)

得到的布尔序列中，True对应的行索引就是包含空值的行。我们可以将这组布尔值作为行索引来筛选出这些行。

![](img/da5f65d72238fcae6e2d3808739ff41b_69.png)

```python
# 获取包含空值的行数据
df.loc[condition]
```

![](img/da5f65d72238fcae6e2d3808739ff41b_71.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_73.png)

要删除这些行，可以先获取其索引，然后使用`drop`方法。

![](img/da5f65d72238fcae6e2d3808739ff41b_75.png)

```python
# 获取要删除的行索引
drop_index = df.loc[condition].index
# 删除这些行
df.drop(labels=drop_index, axis=0)
```

![](img/da5f65d72238fcae6e2d3808739ff41b_77.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_79.png)

同样，也可以使用`notnull()`结合`all()`来实现。`all(axis=1)`会检查每一行是否全部为True（即没有空值）。

![](img/da5f65d72238fcae6e2d3808739ff41b_81.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_83.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_85.png)

```python
# 使用 notnull() 和 all() 找出不含空值的行
condition2 = df.notnull().all(axis=1)
# 直接筛选出不含空值的行
df.loc[condition2]
```

![](img/da5f65d72238fcae6e2d3808739ff41b_87.png)

**规律**：`isnull()`通常结合`any()`使用，`notnull()`通常结合`all()`使用。通过这几种技术的组合，可以删除包含空值的行数据。

![](img/da5f65d72238fcae6e2d3808739ff41b_89.png)

### 方式二：使用 `dropna()` 方法

![](img/da5f65d72238fcae6e2d3808739ff41b_91.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_93.png)

pandas提供了一个更简便的内置函数`dropna()`，可以直接删除包含缺失值的行或列。

![](img/da5f65d72238fcae6e2d3808739ff41b_95.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_97.png)

```python
# 删除包含空值的行 (axis=0 表示行)
df.dropna(axis=0)
```

![](img/da5f65d72238fcae6e2d3808739ff41b_99.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_101.png)

如果想删除包含空值的列，可以将`axis`参数设置为1。

![](img/da5f65d72238fcae6e2d3808739ff41b_103.png)

```python
# 删除包含空值的列
df.dropna(axis=1)
```

![](img/da5f65d72238fcae6e2d3808739ff41b_105.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_107.png)

`dropna()`方法一步到位，但理解方式一中的原理同样重要。

![](img/da5f65d72238fcae6e2d3808739ff41b_109.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_111.png)

## 缺失值的覆盖操作

![](img/da5f65d72238fcae6e2d3808739ff41b_113.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_115.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_117.png)

除了删除，处理缺失值的另一种方式是对其进行覆盖（填充）。这意味着用某个值替换空值，使其不再为空。

![](img/da5f65d72238fcae6e2d3808739ff41b_119.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_121.png)

最简单的方式是使用`fillna()`方法并指定一个固定值。

![](img/da5f65d72238fcae6e2d3808739ff41b_123.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_125.png)

```python
# 用固定值666填充所有空值
df.fillna(value=666)
```

![](img/da5f65d72238fcae6e2d3808739ff41b_127.png)

然而，用固定值填充通常缺乏合理性。更合理的做法是使用空值周围邻近的值进行填充，例如使用前一个或后一个有效值。这可以通过`fillna()`的`method`参数实现。

```python
# 向前填充：用同一列上一个非空值填充空值
df.fillna(method='ffill', axis=0)
# 向后填充：用同一列下一个非空值填充空值
df.fillna(method='bfill', axis=0)
```

`axis`参数决定了填充的方向：
*   `axis=0`（默认）：沿列方向（垂直）查找前/后值。
*   `axis=1`：沿行方向（水平）查找左/右值。

```python
# 水平方向向前填充（用左边的值填充）
df.fillna(method='ffill', axis=1)
```

**何时选择删除，何时选择填充？**
*   **删除**：当包含空值的行数占总行数的比例较小，删除成本不高时，优先选择删除，以保留数据的真实性。
*   **填充**：当空值过多，删除会导致大量数据丢失（删除成本高）时，可以考虑使用合理的策略进行填充。但需注意，填充的值终究是估算值。

## 总结

![](img/da5f65d72238fcae6e2d3808739ff41b_129.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_131.png)

本节课我们一起学习了数据清洗中缺失值的处理。我们首先了解了数据分析中使用的空值`np.nan`与Python中的`None`在类型和运算特性上的区别。然后，我们重点掌握了两种处理缺失值的方法：
1.  **删除**：使用`isnull().any()`、`notnull().all()`组合或直接使用`dropna()`方法删除包含空值的行或列。
2.  **覆盖（填充）**：使用`fillna()`方法，可以用固定值填充，也可以用前向(`ffill`)或后向(`bfill`)填充等更合理的方式。

![](img/da5f65d72238fcae6e2d3808739ff41b_133.png)

![](img/da5f65d72238fcae6e2d3808739ff41b_135.png)

理解这些方法及其适用场景，是进行高质量数据分析的基础。在下一节中，我们将继续学习数据清洗的另一个重要部分——重复值的处理。