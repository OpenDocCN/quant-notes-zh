# 2025数据分析：P14：股票买卖收益分析 📈💰

![](img/a40b3f54d1300a6adbd48572c6da40b6_1.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_3.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_5.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_7.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_9.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_11.png)

在本节课中，我们将学习如何使用Numpy和Pandas对一个具体的股票交易策略进行收益分析。我们将模拟一个从2010年开始，每月第一个交易日买入一手股票，并在每年最后一个交易日卖出所有股票的投资策略，并计算其总收益。

![](img/a40b3f54d1300a6adbd48572c6da40b6_13.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_15.png)

## 需求分析

![](img/a40b3f54d1300a6adbd48572c6da40b6_17.png)

上一节我们明确了课程目标，本节中我们来看看具体的需求。我们需要计算一个人从2010年1月1日开始，遵循特定买卖规则，到2020年2月为止的总收益。

以下是需求的核心要点：
*   **时间范围**：从2010年到2020年2月。
*   **交易规则**：
    *   **买入**：每月的第一个交易日，买入一手（即100股）股票。
    *   **卖出**：每年的最后一个交易日，卖出当年持有的所有股票。
*   **价格基准**：股票的买卖单价均使用当日的**开盘价**。
*   **收益计算**：总收益 = 卖出股票总收入 + 手中剩余股票估值 - 买入股票总成本。

## 数据准备与时间切片

![](img/a40b3f54d1300a6adbd48572c6da40b6_19.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_21.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_23.png)

首先，我们需要将数据的时间范围控制在2010年至2020年之间。假设原始数据的行索引已经是日期时间类型。

![](img/a40b3f54d1300a6adbd48572c6da40b6_25.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_27.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_29.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_31.png)

```python
# 假设 df 是原始的DataFrame，且索引为日期
new_df = df[‘2010-01-01’:‘2020-02’]
```
这段代码通过切片操作，`new_df` 将只包含2010年1月到2020年2月之间的数据。

![](img/a40b3f54d1300a6adbd48572c6da40b6_33.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_35.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_37.png)

## 计算买入股票的总成本

![](img/a40b3f54d1300a6adbd48572c6da40b6_39.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_41.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_43.png)

接下来，我们计算买入股票所花费的总金额。根据规则，我们需要找到每个月的第一个交易日。

![](img/a40b3f54d1300a6adbd48572c6da40b6_45.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_47.png)

以下是实现步骤：
1.  使用 `resample(‘M’).first()` 方法，按月份分组并获取每个月的第一行数据，即每月第一个交易日的数据。
2.  从这些数据中提取‘open’（开盘价）列。
3.  由于每月买入一手（100股），所以将每个开盘价乘以100，再求和，即得到总成本。

![](img/a40b3f54d1300a6adbd48572c6da40b6_49.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_51.png)

```python
# 获取每月第一个交易日的数据
df_monthly = new_df.resample(‘M’).first()

![](img/a40b3f54d1300a6adbd48572c6da40b6_53.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_55.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_57.png)

# 计算买入股票的总成本
cost_buy = df_monthly[‘open’].sum() * 100
print(f“买入股票总成本：{cost_buy}“)
```

![](img/a40b3f54d1300a6adbd48572c6da40b6_59.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_61.png)

## 计算卖出股票的总收入

![](img/a40b3f54d1300a6adbd48572c6da40b6_63.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_65.png)

现在，我们来计算卖出股票获得的收入。根据规则，我们需要找到每年最后一个交易日（2020年除外，因为它尚未结束）。

![](img/a40b3f54d1300a6adbd48572c6da40b6_67.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_69.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_71.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_73.png)

以下是实现步骤：
1.  使用 `resample(‘A’).last()` 方法，按年份分组并获取每年的最后一行数据。
2.  由于2020年未结束，其股票不应卖出，所以需要剔除2020年的数据行。
3.  从剩余数据中提取‘open’列。每年卖出的是当年累计买入的所有股票（即1200股），因此将每年开盘价乘以1200，再求和。

![](img/a40b3f54d1300a6adbd48572c6da40b6_75.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_77.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_79.png)

```python
# 获取每年最后一个交易日的数据
df_yearly = new_df.resample(‘A’).last()

![](img/a40b3f54d1300a6adbd48572c6da40b6_81.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_83.png)

# 剔除2020年的数据（因为该年未结束，股票未卖出）
df_yearly = df_yearly[:-1]

![](img/a40b3f54d1300a6adbd48572c6da40b6_85.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_87.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_89.png)

# 计算卖出股票的总收入（每年卖出1200股）
receive_sell = df_yearly[‘open’].sum() * 1200
print(f“卖出股票总收入：{receive_sell}“)
```

![](img/a40b3f54d1300a6adbd48572c6da40b6_91.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_93.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_95.png)

## 计算剩余股票价值及总收益

![](img/a40b3f54d1300a6adbd48572c6da40b6_97.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_99.png)

最后，我们需要计算手中剩余的股票价值（即2020年买入但未卖出的股票），并最终计算总收益。

![](img/a40b3f54d1300a6adbd48572c6da40b6_101.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_103.png)

以下是实现步骤：
1.  **计算剩余股票数量**：2020年已过去的月份里，每月买入一手，共买入 `n` 手（`n*100`股）。
2.  **确定剩余股票单价**：使用最近一个交易日（即数据最后一行）的收盘价进行估值。
3.  **计算总收益**：总收益 = 卖出总收入 + 剩余股票价值 - 买入总成本。

![](img/a40b3f54d1300a6adbd48572c6da40b6_105.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_107.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_109.png)

```python
# 计算剩余股票数量（例如，2020年有2个月的数据，则剩余200股）
remaining_shares = 200  # 根据实际2020年包含的月份数计算

![](img/a40b3f54d1300a6adbd48572c6da40b6_111.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_113.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_115.png)

# 获取最近交易日的收盘价作为估值单价
last_close_price = new_df[‘close’].iloc[-1]

# 计算剩余股票价值
remaining_value = remaining_shares * last_close_price

![](img/a40b3f54d1300a6adbd48572c6da40b6_117.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_119.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_121.png)

# 计算总收益
total_profit = receive_sell + remaining_value - cost_buy
print(f“剩余股票价值：{remaining_value}“)
print(f“策略总收益：{total_profit}“)
```

![](img/a40b3f54d1300a6adbd48572c6da40b6_123.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_125.png)

## 总结

![](img/a40b3f54d1300a6adbd48572c6da40b6_127.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_129.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_131.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_133.png)

本节课中我们一起学习了如何对一个具体的股票交易策略进行完整的收益分析。我们通过Pandas的强大功能，特别是时间序列的切片和`resample`重采样方法，高效地完成了以下任务：
*   按时间范围筛选数据。
*   精确提取每月第一个交易日和每年最后一个交易日的数据。
*   基于开盘价计算买入总成本和卖出总收入。
*   结合最新收盘价估算持仓价值，并最终计算出策略的总收益。

![](img/a40b3f54d1300a6adbd48572c6da40b6_135.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_137.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_139.png)

![](img/a40b3f54d1300a6adbd48572c6da40b6_141.png)

这个案例不仅巩固了数据索引和切片操作，也引入了`resample`这一处理时间序列数据的核心技能，是金融量化分析中的一个典型实践。