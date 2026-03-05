# Python金融量化：P43：获取给定区间全部数据 📊

![](img/872f979d8e9af46b39c55c32ffa8dbf2_0.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_2.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_4.png)

在本节课中，我们将学习如何通过循环遍历日期序列，获取指定时间区间内每一天的金融数据，并将这些数据整理成一个结构化的DataFrame。这个过程是构建量化分析数据集的基础步骤。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_6.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_8.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_10.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间区间内的全部数据。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_12.png)

## 核心思路与流程

![](img/872f979d8e9af46b39c55c32ffa8dbf2_14.png)

我们的目标是：给定一个日期序列，循环获取其中每一天的数据，并将所有日期的数据汇总到一个总表中。核心思路是使用`for`循环遍历日期，在每次循环中获取当日数据，并将其添加到一个不断增长的DataFrame中。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_16.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_18.png)

以下是实现此目标的具体步骤：

![](img/872f979d8e9af46b39c55c32ffa8dbf2_20.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_22.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_24.png)

1.  **确定循环范围**：首先需要知道日期序列的长度，以确定需要循环多少次。
2.  **遍历每日数据**：使用`for`循环，按索引`i`遍历日期序列中的每一天。
3.  **获取单日数据**：在循环体内，调用获取数据的方法，传入当前日期的索引`i`，以得到当日的三维数据数组。
4.  **提取有效数据**：从返回的三维数组中，通过索引定位（例如`[0, 2, :]`）提取出我们需要的实际指标值。
5.  **构建单日数据行**：为每一天的数据创建一个小的DataFrame，至少包含两列：`日期`和`指标值`。
6.  **拼接至总表**：在每次循环中，将新创建的单日DataFrame拼接到一个初始为空的“总”DataFrame中。
7.  **检查与验证**：循环结束后，检查最终的总DataFrame，确保其包含预期的日期、股票名称和指标值。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_26.png)

## 代码实现详解

![](img/872f979d8e9af46b39c55c32ffa8dbf2_28.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_30.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_32.png)

现在，让我们通过代码来具体实现上述流程。我们将逐步构建循环和数据处理逻辑。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_34.png)

首先，我们需要确定日期序列的长度，这代表了我们要获取数据的总天数。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_36.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_38.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_40.png)

```python
# 假设 dates 是我们的日期序列
total_days = len(dates)
```

接下来，我们初始化一个空的DataFrame作为总表，用于累积所有日期的数据。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_42.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_44.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_46.png)

```python
import pandas as pd
total_df = pd.DataFrame()
```

![](img/872f979d8e9af46b39c55c32ffa8dbf2_48.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_50.png)

以下是循环遍历每一天并处理数据的核心代码块：

![](img/872f979d8e9af46b39c55c32ffa8dbf2_52.png)

```python
for i in range(total_days):
    # 1. 获取第i天的原始数据（假设get_data是获取数据的方法）
    daily_raw_data = get_data(i)

    # 2. 从三维数据中提取所需的指标值（例如索引[0,2,:]位置）
    # 这里的索引需要根据实际数据结构调整
    factor_value = daily_raw_data.loc[0, 2, :]

    # 3. 构建代表第i天数据的单行DataFrame
    daily_df = pd.DataFrame({
        '日期': [dates[i]],  # 日期列
        '因子值': [factor_value]   # 指标值列，列名可自定义
    })

    # 4. 将单日DataFrame拼接到总表中
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

![](img/872f979d8e9af46b39c55c32ffa8dbf2_54.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_56.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_58.png)

**代码关键点说明**：
*   `get_data(i)`: 这是一个示意函数，代表根据索引`i`获取第`i`天数据的方法。在实际应用中，你需要替换为真实的API调用或数据查询逻辑。
*   `.loc[0, 2, :]`: 这是一个示例索引操作，用于从三维数组中提取特定位置的数据。你需要根据你的数据实际形状和所需指标的位置来调整这些索引数字。
*   `pd.concat`: 这是Pandas中用于拼接DataFrame的函数。`ignore_index=True`参数确保拼接后生成新的连续索引。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_60.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_62.png)

## 执行结果与验证

![](img/872f979d8e9af46b39c55c32ffa8dbf2_64.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_66.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_68.png)

代码执行完毕后，`total_df`就是我们需要的汇总数据表。由于循环处理的数据量可能较大（例如一年的交易日数据），执行速度会相对较慢。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_70.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_72.png)

我们可以使用`head()`方法查看数据表的前几行，以验证数据是否符合预期。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_74.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_76.png)

```python
print(total_df.head())
```

![](img/872f979d8e9af46b39c55c32ffa8dbf2_78.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_80.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_82.png)

预期的输出应该是一个DataFrame，包含`日期`和`因子值`两列，每一行对应一个交易日的数据。如果发现总表为空或数据不正确，需要检查：
1.  数据获取函数`get_data(i)`是否返回了有效数据。
2.  数据提取的索引`[0, 2, :]`是否正确。
3.  拼接操作`pd.concat`是否被正确赋值回`total_df`（即使用了`total_df = pd.concat(...)`）。

![](img/872f979d8e9af46b39c55c32ffa8dbf2_84.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_86.png)

## 总结

![](img/872f979d8e9af46b39c55c32ffa8dbf2_88.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_90.png)

![](img/872f979d8e9af46b39c55c32ffa8dbf2_92.png)

本节课中我们一起学习了如何获取给定时间区间内的全部金融数据。我们通过**`for`循环遍历日期**，在循环体内**获取并提取单日数据**，然后将其**构建成DataFrame行**，最后使用**`pd.concat`进行循环拼接**，从而将所有日期的数据整合到一个结构化的总表中。这个方法虽然需要手动编写循环，但它是处理时间序列数据、构建量化分析数据集的通用且基础的方法。掌握这一流程，你就能为后续更复杂的金融分析和策略回测准备好规整的数据基础。