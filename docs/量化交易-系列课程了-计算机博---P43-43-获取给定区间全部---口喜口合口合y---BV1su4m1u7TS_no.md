# Python金融分析+量化交易：P43：获取给定区间全部数据 📊

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_1.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_3.png)

在本节课中，我们将学习如何从给定的时间序列数据中，循环获取每一天的数据，并将这些数据整理成一个结构化的DataFrame。这个过程是金融数据分析中处理多日数据的基础。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_5.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_7.png)

## 概述

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_9.png)

上一节我们介绍了如何获取单日数据。本节中我们来看看如何获取一个时间区间内的所有数据。核心思路是遍历日期序列中的每一天，获取当天的数据，然后将所有日期的数据汇总到一个总表中。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_11.png)

## 遍历日期序列

首先，我们有一个日期序列。要获取其中每一天的数据，最直接的方法是使用循环。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_13.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_15.png)

以下是遍历日期序列的基本步骤：

1.  确定日期序列的长度，即总天数。
2.  使用 `for` 循环遍历每一天的索引。
3.  在循环中，根据索引获取对应日期的数据。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_17.png)

对应的核心代码逻辑如下：
```python
# 假设 dates 是日期序列
for i in range(len(dates)):
    # 获取第 i 天的数据
    daily_data = get_data_by_index(i)
```

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_19.png)

## 处理每日数据

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_21.png)

在循环体内，我们获取到的是单日的数据。根据之前的知识，单日数据可能是一个多维数组（例如三维数组）。我们通常只关心其中包含实际数值的部分。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_23.png)

以下是处理单日数据的步骤：

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_25.png)

1.  通过索引 `i` 调用数据获取函数。
2.  从返回的多维数组中，提取出我们需要的数值部分。例如，如果数据的 `shape` 显示有用信息在第三个维度，我们可以通过索引 `[:, :, :]` 来提取。
3.  将提取出的数值数组保存下来，用于后续构建表格。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_27.png)

代码示例如下：
```python
# 获取第 i 天的原始数据（假设为三维）
raw_data = get_data_by_index(i)
# 提取有用的数值部分（例如第三个维度的全部数据）
useful_values = raw_data.loc[:, :, :]  # 根据实际数据结构调整索引
```

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_29.png)

## 构建并汇总DataFrame

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_31.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_33.png)

我们最终的目标是得到一个总表（DataFrame），它至少包含三列：日期、股票名称和指标值。

以下是构建并汇总DataFrame的步骤：

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_35.png)

1.  在循环开始前，初始化一个空的DataFrame作为总表。
2.  在每次循环中，用当天的数据（日期和指标值）创建一个临时的、只有一行的DataFrame。
3.  将这个临时DataFrame拼接到总表中。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_37.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_39.png)

以下是关键操作代码：
```python
import pandas as pd

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_41.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_43.png)

# 1. 初始化总表
total_df = pd.DataFrame()

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_45.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_47.png)

# 在循环中：
for i in range(len(dates)):
    # ... 获取 useful_values ...
    current_date = dates[i]

    # 2. 创建临时DataFrame
    temp_df = pd.DataFrame({
        '日期': [current_date],
        '因子值': [useful_values]  # 列名可自定义
    })

    # 3. 拼接至总表
    total_df = pd.concat([total_df, temp_df], ignore_index=True)
```

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_49.png)

**注意**：`pd.concat` 操作后必须将结果赋值回原变量（如 `total_df = pd.concat(...)`），否则拼接不会生效。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_51.png)

## 执行与验证

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_53.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_55.png)

完成代码编写后，执行循环。处理较长时间区间（如一年）的数据可能需要一些时间。执行完毕后，使用 `total_df.head()` 查看数据前几行，验证数据是否包含日期、股票标识（如果已添加）和指标值这三列，并且格式符合预期。

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_57.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_59.png)

## 总结

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_61.png)

![](img/5809f1e755dcb4d941b8cdfe89a3d6eb_63.png)

本节课中我们一起学习了如何获取给定时间区间内的全部数据。我们通过循环遍历日期，逐日提取和处理数据，并最终将所有数据汇总到一个结构清晰的DataFrame中。这个方法虽然需要手动编写循环，但它是处理时间序列数据的基础且必要的技能。