# Python金融量化+业务数据分析：P20：DataFrame的级联操作

![](img/d41e59ef32942f2944ce5648ae3fb70a_1.png)

在本节课中，我们将要学习Pandas模块中DataFrame的级联操作。级联是将多个表格进行横向或纵向拼接的基础操作，掌握它对于后续的数据整合与分析至关重要。学完本节内容后，我们将迎来一个纯数据分析案例——人口数据分析，届时会综合运用级联、合并以及之前学过的DataFrame常规操作。

## 级联操作概述

![](img/d41e59ef32942f2944ce5648ae3fb70a_3.png)

上一节我们介绍了DataFrame的基本结构，本节中我们来看看如何将多个DataFrame拼接在一起。级联这个词对我们来说并不陌生，因为在NumPy模块中我们学习过数组的级联。同样，以二维表格形式存在的DataFrame也可以进行级联。简单来说，级联就是使用表格进行横向或纵向的拼接。

![](img/d41e59ef32942f2944ce5648ae3fb70a_5.png)

首先，我们需要导入必要的模块并创建一些示例数据。

![](img/d41e59ef32942f2944ce5648ae3fb70a_7.png)

```python
import numpy as np
import pandas as pd
from pandas import DataFrame

# 创建第一个DataFrame：df1
df1 = DataFrame(data=np.random.randint(0, 100, size=(5, 3)), columns=[‘A‘, ‘B‘, ‘C‘])
# 创建第二个DataFrame：df2
df2 = DataFrame(data=np.random.randint(0, 100, size=(5, 3)), columns=[‘A‘, ‘D‘, ‘C‘])
```

![](img/d41e59ef32942f2944ce5648ae3fb70a_9.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_11.png)

执行上述代码后，我们得到两个DataFrame：
*   `df1` 有5行3列，列索引为 `[‘A‘, ‘B‘, ‘C‘]`。
*   `df2` 也有5行3列，但列索引为 `[‘A‘, ‘D‘, ‘C‘]`。

![](img/d41e59ef32942f2944ce5648ae3fb70a_13.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_15.png)

## 使用 `concat` 进行级联

以下是进行级联操作的核心方法。

### 横向级联

![](img/d41e59ef32942f2944ce5648ae3fb70a_17.png)

横向级联意味着将表格按行方向拼接，要求参与级联的DataFrame具有相同的行索引。

![](img/d41e59ef32942f2944ce5648ae3fb70a_19.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_21.png)

```python
result_h = pd.concat((df1, df2), axis=1)
```

![](img/d41e59ef32942f2944ce5648ae3fb70a_23.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_25.png)

参数说明：
*   `objects`: 要级联的DataFrame对象，以元组形式传入，例如 `(df1, df2)`。
*   `axis`: 级联方向。`axis=1` 表示横向级联（按列拼接）。

![](img/d41e59ef32942f2944ce5648ae3fb70a_27.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_29.png)

由于 `df1` 和 `df2` 行数相同，横向级联会将它们并排连接，生成一个5行6列的新表格。

![](img/d41e59ef32942f2944ce5648ae3fb70a_31.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_33.png)

### 纵向级联

![](img/d41e59ef32942f2944ce5648ae3fb70a_35.png)

纵向级联意味着将表格按列方向拼接，要求参与级联的DataFrame具有相同的列索引。

![](img/d41e59ef32942f2944ce5648ae3fb70a_37.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_39.png)

```python
result_v = pd.concat((df1, df2), axis=0)
```

![](img/d41e59ef32942f2944ce5648ae3fb70a_41.png)

参数说明：
*   `axis=0` 表示纵向级联（按行拼接），这也是 `concat` 函数的默认值。

当我们尝试纵向级联 `df1` 和 `df2` 时，由于它们的列索引不完全一致（`df1` 有 ‘B‘ 列，`df2` 有 ‘D‘ 列），就会产生一种“不匹配级联”。

![](img/d41e59ef32942f2944ce5648ae3fb70a_43.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_45.png)

## 不匹配级联与连接方式

在不匹配级联中，Pandas会保留所有出现过的列。对于某个DataFrame中不存在的列，会用空值（NaN）进行填充。

![](img/d41e59ef32942f2944ce5648ae3fb70a_47.png)

```python
# 默认情况下，concat使用外连接(outer join)
result_v_outer = pd.concat((df1, df2), axis=0, join=‘outer‘)
```

![](img/d41e59ef32942f2944ce5648ae3fb70a_49.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_51.png)

`join` 参数决定了如何处理不匹配的索引：
*   **`join=‘outer‘` (默认值，外连接)**：保留所有索引的并集，缺失值用NaN填充。这保证了数据的完整性。
*   **`join=‘inner‘` (内连接)**：只保留所有索引的交集。

![](img/d41e59ef32942f2944ce5648ae3fb70a_53.png)

```python
# 使用内连接(inner join)
result_v_inner = pd.concat((df1, df2), axis=0, join=‘inner‘)
```

![](img/d41e59ef32942f2944ce5648ae3fb70a_55.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_57.png)

执行内连接后，结果将只包含 `df1` 和 `df2` 共有的列（即 ‘A‘ 和 ‘C‘ 列），‘B‘ 和 ‘D‘ 列因为不匹配而被丢弃。

![](img/d41e59ef32942f2944ce5648ae3fb70a_59.png)

**重要提示**：如果希望级联后不丢失任何原始数据，必须使用外连接(`join=‘outer‘`)。内连接虽然能避免产生空值，但会导致部分数据丢失。

![](img/d41e59ef32942f2944ce5648ae3fb70a_61.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_63.png)

## 使用 `append` 进行级联

![](img/d41e59ef32942f2944ce5648ae3fb70a_65.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_67.png)

除了 `concat`，Pandas还提供了 `append` 方法用于纵向追加数据。

```python
result_append = df1.append(df2)
```

![](img/d41e59ef32942f2944ce5648ae3fb70a_69.png)

`append` 方法的特点：
*   只能进行纵向级联（相当于 `axis=0`）。
*   默认采用外连接(`join=‘outer‘`)逻辑，且不能更改为内连接。
*   通常，我们掌握功能更强大的 `concat` 方法即可，知道 `append` 的存在但一般较少使用。

![](img/d41e59ef32942f2944ce5648ae3fb70a_71.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_73.png)

## 级联操作总结

![](img/d41e59ef32942f2944ce5648ae3fb70a_75.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_77.png)

本节课中我们一起学习了DataFrame的级联操作。级联的核心是将多个DataFrame沿横向（`axis=1`）或纵向（`axis=0`）进行拼接。关键在于理解“不匹配级联”的概念：当参与级联的表格行列索引不一致时，会产生空值（NaN）。通过 `join` 参数（`‘outer‘` 或 `‘inner‘`）可以控制是保留所有数据（外连接）还是只保留共有部分（内连接）。为了保证数据的完整性，在实践中通常使用外连接。

![](img/d41e59ef32942f2944ce5648ae3fb70a_79.png)

![](img/d41e59ef32942f2944ce5648ae3fb70a_81.png)

级联完成后，下节课我们将讲解另一个重要的数据整合操作——合并，并详细阐述合并与级联的核心区别。