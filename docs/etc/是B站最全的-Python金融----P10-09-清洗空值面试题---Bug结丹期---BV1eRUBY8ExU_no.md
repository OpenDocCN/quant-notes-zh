# Python金融分析+量化交易：P10：09 清洗空值面试题

![](img/ec066cc430649cff86bbdf5afb364c2c_1.png)

在本节课中，我们将通过一个真实的面试题，学习如何处理数据集中的空值（缺失值）。我们将使用Pandas库来加载数据，并演示两种处理空值的核心方法：删除和填充。

![](img/ec066cc430649cff86bbdf5afb364c2c_3.png)

## 概述与数据加载

![](img/ec066cc430649cff86bbdf5afb364c2c_5.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_7.png)

上一节我们介绍了数据清洗的基本概念。本节中，我们来看看如何在一个具体场景下应用这些知识。

![](img/ec066cc430649cff86bbdf5afb364c2c_9.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_11.png)

题目背景是处理一个冷库的温度数据。数据来自一个Excel表格，其中包含7个温度采集设备（编号1-7）每分钟采集的温度记录。我们的目标是使用前4个必要设备的数据，为后续建立温度场关系模型（用于预测设备5-7的温度）做准备。虽然模型构建本身涉及机器学习，不在当前讨论范围，但数据预处理——特别是处理数据中的“丢帧”（即空值）——是我们可以完成的。

![](img/ec066cc430649cff86bbdf5afb364c2c_13.png)

首先，我们需要将数据加载到Pandas DataFrame中。

![](img/ec066cc430649cff86bbdf5afb364c2c_15.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_17.png)

```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```

加载后，数据`df`的结构如下：每一行代表一个时间点（每分钟），每一列代表一个温度采集设备（1-7）。例如，数值`-20.5`可能代表在某个时间点，3号设备采集到的温度。

![](img/ec066cc430649cff86bbdf5afb364c2c_19.png)

## 核心任务：处理空值

原始数据中存在丢帧现象，即某些位置的数据是空值（NaN）。我们的任务就是清洗这些空值。主要有两种思路：删除包含空值的整行数据，或用邻近值填充空值。

![](img/ec066cc430649cff86bbdf5afb364c2c_21.png)

我们首先关注设备1-4的数据，因为它们是建立模型所必需的。因此，我们先从原始数据中提取这四列。

![](img/ec066cc430649cff86bbdf5afb364c2c_23.png)

```python
df = df[['1', '2', '3', '4']]
```

![](img/ec066cc430649cff86bbdf5afb364c2c_25.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_27.png)

现在，`df`只包含设备1-4的数据，其中存在一些空值。接下来，我们分别尝试两种处理方法。

![](img/ec066cc430649cff86bbdf5afb364c2c_29.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_31.png)

### 方法一：删除包含空值的行

![](img/ec066cc430649cff86bbdf5afb364c2c_33.png)

第一种方法是直接删除任何在设备1-4列中含有空值的整行数据。这能保证剩余数据的完整性。

![](img/ec066cc430649cff86bbdf5afb364c2c_35.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_37.png)

在Pandas中，我们可以使用`dropna`方法。需要指定`axis=0`来按行删除。

```python
df_dropped = df.dropna(axis=0)
```

![](img/ec066cc430649cff86bbdf5afb364c2c_39.png)

执行后，`df_dropped`将只包含在四列上都有完整数据的行。原始数据有1060行，删除空值行后可能剩下982行。由于删除的数据比例不大，这种方法是可行的。

![](img/ec066cc430649cff86bbdf5afb364c2c_41.png)

另一种等效的实现方式是使用布尔索引。我们先检查哪些行在四列上**都不**为空（即`notnull`且`all`），然后选取这些行。

![](img/ec066cc430649cff86bbdf5afb364c2c_43.png)

```python
df_dropped_alternative = df[df.notnull().all(axis=1)]
```

`df.notnull().all(axis=1)`会生成一个布尔序列，`True`表示该行在所选列上全部非空。用这个序列索引原DataFrame，就得到了删除空值行的结果，与`dropna`效果一致。

![](img/ec066cc430649cff86bbdf5afb364c2c_45.png)

