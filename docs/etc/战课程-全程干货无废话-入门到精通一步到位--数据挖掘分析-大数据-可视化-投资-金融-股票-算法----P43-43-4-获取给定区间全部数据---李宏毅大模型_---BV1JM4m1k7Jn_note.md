# Python金融分析与量化交易实战：P43：获取给定区间全部数据 📊

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_0.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_2.png)

在本节课中，我们将学习如何通过循环遍历指定日期区间，从数据源中逐日获取金融因子数据，并将这些数据整理成一个结构化的总表。这是构建量化分析数据集的关键步骤。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_4.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_6.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间段内的所有数据。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_8.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_10.png)

## 核心思路与步骤

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_12.png)

核心思路是：我们有一个日期序列，需要遍历其中的每一天，获取当天的因子数据，然后将每天的数据汇总到一个总的数据表中。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_14.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_16.png)

以下是实现这一目标的具体步骤：

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_18.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_20.png)

1.  **确定日期范围**：首先，我们需要明确要获取数据的起始日期和结束日期，从而确定需要遍历的天数。
2.  **循环遍历每一天**：使用 `for` 循环，遍历日期序列中的每一天。
3.  **获取每日数据**：在循环体内，调用获取单日数据的函数，传入当前循环的日期索引，以获取当天的因子值。
4.  **构建每日数据表**：将获取到的每日数据（因子值）与对应的日期信息组合，构建成一个小的 `DataFrame`。
5.  **汇总到总表**：在循环过程中，将每天构建的小 `DataFrame` 通过拼接操作，逐步汇总到一个总的 `DataFrame` 中。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_22.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_24.png)

## 代码实现详解

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_26.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_28.png)

接下来，我们详细拆解每一步的代码实现。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_30.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_32.png)

### 1. 遍历日期序列

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_34.png)

我们首先需要知道日期序列的长度，即总共有多少天需要处理。这可以通过计算日期序列的长度来实现。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_36.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_38.png)

```python
# 假设 dates 是一个包含所有目标日期的列表或序列
total_days = len(dates)
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_40.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_42.png)

然后，我们使用 `for` 循环来遍历这个序列。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_44.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_46.png)

```python
for i in range(total_days):
    # 在这里处理第 i 天的数据
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_48.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_50.png)

### 2. 获取每日因子数据

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_52.png)

在循环体内，对于第 `i` 天，我们需要获取其对应的因子数据。这通常通过一个函数来完成，该函数接收一个日期索引 `i` 作为参数。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_54.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_56.png)

```python
    # 调用函数获取第 i 天的因子数据
    daily_factor_data = get_factor_data(i)  # 假设的函数名
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_58.png)

获取到的 `daily_factor_data` 可能是一个多维数组。根据之前的知识，我们通常只关心其中代表具体数值的第三个维度。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_60.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_62.png)

```python
    # 提取有用的数据部分（例如第三个维度的全部数据）
    useful_values = daily_factor_data[0, 2, :]  # 这是一个示例索引，具体取决于数据结构
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_64.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_66.png)

### 3. 构建每日数据DataFrame

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_68.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_70.png)

获取到当天的数值后，我们需要将其与日期信息一起存储。我们创建一个新的 `DataFrame` 来存放这两列信息。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_72.png)

```python
    import pandas as pd

    # 创建一个临时的DataFrame来存放当天的数据
    daily_df = pd.DataFrame()
    # 添加“日期”列，值为当前遍历到的日期 dates[i]
    daily_df[‘日期‘] = [dates[i]]
    # 添加“因子值”列，值为我们提取出的 useful_values
    daily_df[‘因子值‘] = [useful_values]
    # 如果需要，还可以添加股票名称列
    # daily_df[‘股票名称‘] = [stock_name]
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_74.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_76.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_78.png)

### 4. 拼接数据到总表

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_80.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_82.png)

在循环开始前，我们需要初始化一个空的 `DataFrame` 作为总表。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_84.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_86.png)

```python
# 初始化一个空的DataFrame作为总表
total_dataframe = pd.DataFrame()
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_88.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_90.png)

在每次循环中，将当天构建的 `daily_df` 拼接到总表中。这里使用 `pd.concat` 函数。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_92.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_94.png)

```python
    # 将当天的DataFrame拼接到总表中
    total_dataframe = pd.concat([total_dataframe, daily_df], ignore_index=True)
```
**注意**：`pd.concat` 操作后必须将结果重新赋值给 `total_dataframe`，否则总表的内容不会更新。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_96.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_98.png)

### 5. 检查与等待

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_100.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_102.png)

由于处理一整年的数据（例如从2019年1月1日到2020年1月1日）可能涉及大量计算和网络请求，这个过程可能会比较慢。执行完毕后，我们可以查看总表的前几行以验证数据是否正确。

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_104.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_106.png)

```python
# 显示总表的前几行数据
print(total_dataframe.head())
```

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_108.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_110.png)

## 完整流程总结

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_112.png)

![](img/b6f858a0a21ff2711d09e88a5d88bb9b_114.png)

本节课中我们一起学习了如何获取一个给定时间区间内的全部金融因子数据。我们首先通过循环遍历日期序列中的每一天，然后针对每一天获取其因子数据并构建一个包含日期和数值的 `DataFrame`，最后使用 `pd.concat` 函数将所有日期的数据逐行拼接，汇总成一个完整的总表。这个过程虽然需要手动编写循环，但它是构建标准化量化数据集的必要步骤。通过这个方法，我们能够将原始的、按日分散的数据，整理成便于后续分析和建模的结构化表格。