# Python金融量化+股票交易：P43：获取给定区间全部数据 📊

![](img/5ae11cea1f442a37f00ad537138b5944_0.png)

![](img/5ae11cea1f442a37f00ad537138b5944_2.png)

![](img/5ae11cea1f442a37f00ad537138b5944_4.png)

![](img/5ae11cea1f442a37f00ad537138b5944_6.png)

![](img/5ae11cea1f442a37f00ad537138b5944_8.png)

在本节课中，我们将学习如何通过循环遍历指定日期区间，获取每一天的股票因子数据，并将这些数据整理成一个结构化的DataFrame。这是构建量化分析数据集的关键步骤。

![](img/5ae11cea1f442a37f00ad537138b5944_10.png)

![](img/5ae11cea1f442a37f00ad537138b5944_12.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间区间内的全部数据。

![](img/5ae11cea1f442a37f00ad537138b5944_14.png)

![](img/5ae11cea1f442a37f00ad537138b5944_16.png)

![](img/5ae11cea1f442a37f00ad537138b5944_18.png)

## 核心思路与步骤

![](img/5ae11cea1f442a37f00ad537138b5944_20.png)

![](img/5ae11cea1f442a37f00ad537138b5944_22.png)

![](img/5ae11cea1f442a37f00ad537138b5944_24.png)

核心思路是遍历指定日期区间内的每一天，对每一天都执行获取因子数据的操作，然后将每天的数据拼接成一个总表。

![](img/5ae11cea1f442a37f00ad537138b5944_26.png)

![](img/5ae11cea1f442a37f00ad537138b5944_28.png)

![](img/5ae11cea1f442a37f00ad537138b5944_30.png)

以下是实现这一过程的具体步骤：

![](img/5ae11cea1f442a37f00ad537138b5944_32.png)

![](img/5ae11cea1f442a37f00ad537138b5944_34.png)

1.  **确定日期区间长度**：首先需要知道我们要遍历的总天数。
2.  **循环遍历每一天**：使用 `for` 循环，遍历日期序列中的每一个日期。
3.  **获取每日数据**：在循环内部，调用获取因子值的函数，传入当前日期的索引，以获取该日所有股票的数据。
4.  **整理每日数据**：将获取到的每日数据（通常是一个多维数组）整理成一个包含日期、股票代码和因子值的临时DataFrame。
5.  **拼接数据**：将每次循环生成的临时DataFrame，通过 `pandas.concat` 函数，逐日拼接到一个总的DataFrame中。

![](img/5ae11cea1f442a37f00ad537138b5944_36.png)

![](img/5ae11cea1f442a37f00ad537138b5944_38.png)

![](img/5ae11cea1f442a37f00ad537138b5944_40.png)

## 代码实现详解

![](img/5ae11cea1f442a37f00ad537138b5944_42.png)

![](img/5ae11cea1f442a37f00ad537138b5944_44.png)

![](img/5ae11cea1f442a37f00ad537138b5944_46.png)

以下是实现上述逻辑的核心代码框架：

![](img/5ae11cea1f442a37f00ad537138b5944_48.png)

![](img/5ae11cea1f442a37f00ad537138b5944_50.png)

```python
# 假设 dates 是日期序列，factor_data 是获取单日因子数据的函数
total_days = len(dates)  # 步骤1：获取总天数
total_df = pd.DataFrame()  # 初始化一个空的总DataFrame

![](img/5ae11cea1f442a37f00ad537138b5944_52.png)

![](img/5ae11cea1f442a37f00ad537138b5944_54.png)

for i in range(total_days):  # 步骤2：循环遍历每一天
    # 步骤3：获取第i天的因子数据
    daily_raw_data = factor_data(i)

    # 步骤4：整理数据，提取有效部分（例如第三个维度的数据）
    # 假设有效数据在 daily_raw_data[0, 2, :] 这个结构中
    values = daily_raw_data[0, 2, :]

    # 创建临时DataFrame，包含因子值
    temp_df = pd.DataFrame(values, columns=['factor_value'])

    # 为临时DataFrame添加日期列
    temp_df['date'] = dates[i]

    # （可选）添加股票代码列，假设有 stock_codes 列表
    # temp_df['stock_code'] = stock_codes

    # 步骤5：将临时DataFrame拼接到总表
    total_df = pd.concat([total_df, temp_df], ignore_index=True)
```

![](img/5ae11cea1f442a37f00ad537138b5944_56.png)

![](img/5ae11cea1f442a37f00ad537138b5944_58.png)

![](img/5ae11cea1f442a37f00ad537138b5944_60.png)

## 关键注意事项

![](img/5ae11cea1f442a37f00ad537138b5944_62.png)

![](img/5ae11cea1f442a37f00ad537138b5944_64.png)

![](img/5ae11cea1f442a37f00ad537138b5944_66.png)

在实现过程中，有几个关键点需要注意：

![](img/5ae11cea1f442a37f00ad537138b5944_68.png)

![](img/5ae11cea1f442a37f00ad537138b5944_70.png)

![](img/5ae11cea1f442a37f00ad537138b5944_72.png)

![](img/5ae11cea1f442a37f00ad537138b5944_74.png)

*   **数据提取**：从 `factor_data` 函数返回的原始数据中，需要根据其具体结构（例如三维数组 `[0, 2, :]`）准确提取出我们需要的因子值序列。
*   **列名定义**：在创建临时DataFrame时，需要为数据列定义清晰的列名，如 `‘factor_value’`、`‘date’`。
*   **拼接操作**：使用 `pd.concat` 进行拼接时，通常需要设置 `ignore_index=True` 来重置索引，避免索引重复。
*   **执行效率**：如果遍历的日期区间很长（例如一年），循环执行可能会比较耗时。在实际应用中，这是获取历史数据必须经历的过程。

![](img/5ae11cea1f442a37f00ad537138b5944_76.png)

![](img/5ae11cea1f442a37f00ad537138b5944_78.png)

![](img/5ae11cea1f442a37f00ad537138b5944_80.png)

## 结果验证

![](img/5ae11cea1f442a37f00ad537138b5944_82.png)

![](img/5ae11cea1f442a37f00ad537138b5944_84.png)

![](img/5ae11cea1f442a37f00ad537138b5944_86.png)

![](img/5ae11cea1f442a37f00ad537138b5944_88.png)

代码执行完毕后，我们可以查看最终生成的 `total_df` 的前几行，以验证数据是否符合预期。

![](img/5ae11cea1f442a37f00ad537138b5944_90.png)

![](img/5ae11cea1f442a37f00ad537138b5944_92.png)

```python
print(total_df.head())
```

![](img/5ae11cea1f442a37f00ad537138b5944_94.png)

![](img/5ae11cea1f442a37f00ad537138b5944_96.png)

![](img/5ae11cea1f442a37f00ad537138b5944_98.png)

![](img/5ae11cea1f442a37f00ad537138b5944_100.png)

输出应该包含 `date` 和 `factor_value` 等列，每一行代表一只股票在特定日期的因子值。这样，我们就成功地将一个时间区间内的离散日数据，整合成了一个连续、规整的数据集，为后续的分析和建模打下了基础。

![](img/5ae11cea1f442a37f00ad537138b5944_102.png)

![](img/5ae11cea1f442a37f00ad537138b5944_104.png)

![](img/5ae11cea1f442a37f00ad537138b5944_106.png)

本节课中我们一起学习了如何通过循环遍历和拼接操作，获取指定日期区间内完整的股票因子数据。这个方法虽然直接，但却是构建量化分析基础数据集的通用且有效的手段。