### 方法二：使用邻近值填充空值

![](img/ec066cc430649cff86bbdf5afb364c2c_47.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_49.png)

第二种方法不是删除数据，而是用合理的值填充空位。对于时间序列数据（如每分钟的温度），一个常用的策略是使用同一列（即同一设备）中前一个或后一个时间点的值来填充，因为相邻时刻的温度通常变化不大。

![](img/ec066cc430649cff86bbdf5afb364c2c_51.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_53.png)

这可以通过`fillna`方法实现，并指定填充方法（`method`）和轴向（`axis`）。

![](img/ec066cc430649cff86bbdf5afb364c2c_55.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_57.png)

*   **向前填充**：用该空值**上方**（同一列的前一个）非空值填充。参数为`method='ffill'`或`method='pad'`。
*   **向后填充**：用该空值**下方**（同一列的后一个）非空值填充。参数为`method='bfill'`或`method='backfill'`。

![](img/ec066cc430649cff86bbdf5afb364c2c_59.png)

由于数据是按时间排列的，我们按列方向（`axis=0`）进行填充。

```python
# 先向前填充
df_filled = df.fillna(method='ffill', axis=0)
# 再向后填充（处理开头连续空值的情况）
df_filled = df_filled.fillna(method='bfill', axis=0)
```

![](img/ec066cc430649cff86bbdf5afb364c2c_61.png)

为什么需要两步？假设某列第一行就是空值，它没有“前一个”值可供向前填充。因此，先向前填充后，可能仍存在空值（位于数据开头）。再执行一次向后填充，就能用“后一个”值填充这些剩余的空值，确保所有空值都被处理。

![](img/ec066cc430649cff86bbdf5afb364c2c_63.png)

### 验证清洗结果

无论采用哪种方法，清洗后都需要验证是否还存在空值。我们可以使用`isnull()`结合`any()`方法来检查。

![](img/ec066cc430649cff86bbdf5afb364c2c_65.png)

*   `df.isnull()`：返回一个与原DataFrame形状相同的布尔DataFrame，空值位置为`True`。
*   `.any(axis=0)`：沿行方向（`axis=0`）检查，如果**任何一行**在该列为`True`，则结果中该列为`True`。这用于检查各列是否还有空值。
*   `.any(axis=1)`：沿列方向（`axis=1`）检查，如果**任何一列**在该行为`True`，则结果中该行为`True`。这用于检查各行是否还有空值。

![](img/ec066cc430649cff86bbdf5afb364c2c_67.png)

对于填充后的数据`df_filled`，我们可以这样验证：

```python
# 检查各列是否还有空值
column_has_null = df_filled.isnull().any(axis=0)
# 检查各行是否还有空值
row_has_null = df_filled.isnull().any(axis=1)
```

![](img/ec066cc430649cff86bbdf5afb364c2c_69.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_71.png)

如果`column_has_null`和`row_has_null`的结果全部为`False`，则说明数据中已不存在任何空值，清洗工作完成。

![](img/ec066cc430649cff86bbdf5afb364c2c_73.png)

## 总结

![](img/ec066cc430649cff86bbdf5afb364c2c_75.png)

本节课中我们一起学习了如何处理数据集中的空值。我们通过一个冷库温度数据的面试题，实践了两种核心方法：
1.  **删除**：使用`df.dropna(axis=0)`或布尔索引`df[df.notnull().all(axis=1)]`，直接删除包含空值的行。适用于空值较少、删除后不影响分析的情况。
2.  **填充**：使用`df.fillna(method='ffill', axis=0)`和`df.fillna(method='bfill', axis=0)`组合，用同一设备相邻时间点的值填充空值。适用于时间序列数据，能保留更多数据样本。

![](img/ec066cc430649cff86bbdf5afb364c2c_77.png)

最后，我们使用`df.isnull().any()`来验证清洗结果，确保数据已准备就绪，可供下一步分析或建模使用。

![](img/ec066cc430649cff86bbdf5afb364c2c_79.png)

![](img/ec066cc430649cff86bbdf5afb364c2c_81.png)

掌握了空值处理，数据清洗就完成了重要的一步。下一节，我们将继续学习如何处理数据中的重复值和异常值。