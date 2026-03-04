# 数据分析之量化案例：P13：股票买卖收益分析 📈

![](img/648e6dabd6179c2a5289cdc917d8b3b1_1.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_3.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_5.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_7.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_9.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_11.png)

在本节课中，我们将学习如何分析一个特定的股票交易策略。我们将模拟一个从2010年1月1日开始的投资行为：每月第一个交易日买入一手股票，并在每年最后一个交易日卖出所有股票。我们的目标是计算截至2020年初，遵循此策略的总收益。通过这个案例，我们将巩固Pandas数据框的索引、切片操作，并学习一个新的数据处理技能：`resample`。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_13.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_15.png)

## 需求分析

![](img/648e6dabd6179c2a5289cdc917d8b3b1_17.png)

上一节我们明确了任务目标，本节中我们来看看如何将需求拆解为可执行的步骤。

首先，我们需要明确几个核心概念和条件：

1.  **时间范围**：分析区间为2010年1月1日至2020年2月（案例制作时）。
2.  **交易单位**：一手股票等于**100**股。
3.  **买入规则**：每月第一个交易日，以当日**开盘价**买入一手（100股）股票。
4.  **卖出规则**：每年最后一个交易日，以当日**开盘价**卖出当年持有的所有股票。
5.  **收益计算**：总收益 = 卖出股票总收入 + 手中剩余股票估值 - 买入股票总支出。
6.  **特殊情况**：2020年未结束，因此当年买入的股票尚未卖出，需按最后一天的**收盘价**估算其价值。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_19.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_21.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_23.png)

## 数据准备与时间范围筛选

![](img/648e6dabd6179c2a5289cdc917d8b3b1_25.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_27.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_29.png)

我们的原始数据行索引已被转换为时间序列。首先，需要将数据筛选到指定的时间范围内。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_31.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_33.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_35.png)

```python
# 假设原始数据框名为 df，且索引为DatetimeIndex
new_df = df[‘2010-01-01‘:‘2020-02‘]
```
这段代码将创建一个新的数据框`new_df`，它只包含2010年1月至2020年2月的数据。这是后续所有计算的基础。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_37.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_39.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_41.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_43.png)

## 计算买入股票总支出

![](img/648e6dabd6179c2a5289cdc917d8b3b1_45.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_47.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_49.png)

接下来，我们计算投资者买入股票所花费的总金额。这需要找到每月第一个交易日的数据。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_51.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_53.png)

以下是实现步骤：
1.  使用`resample(‘M‘)`方法按月份对数据进行分组。
2.  使用`.first()`方法获取每个月的第一行数据，即每月第一个交易日。
3.  从这些行中提取`open`（开盘价）列。
4.  每个开盘价代表买入100股的成本，因此对开盘价求和后乘以100，即为总买入成本。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_55.png)

```python
# 获取每月第一个交易日的行数据
df_monthly = new_df.resample(‘M‘).first()

![](img/648e6dabd6179c2a5289cdc917d8b3b1_57.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_59.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_61.png)

# 计算买入股票的总花费 (cost)
cost = df_monthly[‘open‘].sum() * 100
print(f“买入股票总花费：{cost}“)
```

![](img/648e6dabd6179c2a5289cdc917d8b3b1_63.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_65.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_67.png)

## 计算卖出股票总收入

![](img/648e6dabd6179c2a5289cdc917d8b3b1_69.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_71.png)

现在，我们来计算卖出股票获得的收入。这需要找到每年最后一个交易日的数据，并注意排除未结束的2020年。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_73.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_75.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_77.png)

以下是实现步骤：
1.  使用`resample(‘A‘)`方法按年份对数据进行分组。
2.  使用`.last()`方法获取每年的最后一行数据。
3.  由于2020年未结束，其“最后一行”实际上是当前数据的最新日期（如2020-02-18），我们需要将其剔除。
4.  从筛选后的行数据中提取`open`列。
5.  每年卖出的是当年累计买入的所有股票。一个完整年份买入12手，即1200股。因此，对开盘价求和后乘以1200，即为卖出总收入（不含2020年）。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_79.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_81.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_83.png)

```python
# 获取每年最后一个交易日的行数据
df_yearly = new_df.resample(‘A‘).last()

![](img/648e6dabd6179c2a5289cdc917d8b3b1_85.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_87.png)

# 剔除2020年的数据（因为2020年未结束，股票未卖出）
df_yearly = df_yearly[:-1]

![](img/648e6dabd6179c2a5289cdc917d8b3b1_89.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_91.png)

# 计算卖出股票的总收入 (receive)
receive = df_yearly[‘open‘].sum() * 1200
print(f“卖出股票总收入：{receive}“)
```

![](img/648e6dabd6179c2a5289cdc917d8b3b1_93.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_95.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_97.png)

## 计算剩余股票价值及总收益

![](img/648e6dabd6179c2a5289cdc917d8b3b1_99.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_101.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_103.png)

最后，我们需要计算2020年已买入但未卖出的股票价值，并最终计算总收益。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_105.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_107.png)

以下是实现步骤：
1.  获取数据集中最后一天的收盘价，作为剩余股票的单价。
2.  计算2020年买入的股票数量（案例中为2个月，共2手，即200股）。
3.  剩余股票价值 = 最后一天收盘价 * 200。
4.  总收益 = 卖出总收入 + 剩余股票价值 - 买入总成本。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_109.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_111.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_113.png)

```python
# 获取最后一天的收盘价，用于估算剩余股票价值
last_price = new_df[‘close‘][-1]

![](img/648e6dabd6179c2a5289cdc917d8b3b1_115.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_117.png)

# 计算剩余股票价值 (2020年买入的，案例中为2手，共200股)
last_money = last_price * 200
print(f“剩余股票估值：{last_money}“)

# 计算总收益
total_profit = receive + last_money - cost
print(f“总收益：{total_profit}“)
```

![](img/648e6dabd6179c2a5289cdc917d8b3b1_119.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_121.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_123.png)

## 总结

![](img/648e6dabd6179c2a5289cdc917d8b3b1_125.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_127.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_129.png)

本节课中，我们一起学习并实现了一个完整的股票交易策略收益分析案例。我们首先对复杂需求进行了逐步拆解，明确了时间范围、交易规则等核心要素。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_131.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_133.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_135.png)

在实现过程中，我们关键运用了以下技能：
*   **数据筛选**：利用时间序列索引进行切片，快速提取目标时间范围的数据。
*   **数据重采样**：使用`df.resample().first()`和`df.resample().last()`高效地按周期（月、年）提取特定数据点，这是处理时间序列数据的强大工具。
*   **数据计算**：结合`sum()`等聚合函数与算术运算，完成成本、收入、收益的核算。

![](img/648e6dabd6179c2a5289cdc917d8b3b1_137.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_139.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_141.png)

![](img/648e6dabd6179c2a5289cdc917d8b3b1_143.png)

通过这个案例，你将掌握如何将实际的金融分析问题转化为可执行的代码逻辑，并熟练运用Pandas进行时间序列数据处理。