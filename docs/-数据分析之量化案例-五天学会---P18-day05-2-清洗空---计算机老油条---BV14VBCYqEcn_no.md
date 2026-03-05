# 数据分析之量化案例：P18：Day05-2. 清洗空值面试题 📊

![](img/755af7084245355ceca68c8389c1d9cc_1.png)

![](img/755af7084245355ceca68c8389c1d9cc_3.png)

在本节中，我们将通过一个真实的面试题，学习如何处理数据集中的空值（缺失值）。我们将使用一个冷库温度数据的案例，演示如何加载数据、识别空值，并运用删除和填充两种策略进行数据清洗。

![](img/755af7084245355ceca68c8389c1d9cc_5.png)

![](img/755af7084245355ceca68c8389c1d9cc_7.png)

## 数据加载与查看

![](img/755af7084245355ceca68c8389c1d9cc_9.png)

![](img/755af7084245355ceca68c8389c1d9cc_11.png)

首先，我们需要将存储在Excel文件中的数据加载到Pandas DataFrame中，以便查看其结构。

![](img/755af7084245355ceca68c8389c1d9cc_13.png)

```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```

![](img/755af7084245355ceca68c8389c1d9cc_15.png)

![](img/755af7084245355ceca68c8389c1d9cc_17.png)

加载后的数据包含7列，分别对应7个温度采集设备（编号1-7）。每一行数据代表在某一分钟（例如15:17:01）所有设备采集到的温度值。我们的目标是使用前4个必要设备（1-4列）的数据来预测后3个设备（5-7列）的温度。但在构建预测模型前，必须对原始数据中存在的空值（丢帧数据）进行预处理。

![](img/755af7084245355ceca68c8389c1d9cc_19.png)

## 空值清洗策略

原始数据中存在缺失值。处理缺失值主要有两种方法：删除含有空值的行，或使用合理值填充空值。以下将分别演示这两种方法。

![](img/755af7084245355ceca68c8389c1d9cc_21.png)

### 方法一：删除含有空值的行

![](img/755af7084245355ceca68c8389c1d9cc_23.png)

![](img/755af7084245355ceca68c8389c1d9cc_25.png)

我们可以选择直接删除任何在1-4列中含有空值的整行数据。这种方法简单直接，适用于缺失数据比例不高的情况。

![](img/755af7084245355ceca68c8389c1d9cc_27.png)

![](img/755af7084245355ceca68c8389c1d9cc_29.png)

首先，我们提取出前四列数据：

![](img/755af7084245355ceca68c8389c1d9cc_31.png)

![](img/755af7084245355ceca68c8389c1d9cc_33.png)

```python
df_target = df[['1', '2', '3', '4']]
```

![](img/755af7084245355ceca68c8389c1d9cc_35.png)

![](img/755af7084245355ceca68c8389c1d9cc_37.png)

接着，使用 `dropna` 方法删除含有空值的行。参数 `axis=0` 表示按行操作。

```python
df_dropped = df_target.dropna(axis=0)
```

![](img/755af7084245355ceca68c8389c1d9cc_39.png)

![](img/755af7084245355ceca68c8389c1d9cc_41.png)

我们也可以使用 `notnull` 和 `all` 方法组合来实现相同的效果：

![](img/755af7084245355ceca68c8389c1d9cc_43.png)

```python
df_dropped_alt = df_target[df_target.notnull().all(axis=1)]
```

原始数据有1060行，删除空值后剩余982行。删除的数据量不大，因此这种方法是可行的。

![](img/755af7084245355ceca68c8389c1d9cc_45.png)

### 方法二：使用近邻值填充空值

![](img/755af7084245355ceca68c8389c1d9cc_47.png)

![](img/755af7084245355ceca68c8389c1d9cc_49.png)

![](img/755af7084245355ceca68c8389c1d9cc_51.png)

另一种策略是填充空值，而不是删除整行。对于时间序列数据（如每分钟采集的温度），通常使用前后相邻时刻的值进行填充，因为相邻时刻的温度变化通常较小。

![](img/755af7084245355ceca68c8389c1d9cc_53.png)

![](img/755af7084245355ceca68c8389c1d9cc_55.png)

我们使用 `fillna` 方法进行填充。参数 `method=‘ffill’` 表示用上方（前一个时间点）的值填充，`axis=0` 表示沿列方向（垂直方向）操作。

![](img/755af7084245355ceca68c8389c1d9cc_57.png)

```python
df_filled = df_target.fillna(method='ffill', axis=0)
```

![](img/755af7084245355ceca68c8389c1d9cc_59.png)

仅使用前向填充可能无法处理第一行就是空值的情况。因此，我们可以再执行一次后向填充（`method=‘bfill’`）以确保所有空值都被处理。

![](img/755af7084245355ceca68c8389c1d9cc_61.png)

```python
df_filled = df_filled.fillna(method='bfill', axis=0)
```

![](img/755af7084245355ceca68c8389c1d9cc_63.png)

### 验证清洗结果

填充完成后，需要验证数据中是否还存在空值。我们可以使用 `isnull` 和 `any` 方法进行检查。

![](img/755af7084245355ceca68c8389c1d9cc_65.png)

![](img/755af7084245355ceca68c8389c1d9cc_67.png)

检查每一行是否还有空值：
```python
has_null_row = df_filled.isnull().any(axis=1)
# 如果结果全为False，则说明所有行都已无空值
```

检查每一列是否还有空值：
```python
has_null_col = df_filled.isnull().any(axis=0)
# 如果结果全为False，则说明所有列都已无空值
```

![](img/755af7084245355ceca68c8389c1d9cc_69.png)

![](img/755af7084245355ceca68c8389c1d9cc_71.png)

通过上述检查，可以确认我们的数据清洗是完整和有效的。

![](img/755af7084245355ceca68c8389c1d9cc_73.png)

## 本节总结

![](img/755af7084245355ceca68c8389c1d9cc_75.png)

![](img/755af7084245355ceca68c8389c1d9cc_77.png)

在本节中，我们共同学习了一个处理数据空值的实际案例。我们掌握了两种核心的清洗策略：
1.  **删除**：使用 `dropna()` 直接删除含有空值的行。
2.  **填充**：使用 `fillna()` 并配合 `method` 参数，用前向或后向的邻近值填充空值。

![](img/755af7084245355ceca68c8389c1d9cc_79.png)

![](img/755af7084245355ceca68c8389c1d9cc_81.png)

我们还学习了如何使用 `isnull().any()` 来验证清洗后的数据是否已不存在空值。选择哪种方法取决于数据的具体情况和业务需求。在数据缺失不多时，删除是高效的选择；而当需要保留所有数据样本时，填充则是更优的策略。