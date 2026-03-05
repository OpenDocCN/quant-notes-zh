# Python金融量化+业务数据分析：P18：2.清洗空值面试题 📊

![](img/5d177fbc90d39b06d45b2816fc8e01a0_1.png)

在本节课中，我们将通过一个真实的面试题，学习如何处理数据集中的空值（缺失值）。我们将使用Pandas库来加载数据，并演示两种处理空值的核心方法：删除和填充。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_3.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_5.png)

## 概述

![](img/5d177fbc90d39b06d45b2816fc8e01a0_7.png)

面试题描述了一个冷库温度数据场景。数据来自七个温度采集设备，每分钟记录一次。我们的目标是使用前四个设备的数据来预测后三个设备的温度。但在进行机器学习建模前，需要先处理原始数据中存在的“丢帧”现象，即空值。本节课将专注于数据清洗的第一步：空值处理。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_9.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_11.png)

## 数据加载与查看

![](img/5d177fbc90d39b06d45b2816fc8e01a0_13.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_15.png)

首先，我们需要将数据加载到Python环境中进行查看。数据存储在一个名为`text data.xlsx`的Excel文件中。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_17.png)

```python
import pandas as pd

![](img/5d177fbc90d39b06d45b2816fc8e01a0_19.png)

# 读取Excel文件
df = pd.read_excel('./data/text data.xlsx')
print(df.head())
```

加载后，我们看到数据框的列`1`到`7`分别代表七个温度采集设备，每一行代表每分钟采集到的一组温度读数。

## 提取目标数据

![](img/5d177fbc90d39b06d45b2816fc8e01a0_21.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_23.png)

我们的模型仅依赖于前四个设备（列`1`到`4`）的数据。因此，首先从完整数据集中提取这四列。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_25.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_27.png)

```python
# 提取前四列数据
df_target = df[['1', '2', '3', '4']]
print(df_target.head())
```

![](img/5d177fbc90d39b06d45b2816fc8e01a0_29.png)

现在，`df_target`包含了我们需要清洗和处理的数据。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_31.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_33.png)

## 方法一：删除含有空值的行

![](img/5d177fbc90d39b06d45b2816fc8e01a0_35.png)

一种直接的处理方式是删除任何包含空值的行。如果数据量足够大且缺失行不多，这是一个可行的选择。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_37.png)

以下是使用`dropna`函数删除空值行的操作：

![](img/5d177fbc90d39b06d45b2816fc8e01a0_39.png)

```python
# 删除任何包含空值的行
df_dropped = df_target.dropna(axis=0)
print(f"原始数据行数: {df_target.shape[0]}")
print(f"删除空值后行数: {df_dropped.shape[0]}")
```

![](img/5d177fbc90d39b06d45b2816fc8e01a0_41.png)

**代码解释**：
*   `dropna()`: 用于删除缺失值。
*   `axis=0`: 参数`axis=0`表示操作方向为行（即删除整行）。在Pandas的`dropna`、`fillna`等函数中，`axis=0`通常代表行，`axis=1`代表列。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_43.png)

我们也可以通过布尔索引实现相同的效果：

```python
# 使用 notna() 和 all() 创建布尔掩码，筛选出不含空值的行
mask = df_target.notna().all(axis=1)
df_dropped_alternative = df_target.loc[mask]
```

![](img/5d177fbc90d39b06d45b2816fc8e01a0_45.png)

**代码解释**：
*   `df_target.notna()`: 返回一个布尔型DataFrame，非空值为`True`，空值为`False`。
*   `.all(axis=1)`: 参数`axis=1`表示按行检查。如果一行中所有值都为`True`（即没有空值），则结果为`True`，否则为`False`。
*   `df_target.loc[mask]`: 使用布尔掩码`mask`索引，只选择值为`True`的行。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_47.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_49.png)

对比原始数据（1060行）和删除后的数据（982行），我们发现只删除了少量数据，删除成本较低，因此这种方法是可以接受的。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_51.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_53.png)

## 方法二：填充空值

![](img/5d177fbc90d39b06d45b2816fc8e01a0_55.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_57.png)

如果希望保留所有数据行，可以使用填充法。对于时间序列数据，常用前后相邻的值进行填充，因为相邻时刻的温度通常变化不大。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_59.png)

以下是使用`fillna`方法进行向前和向后填充的操作：

```python
# 首先向前填充（用上一行的值填充当前空值）
df_filled = df_target.fillna(method='ffill', axis=0)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_61.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_63.png)

# 然后向后填充（用下一行的值填充当前空值），确保所有空值都被处理
df_filled = df_filled.fillna(method='bfill', axis=0)
```

**代码解释**：
*   `fillna(method='ffill', axis=0)`: `method='ffill'`表示向前填充，`axis=0`表示沿行方向（竖直方向）操作。
*   `fillna(method='bfill', axis=0)`: `method='bfill'`表示向后填充。
*   先向前再向后填充，可以处理数据开头或结尾的空值，确保所有空值都被覆盖。

## 验证清洗结果

![](img/5d177fbc90d39b06d45b2816fc8e01a0_65.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_67.png)

填充完成后，需要验证是否还存在空值。

```python
# 检查是否还有空值
# 按列检查，如果某一列还有空值，则返回True
has_null_columns = df_filled.isna().any(axis=0)
print("各列是否还有空值：")
print(has_null_columns)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_69.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_71.png)

# 也可以按行检查
has_null_rows = df_filled.isna().any(axis=1)
print("\n是否存在包含空值的行：")
print(has_null_rows.any()) # 如果为False，表示所有行都已无空值
```

![](img/5d177fbc90d39b06d45b2816fc8e01a0_73.png)

**代码解释**：
*   `isna()`: 是`isnull()`的别名，用于检测空值，返回布尔型DataFrame。
*   `.any(axis=0)`: 参数`axis=0`表示按列检查。如果一列中存在任何一个`True`（即空值），则结果为`True`。
*   如果输出显示所有列都为`False`，则证明空值已被成功清除。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_75.png)

## 总结

![](img/5d177fbc90d39b06d45b2816fc8e01a0_77.png)

本节课中，我们一起学习了如何处理数据集中的空值，这是数据清洗的关键步骤。我们通过一个冷库温度数据的面试题，实践了两种主要方法：
1.  **删除法**：使用`df.dropna()`直接删除含有空值的行。适用于数据量大、缺失值少的情况。
2.  **填充法**：使用`df.fillna()`，并结合时间序列特性，采用向前(`ffill`)和向后(`bfill`)填充策略。适用于希望保留数据样本量的情况。

![](img/5d177fbc90d39b06d45b2816fc8e01a0_79.png)

![](img/5d177fbc90d39b06d45b2816fc8e01a0_81.png)

最后，我们使用`df.isna().any()`来验证清洗操作是否彻底，确保数据为后续分析做好准备。下一节，我们将探讨如何处理数据中的重复值和异常值。