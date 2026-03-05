# Python金融量化与股票交易：P18：清洗空值面试题 📊

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_1.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_3.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_5.png)

在本节课中，我们将通过一个真实的面试题，学习如何处理数据集中的空值（缺失值）。我们将使用Pandas库来加载数据，并演示两种处理空值的核心方法：删除包含空值的行，以及使用邻近值进行填充。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_7.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_9.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_11.png)

## 数据加载与理解

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_13.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_15.png)

首先，我们需要加载并理解数据。题目提供了一个Excel文件，其中记录了冷库中七个温度采集设备（编号1-7）每隔一分钟采集的温度数据。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_17.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_19.png)

以下是加载数据的代码：
```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```
加载后，`df` 是一个DataFrame。其中，列名1-7代表七个设备，每一行代表一个时间点（每分钟）采集到的七组温度值。我们的目标是使用前四个设备（1-4列）的数据来构建模型，以预测后三个设备（5-7列）的温度。作为数据预处理的第一步，我们需要处理前四列数据中的空值。

## 处理空值：删除法

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_21.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_23.png)

第一种处理空值的方法是直接删除包含空值的行。这种方法适用于数据量较大且缺失值不多的场景。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_25.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_27.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_29.png)

以下是删除包含空值行的两种等效方法：

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_31.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_33.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_35.png)

**方法一：使用 `dropna` 函数**
```python
# axis=0 表示删除行（包含空值的行）
df_cleaned_drop = df[[1, 2, 3, 4]].dropna(axis=0)
```

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_37.png)

**方法二：使用布尔索引与 `notna` 和 `all` 函数**
```python
# 生成一个布尔序列，标记出那些在1-4列上完全没有空值的行
mask = df[[1, 2, 3, 4]].notna().all(axis=1)
# 利用这个布尔序列作为索引，筛选出符合条件的行
df_cleaned_mask = df.loc[mask]
```
原始数据有1060行，删除空值后剩余982行。由于删除的数据比例不高，这种方法在此案例中是可行的。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_39.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_41.png)

## 处理空值：填充法

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_43.png)

第二种处理空值的方法是使用邻近的值进行填充。这适用于我们希望保留所有数据行，或数据具有时间序列特性（前后时刻的值相近）的场景。对于温度数据，同一设备相邻时间点的读数通常变化不大，适合用此方法。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_45.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_47.png)

以下是使用前后邻近值进行填充的步骤：

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_49.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_51.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_53.png)

**步骤一：向前填充**
使用同一列中上一个非空值来填充当前空值。
```python
df_filled = df[[1, 2, 3, 4]].fillna(method='ffill', axis=0)
```

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_55.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_57.png)

**步骤二：向后填充**
由于数据起始行可能为空，向前填充无法处理。因此，我们再进行一次向后填充，使用同一列中下一个非空值来填充剩余的空值。
```python
df_filled = df_filled.fillna(method='bfill', axis=0)
```
经过向前和向后两次填充，理论上可以处理所有空值。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_59.png)

## 验证清洗结果

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_61.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_63.png)

完成填充后，我们需要验证数据中是否还存在空值。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_65.png)

以下是验证代码：
```python
# 检查每一列是否还存在任何空值
check_columns = df_filled.isna().any(axis=0)
print(check_columns)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_67.png)

# 检查每一行是否还存在任何空值
check_rows = df_filled.isna().any(axis=1)
print(check_rows.any())
```
如果输出结果中所有列和行的检查结果均为 `False`，则说明数据中的空值已被成功清洗干净。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_69.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_71.png)

## 总结

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_73.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_75.png)

本节课中，我们一起学习了如何处理数据集中的空值。我们通过一个冷库温度数据的面试题，实践了两种核心方法：
1.  **删除法**：使用 `dropna()` 或布尔索引直接移除包含空值的行。适用于数据缺失少的情况。
2.  **填充法**：使用 `fillna()` 并配合 `method` 参数（如 `‘ffill’`, `‘bfill’`），利用数据的邻近值进行填充。适用于希望保留数据量或数据具有连续性的情况。

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_77.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_79.png)

![](img/b5b5cd08e833e2e33a2c63183f63f1c6_81.png)

最后，我们使用 `isna().any()` 来验证清洗操作是否彻底。掌握这些数据清洗技能，是进行后续金融量化分析与建模的重要基础。下一节，我们将学习如何处理数据中的重复值和异常值。