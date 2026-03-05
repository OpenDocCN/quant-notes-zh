# 量化交易教程：P43：获取给定区间全部数据 📊

在本节课中，我们将学习如何通过循环遍历一个日期序列，获取指定时间区间内每一天的股票因子数据，并将这些数据整理成一个结构化的总表。这是处理时间序列金融数据的基础操作。

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何批量获取一个时间区间内的所有数据。

![](img/6908d07a64ee22e424b7cfd531930d98_1.png)

![](img/6908d07a64ee22e424b7cfd531930d98_2.png)

![](img/6908d07a64ee22e424b7cfd531930d98_3.png)

## 核心思路与步骤

![](img/6908d07a64ee22e424b7cfd531930d98_4.png)

![](img/6908d07a64ee22e424b7cfd531930d98_5.png)

核心思路是：我们有一个日期序列，需要遍历其中的每一天，获取当天的因子数据，然后将每天的数据汇总到一个总的数据表中。

![](img/6908d07a64ee22e424b7cfd531930d98_7.png)

以下是实现此功能的具体步骤：

![](img/6908d07a64ee22e424b7cfd531930d98_8.png)

1.  **遍历日期序列**：使用 `for` 循环遍历给定的日期序列。
2.  **获取每日数据**：在循环中，针对每一天，调用数据获取函数（例如 `get_factor_values`）来取得当天的因子值。
3.  **处理数据格式**：获取的数据通常是多维数组（例如三维）。我们需要通过索引（如 `[0, 0, :]`）提取出我们需要的实际数值序列。
4.  **构建每日数据表**：将提取出的数值序列与对应的日期组合，构建成一个包含“日期”和“因子值”两列的 `DataFrame`。
5.  **汇总到总表**：在每次循环中，将新构建的每日 `DataFrame` 拼接到一个初始为空的“总表” `DataFrame` 中。

## 代码实现详解

![](img/6908d07a64ee22e424b7cfd531930d98_10.png)

![](img/6908d07a64ee22e424b7cfd531930d98_11.png)

以下是实现上述步骤的关键代码片段和解释。

首先，我们初始化一个空的 `DataFrame` 作为总表，用于累积每天的数据。

![](img/6908d07a64ee22e424b7cfd531930d98_13.png)

```python
total_df = pd.DataFrame()  # 创建空的总表DataFrame
```

接着，我们开始遍历日期序列。假设 `date_series` 是包含所有目标日期的序列。

![](img/6908d07a64ee22e424b7cfd531930d98_15.png)

![](img/6908d07a64ee22e424b7cfd531930d98_16.png)

```python
for i in range(len(date_series)):  # 遍历每一天
    current_date = date_series[i]  # 获取当前日期
```

![](img/6908d07a64ee22e424b7cfd531930d98_17.png)

在循环内部，我们获取当前日期 `current_date` 的因子数据。这里假设 `get_daily_factor` 是获取单日数据的函数。

![](img/6908d07a64ee22e424b7cfd531930d98_19.png)

```python
    # 获取第i天的因子数据，返回的daily_data可能是一个三维数组
    daily_data = get_daily_factor(factor_name, current_date)
```

由于返回的数据可能是多维的，我们需要提取出有效的数值部分。通过 `.shape` 属性查看维度后，使用 `.iloc` 进行索引提取。

![](img/6908d07a64ee22e424b7cfd531930d98_21.png)

```python
    # 提取有效数据。假设第三维度（索引2）包含所有股票的值
    values = daily_data.iloc[0, 0, :]  # 获取实际数值序列
```

![](img/6908d07a64ee22e424b7cfd531930d98_22.png)

![](img/6908d07a64ee22e424b7cfd531930d98_23.png)

![](img/6908d07a64ee22e424b7cfd531930d98_24.png)

现在，我们有了数值序列 `values` 和对应的日期 `current_date`。接下来，将它们构建成一个结构化的每日 `DataFrame`。

```python
    # 构建每日的DataFrame
    daily_df = pd.DataFrame(values)  # 将数值序列转为DataFrame
    daily_df.columns = ['factor_value']  # 为数值列命名
    daily_df['date'] = current_date  # 新增一列，记录日期
```

最后，也是关键的一步，将当天的 `daily_df` 拼接到总表 `total_df` 中。这里使用 `pd.concat` 函数。

![](img/6908d07a64ee22e424b7cfd531930d98_26.png)

```python
    # 将每日数据拼接到总表中
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

![](img/6908d07a64ee22e424b7cfd531930d98_27.png)

![](img/6908d07a64ee22e424b7cfd531930d98_28.png)

![](img/6908d07a64ee22e424b7cfd531930d98_29.png)

**注意**：必须将 `pd.concat` 的结果重新赋值给 `total_df`，否则拼接操作不会生效。

![](img/6908d07a64ee22e424b7cfd531930d98_30.png)

![](img/6908d07a64ee22e424b7cfd531930d98_31.png)

![](img/6908d07a64ee22e424b7cfd531930d98_32.png)

## 运行结果与检查

代码执行完毕后，我们可以查看总表的前几行，以确认数据格式是否符合预期。

![](img/6908d07a64ee22e424b7cfd531930d98_34.png)

```python
print(total_df.head())
```

![](img/6908d07a64ee22e424b7cfd531930d98_35.png)

![](img/6908d07a64ee22e424b7cfd531930d98_36.png)

![](img/6908d07a64ee22e424b7cfd531930d98_37.png)

输出应类似以下结构，包含日期、股票代码（如果数据包含）和因子值：

![](img/6908d07a64ee22e424b7cfd531930d98_38.png)

| date       | stock_code | factor_value |
| :--------- | :--------- | :----------- |
| 2019-01-02 | 000001     | 0.15         |
| 2019-01-02 | 000002     | 0.22         |
| 2019-01-03 | 000001     | 0.18         |

![](img/6908d07a64ee22e424b7cfd531930d98_40.png)

## 性能注意事项

![](img/6908d07a64ee22e424b7cfd531930d98_41.png)

![](img/6908d07a64ee22e424b7cfd531930d98_42.png)

![](img/6908d07a64ee22e424b7cfd531930d98_43.png)

处理较长的时间区间（例如一年）时，循环获取数据可能较慢。这是正常现象，因为涉及大量的网络请求或数据计算。在实际应用中，可以根据需要调整时间区间长度。

![](img/6908d07a64ee22e424b7cfd531930d98_44.png)

![](img/6908d07a64ee22e424b7cfd531930d98_45.png)

本节课中我们一起学习了如何通过循环遍历获取指定时间区间内的全部因子数据，并整理成规整的 `DataFrame` 格式。这个方法虽然直接，但却是处理时间序列数据、进行后续分析和建模的重要基础步骤。