# Python金融分析与量化交易实战教程：P39：4-获取给定区间全部数据 📊

![](img/1728b7afd50c65c49b8f74a2d6943c44_1.png)

![](img/1728b7afd50c65c49b8f74a2d6943c44_3.png)

在本节课中，我们将学习如何通过循环遍历一个给定的日期区间，获取每一天的特定金融因子数据，并将这些分散的数据整合到一个结构化的`DataFrame`中。这是构建量化分析数据基础的关键步骤。

![](img/1728b7afd50c65c49b8f74a2d6943c44_5.png)

![](img/1728b7afd50c65c49b8f74a2d6943c44_7.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间段内的全部数据。

![](img/1728b7afd50c65c49b8f74a2d6943c44_9.png)

## 遍历日期序列

![](img/1728b7afd50c65c49b8f74a2d6943c44_11.png)

我们的核心任务是遍历一个日期序列。这个序列包含了我们感兴趣的所有交易日。

![](img/1728b7afd50c65c49b8f74a2d6943c44_13.png)

以下是实现遍历的基本思路：
*   首先，确定日期序列的长度，这代表了总共有多少个交易日需要处理。
*   然后，使用一个`for`循环，按索引`i`遍历这个序列中的每一天。

```python
# 假设 dates 是一个日期序列
for i in range(len(dates)):
    current_date = dates[i]
    # 在此处处理 current_date 的数据
```

![](img/1728b7afd50c65c49b8f74a2d6943c44_15.png)

## 获取每日数据并处理

![](img/1728b7afd50c65c49b8f74a2d6943c44_17.png)

![](img/1728b7afd50c65c49b8f74a2d6943c44_19.png)

在循环体内，我们需要获取每一天对应的因子数据。这个数据通常是一个多维数组。

![](img/1728b7afd50c65c49b8f74a2d6943c44_21.png)

以下是处理每日数据的步骤：
*   调用数据获取函数，传入当前日期的索引`i`，以获取当天的因子值。
*   该函数返回一个三维数据。我们通常只关心第三个维度的全部数据，这包含了所有股票在该因子上的数值。
*   通过索引`[0, 0, :]`可以提取出这个一维数组。

```python
# 获取第 i 天的因子数据
daily_factor_data = get_factor_data(i)  # 假设的获取函数
# 提取有用的部分（第三个维度的全部数据）
useful_values = daily_factor_data[0, 0, :]
```

![](img/1728b7afd50c65c49b8f74a2d6943c44_23.png)

## 构建并汇总数据表

![](img/1728b7afd50c65c49b8f74a2d6943c44_25.png)

获取到每日数据后，我们不能仅仅打印它们，而是需要将其汇总到一个总表中。这个总表应包含日期、股票名称和因子值三列。

![](img/1728b7afd50c65c49b8f74a2d6943c44_27.png)

以下是构建汇总`DataFrame`的方法：
1.  **初始化总表**：在循环开始前，创建一个空的`DataFrame`作为容器。
2.  **创建每日数据表**：在每次循环中，用当天的数据创建一个临时的`DataFrame`。这个临时表至少应有两列：一列是日期，一列是因子值。
3.  **拼接数据**：使用`pandas.concat()`函数，将每日的临时表拼接到总表中。

![](img/1728b7afd50c65c49b8f74a2d6943c44_29.png)

```python
import pandas as pd

![](img/1728b7afd50c65c49b8f74a2d6943c44_31.png)

# 1. 初始化一个空的总表
total_df = pd.DataFrame()

for i in range(len(dates)):
    # 获取第 i 天的有用数据
    useful_values = get_factor_data(i)[0, 0, :]

    # 2. 创建每日的临时DataFrame
    daily_df = pd.DataFrame()
    daily_df[‘factor_value‘] = useful_values  # 因子值列
    daily_df[‘date‘] = dates[i]               # 日期列
    # 注意：此处假设 useful_values 的索引就是股票代码，否则需要额外处理股票名列

    # 3. 将每日数据拼接到总表
    total_df = pd.concat([total_df, daily_df], ignore_index=True)

![](img/1728b7afd50c65c49b8f74a2d6943c44_33.png)

# 查看汇总结果的前几行
print(total_df.head())
```

![](img/1728b7afd50c65c49b8f74a2d6943c44_35.png)

![](img/1728b7afd50c65c49b8f74a2d6943c44_37.png)

**注意**：在拼接操作后，必须将结果重新赋值给总表变量（如`total_df = pd.concat(...)`），否则数据不会被保存。处理较长时间段（如一年）的数据时，此过程可能需要一些时间。

![](img/1728b7afd50c65c49b8f74a2d6943c44_39.png)

![](img/1728b7afd50c65c49b8f74a2d6943c44_41.png)

本节课中我们一起学习了如何自动化地获取一个时间区间内的全部金融因子数据。我们通过遍历日期、提取每日数据、并利用`pandas`进行数据拼接，成功地将分散的数据整合成了一个结构清晰、便于后续分析的总表。这是进行历史回测和策略研究的重要数据准备步骤。