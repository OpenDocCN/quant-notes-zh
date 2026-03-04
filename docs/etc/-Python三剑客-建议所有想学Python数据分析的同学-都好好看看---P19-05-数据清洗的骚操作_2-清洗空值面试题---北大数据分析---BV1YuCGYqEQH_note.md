# Python三剑客：P19：05 数据清洗的骚操作_2.清洗空值面试题

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_1.png)

在本节课中，我们将通过一个真实的面试题，学习如何处理数据集中的空值。我们将使用Pandas库来加载数据，并演示两种清洗空值的核心方法：删除包含空值的行，以及使用邻近值进行填充。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_3.png)

---

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_5.png)

## 概述与数据加载

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_7.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_9.png)

首先，我们来看一个面试题。题目描述了一个冷库的温度数据场景，数据来自一个Excel表格，其中包含七个温度采集设备（列1-7）每分钟记录的温度数据。我们的目标是使用前四个设备（列1-4）的数据来建立模型，以预测后三个设备（列5-7）的温度。在建立模型前，我们需要对原始数据中的空值（即“丢帧”数据）进行预处理。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_11.png)

以下是加载数据的代码：

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_13.png)

```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_15.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_17.png)

加载后，`df` 是一个DataFrame。列1-7代表七个设备，每一行代表某一分钟所有设备采集到的温度值。

---

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_19.png)

## 提取目标数据

我们的清洗工作主要针对用于建模的1-4号设备数据。因此，我们首先从原始数据中提取这四列。

```python
df_target = df[['1', '2', '3', '4']]
```

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_21.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_23.png)

现在，`df_target` 只包含我们需要处理的四列数据，其中存在一些空值。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_25.png)

---

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_27.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_29.png)

## 方法一：删除包含空值的行

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_31.png)

处理空值最直接的方法之一是删除包含任何空值的整行数据。Pandas提供了 `dropna` 方法来实现这个功能。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_33.png)

在 `dropna` 方法中，参数 `axis=0` 表示按行操作（删除行），`axis=1` 表示按列操作（删除列）。我们的目标是删除有空值的行。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_35.png)

```python
df_dropped = df_target.dropna(axis=0)
```

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_37.png)

执行后，`df_dropped` 中所有在1-4列存在空值的行都被删除了。我们可以对比原始数据行数（1060行）和处理后数据行数（例如982行），来评估删除操作是否可行。如果删除的数据比例不高，这种方法就是有效的。

另一种实现删除的方法是结合 `notnull()` 和 `all()` 函数进行布尔索引：

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_39.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_41.png)

```python
# 检查每一行在1-4列是否全部为非空
mask = df_target.notnull().all(axis=1)
# 使用布尔索引选择非空行
df_dropped_alternative = df_target.loc[mask]
```

这种方法得到的结果与直接使用 `dropna` 是一致的。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_43.png)

---

## 方法二：使用邻近值填充空值

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_45.png)

如果删除数据会导致信息损失过大，我们可以选择填充空值。对于时间序列数据（如每分钟的温度），常用前后时刻的值来填充当前的空值，因为相邻时刻的温度通常变化不大。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_47.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_49.png)

Pandas的 `fillna` 方法支持这种填充方式。参数 `method=‘ffill’` 表示用前面的值填充（向前填充），`method=‘bfill’` 表示用后面的值填充（向后填充）。参数 `axis=0` 表示沿着列的方向（垂直方向）寻找邻近值。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_51.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_53.png)

首先，我们尝试向前填充：

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_55.png)

```python
df_filled_ffill = df_target.fillna(method='ffill', axis=0)
```

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_57.png)

但是，如果某一列开头的几行就是空值，前面没有值可以用于填充，这些空值依然会保留。为了解决这个问题，我们可以再执行一次向后填充：

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_59.png)

```python
df_filled = df_filled_ffill.fillna(method='bfill', axis=0)
```

通过先后执行向前和向后填充，可以确保所有的空值都被邻近的有效温度值所替换。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_61.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_63.png)

---

## 验证清洗结果

无论采用哪种方法，清洗后都需要验证是否还存在空值。我们可以使用 `isnull()` 结合 `any()` 函数来检查。

对于删除法，由于空值行已被移除，检查结果应为没有空值：

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_65.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_67.png)

```python
# 检查是否还有空值（按行检查）
has_null_rows = df_dropped.isnull().any(axis=1).any()
# 检查是否还有空值（按列检查）
has_null_cols = df_dropped.isnull().any(axis=0).any()
```

对于填充法，我们也需要进行同样的检查：

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_69.png)

```python
# 检查填充后的DataFrame是否还有空值
is_clean = df_filled.isnull().any(axis=0).any()
# 或者分别检查每一列
col_check = df_filled.isnull().any(axis=0)
print(col_check)  # 如果四列都是False，说明清洗干净了
```

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_71.png)

如果输出显示所有列都没有空值（即全为False），则说明我们的数据清洗是成功的。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_73.png)

---

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_75.png)

## 总结

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_77.png)

本节课中，我们一起学习了一个数据清洗空值的实战案例。我们掌握了两种核心方法：
1.  **删除空值行**：使用 `df.dropna(axis=0)` 或布尔索引 `df.loc[df.notnull().all(axis=1)]`。
2.  **填充空值**：对于时间序列数据，使用 `df.fillna(method=‘ffill’, axis=0)` 和 `df.fillna(method=‘bfill’, axis=0)` 组合进行前后填充。

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_79.png)

![](img/5c77bd8b6f7ce5bb3ef36c6ed724d60a_81.png)

通过提取目标数据、选择合适的方法进行处理，并最终验证结果，我们能够有效地清洗数据集中的空值，为后续的数据分析和建模工作打下坚实的基础。下一节，我们将探讨如何处理数据中的重复值和异常值。