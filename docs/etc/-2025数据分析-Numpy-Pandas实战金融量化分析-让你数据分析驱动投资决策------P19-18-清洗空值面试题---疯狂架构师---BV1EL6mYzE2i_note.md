# 数据分析实战：P19：18.清洗空值面试题 🧹

![](img/cefdc592c107037aa91b871ebd9f9015_1.png)

![](img/cefdc592c107037aa91b871ebd9f9015_3.png)

![](img/cefdc592c107037aa91b871ebd9f9015_5.png)

![](img/cefdc592c107037aa91b871ebd9f9015_7.png)

在本节课中，我们将通过一个真实的面试题，学习如何使用Pandas处理数据集中的空值。我们将掌握两种核心方法：删除包含空值的行，以及使用邻近值填充空值。

![](img/cefdc592c107037aa91b871ebd9f9015_9.png)

![](img/cefdc592c107037aa91b871ebd9f9015_11.png)

![](img/cefdc592c107037aa91b871ebd9f9015_13.png)

---

![](img/cefdc592c107037aa91b871ebd9f9015_15.png)

![](img/cefdc592c107037aa91b871ebd9f9015_17.png)

上一节我们介绍了空值处理的基本概念，本节中我们来看看一个具体的应用场景。

![](img/cefdc592c107037aa91b871ebd9f9015_19.png)

题目描述：现有一个冷库的温度数据Excel表格，其中包含7个温度采集设备（列1-7）每分钟记录的数据。数据处理的目标是：使用前4个必要设备的数据建立模型，以预测后3个设备的温度。作为数据预处理步骤，我们需要先处理原始数据中因“丢帧”而产生的空值。

![](img/cefdc592c107037aa91b871ebd9f9015_21.png)

![](img/cefdc592c107037aa91b871ebd9f9015_23.png)

首先，我们读取数据并查看其结构。

![](img/cefdc592c107037aa91b871ebd9f9015_25.png)

![](img/cefdc592c107037aa91b871ebd9f9015_27.png)

![](img/cefdc592c107037aa91b871ebd9f9015_29.png)

![](img/cefdc592c107037aa91b871ebd9f9015_31.png)

```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```

![](img/cefdc592c107037aa91b871ebd9f9015_33.png)

![](img/cefdc592c107037aa91b871ebd9f9015_35.png)

![](img/cefdc592c107037aa91b871ebd9f9015_37.png)

数据中，列1-7代表七个温度采集设备，每一行代表每分钟采集到的一组温度读数。我们的重点是处理列1-4这四列数据中的空值。

![](img/cefdc592c107037aa91b871ebd9f9015_39.png)

以下是处理空值的两种主要策略：

![](img/cefdc592c107037aa91b871ebd9f9015_41.png)

![](img/cefdc592c107037aa91b871ebd9f9015_43.png)

**1. 删除包含空值的行**
这是一种直接的方法，将任何在指定列中包含空值的整行数据移除。

![](img/cefdc592c107037aa91b871ebd9f9015_45.png)

```python
# 方法一：使用 dropna 函数
df_cleaned_drop = df[['1', '2', '3', '4']].dropna(axis=0)

![](img/cefdc592c107037aa91b871ebd9f9015_47.png)

![](img/cefdc592c107037aa91b871ebd9f9015_49.png)

![](img/cefdc592c107037aa91b871ebd9f9015_51.png)

![](img/cefdc592c107037aa91b871ebd9f9015_53.png)

# 方法二：结合 notna() 和 all() 进行布尔索引
df_cleaned_bool = df[['1', '2', '3', '4']][df[['1', '2', '3', '4']].notna().all(axis=1)]
```
在`dropna`函数中，参数`axis=0`表示按行操作（删除行）。原始数据有1060行，删除空值行后剩余982行，数据损失不大，因此删除法是可行的。

![](img/cefdc592c107037aa91b871ebd9f9015_55.png)

![](img/cefdc592c107037aa91b871ebd9f9015_57.png)

![](img/cefdc592c107037aa91b871ebd9f9015_59.png)

**2. 使用邻近值填充空值**
当删除数据成本过高或不合适时，我们可以用空值相邻的数据来填充它。对于时间序列数据（如每分钟的温度），通常使用同一列（同一设备）中前一个或后一个值来填充。

![](img/cefdc592c107037aa91b871ebd9f9015_61.png)

```python
# 先向前填充（用同一列中上一个非空值填充）
df_filled = df[['1', '2', '3', '4']].fillna(method='ffill', axis=0)
# 再向后填充（用同一列中下一个非空值填充），以确保所有空值都被处理
df_filled = df_filled.fillna(method='bfill', axis=0)
```
在`fillna`函数中，`method='ffill'`表示向前填充，`axis=0`表示沿列方向（竖直方向）操作。连续进行向前和向后填充可以处理数据开头或结尾的空值。

![](img/cefdc592c107037aa91b871ebd9f9015_63.png)

填充完成后，我们需要验证是否还有空值残留。

![](img/cefdc592c107037aa91b871ebd9f9015_65.png)

![](img/cefdc592c107037aa91b871ebd9f9015_67.png)

```python
# 检查是否还有空值
check_result = df_filled.isna().any()
print(check_result)
```
如果输出结果中所有列都显示为`False`，则表明所有空值已被成功填充。

![](img/cefdc592c107037aa91b871ebd9f9015_69.png)

![](img/cefdc592c107037aa91b871ebd9f9015_71.png)

---

![](img/cefdc592c107037aa91b871ebd9f9015_73.png)

![](img/cefdc592c107037aa91b871ebd9f9015_75.png)

本节课中我们一起学习了如何处理数据集中的空值。我们掌握了两种实用技巧：**删除空值行**和**使用前后向填充法覆盖空值**。这些是数据清洗的基础，能确保后续分析和建模所用数据的完整性与质量。

![](img/cefdc592c107037aa91b871ebd9f9015_77.png)

![](img/cefdc592c107037aa91b871ebd9f9015_79.png)

![](img/cefdc592c107037aa91b871ebd9f9015_81.png)

下一节，我们将探讨如何处理数据中的重复值和异常值。