# Python金融量化：P19：18：清洗空值面试题 📊

![](img/9b22353c0cc05cce04be4209c0b0e90c_1.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_3.png)

在本节课中，我们将通过一个真实的面试题，学习如何处理数据集中的空值（缺失值）。我们将使用Pandas库来加载数据，并演示两种处理空值的核心方法：删除包含空值的行，以及使用邻近值进行填充。

![](img/9b22353c0cc05cce04be4209c0b0e90c_5.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_7.png)

---

![](img/9b22353c0cc05cce04be4209c0b0e90c_9.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_11.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_13.png)

上一节我们介绍了空值的基本概念，本节中我们来看看如何在一个具体的数据清洗场景中应用这些方法。

![](img/9b22353c0cc05cce04be4209c0b0e90c_15.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_17.png)

题目背景是处理一个冷库的温度数据。数据来自一个Excel表格，其中包含7个温度采集设备（对应7列数据）每分钟记录的温度。我们的目标是使用前4个设备的数据来建立模型，以预测后3个设备的温度。但在建模前，需要先处理原始数据中因“丢帧”而产生的空值。

![](img/9b22353c0cc05cce04be4209c0b0e90c_19.png)

首先，我们需要将数据加载到Pandas DataFrame中。

```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```

![](img/9b22353c0cc05cce04be4209c0b0e90c_21.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_23.png)

加载后，数据`df`的结构如下：每一行代表一个时间点（每分钟），每一列（1-7）代表一个温度采集设备。我们重点关注前4列（设备1-4）的数据质量。

![](img/9b22353c0cc05cce04be4209c0b0e90c_25.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_27.png)

接下来，我们提取出需要处理的这四列数据。

![](img/9b22353c0cc05cce04be4209c0b0e90c_29.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_31.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_33.png)

```python
df = df[['1', '2', '3', '4']]
```

![](img/9b22353c0cc05cce04be4209c0b0e90c_35.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_37.png)

现在，`df`中仅包含前四个设备的数据，其中存在一些空值（NaN）。我们的任务就是清洗这些空值。

以下是两种主要的处理方法：

![](img/9b22353c0cc05cce04be4209c0b0e90c_39.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_41.png)

**方法一：删除包含空值的行**
这是最直接的方法，使用`dropna`函数可以删除任何包含空值的行。

![](img/9b22353c0cc05cce04be4209c0b0e90c_43.png)

```python
df_cleaned_drop = df.dropna(axis=0)
```
在Pandas中，`axis=0`表示操作方向为行（纵向），`axis=1`表示列（横向）。因此，上述代码会删除所有在1-4列中存在空值的整行数据。

![](img/9b22353c0cc05cce04be4209c0b0e90c_45.png)

我们也可以使用`notna`和`all`函数组合来实现相同的效果：
```python
df_cleaned_drop = df[df.notna().all(axis=1)]
```
`df.notna()`会返回一个布尔值DataFrame，其中`True`表示非空值。`all(axis=1)`会检查每一行是否全部为`True`（即该行所有值都非空）。最后，用这个布尔序列索引原DataFrame，就得到了删除空值行后的数据。

![](img/9b22353c0cc05cce04be4209c0b0e90c_47.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_49.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_51.png)

原始数据有1060行，删除空值行后剩下982行。删除的数据量不大，因此这种方法在此场景下是可行的。

![](img/9b22353c0cc05cce04be4209c0b0e90c_53.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_55.png)

**方法二：使用邻近值填充空值**
如果希望保留所有数据行，可以使用填充法。对于时间序列数据，常用前后时刻的值来填充当前的空值，因为相邻时刻的温度通常变化不大。

![](img/9b22353c0cc05cce04be4209c0b0e90c_57.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_59.png)

我们可以使用`fillna`方法，并指定填充方式（`method`）和轴向（`axis`）。

首先进行向前填充（用上一个有效值填充）：
```python
df_filled = df.fillna(method='ffill', axis=0)
```
`axis=0`表示沿着列的方向（纵向）寻找邻近值进行填充。

![](img/9b22353c0cc05cce04be4209c0b0e90c_61.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_63.png)

但向前填充可能无法处理数据开头部分的空值（因为前面没有值）。为了确保所有空值都被填充，我们可以再进行一次向后填充：
```python
df_filled = df_filled.fillna(method='bfill', axis=0)
```
这样，先向前填充，再向后填充，就能确保所有空值都被邻近的有效值覆盖。

填充完成后，我们需要验证是否还有空值残留。可以使用`isna`和`any`函数进行检查。

![](img/9b22353c0cc05cce04be4209c0b0e90c_65.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_67.png)

检查每一列是否还有空值：
```python
has_null = df_filled.isna().any(axis=0)
```
如果`has_null`的结果全为`False`，则说明所有列中都已没有空值。

![](img/9b22353c0cc05cce04be4209c0b0e90c_69.png)

检查每一行是否还有空值：
```python
has_null_row = df_filled.isna().any(axis=1)
```
如果结果中也全为`False`，则证明数据已完全填充干净。

![](img/9b22353c0cc05cce04be4209c0b0e90c_71.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_73.png)

---

![](img/9b22353c0cc05cce04be4209c0b0e90c_75.png)

本节课中我们一起学习了如何处理数据集中的空值。我们通过一个冷库温度数据的实例，实践了两种核心方法：**删除空值行**和**使用前后邻近值填充空值**。掌握这些数据清洗的基本技能，是为后续进行更复杂的数据分析和建模工作打下坚实基础。

![](img/9b22353c0cc05cce04be4209c0b0e90c_77.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_79.png)

![](img/9b22353c0cc05cce04be4209c0b0e90c_81.png)

下一节，我们将继续学习如何处理数据中的重复值和异常值。