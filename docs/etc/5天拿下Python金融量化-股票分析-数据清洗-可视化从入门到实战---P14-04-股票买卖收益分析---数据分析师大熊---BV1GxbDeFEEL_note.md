# Python金融量化：P14：04 股票买卖收益分析 📈

![](img/7d53590e1e6003b6addacbc0e4bfb417_1.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_3.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_5.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_7.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_9.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_11.png)

在本节课中，我们将学习如何分析一个特定的股票买卖策略的收益情况。我们将模拟一个从2010年1月1日开始，每月第一个交易日买入一手股票，并在每年最后一个交易日卖出所有股票的投资策略，并计算其总收益。通过这个案例，我们将掌握数据切片、时间序列重采样等核心技能。

![](img/7d53590e1e6003b6addacbc0e4bfb417_13.png)

## 需求分析

上一节我们明确了课程目标，本节中我们来看看具体的需求。我们需要计算一个特定投资策略的收益。策略规则如下：
*   从2010年1月1日开始。
*   每月第一个交易日买入一手股票。
*   每年最后一个交易日卖出所有股票。

为了计算收益，我们需要进行以下分析：
1.  **时间范围**：数据需要限定在2010年至2020年之间。
2.  **交易单位**：“一手”股票代表100股。
3.  **交易价格**：我们约定使用`开盘价`作为买卖股票的单价。
4.  **收益计算**：总收益 = 卖出股票总收入 + 剩余股票估值 - 买入股票总成本。

![](img/7d53590e1e6003b6addacbc0e4bfb417_15.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_17.png)

## 数据准备与时间切片

![](img/7d53590e1e6003b6addacbc0e4bfb417_19.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_21.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_23.png)

首先，我们需要将数据的时间范围控制在2010年至2020年。假设我们的原始数据`df`的行索引已经是日期时间类型。

![](img/7d53590e1e6003b6addacbc0e4bfb417_25.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_27.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_29.png)

以下是实现代码：
```python
new_df = df[‘2010-01-01’:‘2020-02’]
```
这段代码通过切片操作，将数据限定在2010年1月1日到2020年2月之间。如果行索引不是时间序列，则无法直接这样切片。

![](img/7d53590e1e6003b6addacbc0e4bfb417_31.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_33.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_35.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_37.png)

## 计算买入股票总成本

![](img/7d53590e1e6003b6addacbc0e4bfb417_39.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_41.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_43.png)

接下来，我们计算买入股票的总花费。根据策略，我们需要找到每月第一个交易日的数据。

![](img/7d53590e1e6003b6addacbc0e4bfb417_45.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_47.png)

以下是实现步骤：
1.  使用`resample`方法按月份重采样数据，并获取每个月的第一行数据（即第一个交易日）。
2.  从这些数据中提取`open`（开盘价）列。
3.  每个开盘价乘以100（一手股票），然后求和，得到总成本。

![](img/7d53590e1e6003b6addacbc0e4bfb417_49.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_51.png)

以下是实现代码：
```python
# 获取每月第一个交易日的数据
df_monthly = new_df.resample(‘M’).first()

![](img/7d53590e1e6003b6addacbc0e4bfb417_53.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_55.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_57.png)

# 计算买入总成本：每月开盘价 * 100股，然后求和
cost = df_monthly[‘open’].sum() * 100
print(f“买入股票总成本为：{cost}”)
```

![](img/7d53590e1e6003b6addacbc0e4bfb417_59.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_61.png)

## 计算卖出股票总收入

![](img/7d53590e1e6003b6addacbc0e4bfb417_63.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_65.png)

然后，我们计算卖出股票的总收入。根据策略，我们需要找到每年最后一个交易日的数据，但需注意2020年尚未结束，其股票不应卖出。

![](img/7d53590e1e6003b6addacbc0e4bfb417_67.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_69.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_71.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_73.png)

以下是实现步骤：
1.  使用`resample`方法按年份重采样数据，并获取每年的最后一行数据。
2.  剔除2020年的数据（因为该年股票未卖出）。
3.  从剩余数据中提取`open`列，每个开盘价乘以1200（因为一年买入12手，共1200股），然后求和，得到已卖出股票的总收入。

![](img/7d53590e1e6003b6addacbc0e4bfb417_75.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_77.png)

以下是实现代码：
```python
# 获取每年最后一个交易日的数据
df_yearly = new_df.resample(‘A’).last()

![](img/7d53590e1e6003b6addacbc0e4bfb417_79.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_81.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_83.png)

# 剔除2020年的数据（切片去除最后一行）
df_yearly = df_yearly[:-1]

![](img/7d53590e1e6003b6addacbc0e4bfb417_85.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_87.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_89.png)

# 计算卖出股票总收入：每年开盘价 * 1200股，然后求和
receive = df_yearly[‘open’].sum() * 1200
print(f“卖出股票总收入为：{receive}”)
```

![](img/7d53590e1e6003b6addacbc0e4bfb417_91.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_93.png)

## 计算剩余股票价值及总收益

![](img/7d53590e1e6003b6addacbc0e4bfb417_95.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_97.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_99.png)

最后，我们需要计算手中剩余股票的价值（2020年买入的股票），并最终计算总收益。

![](img/7d53590e1e6003b6addacbc0e4bfb417_101.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_103.png)

以下是实现步骤：
1.  计算剩余股票数量：2020年已买入的月份数 × 100股。
2.  确定剩余股票单价：使用数据中最后一天的`收盘价`进行估值。
3.  计算总收益。

![](img/7d53590e1e6003b6addacbc0e4bfb417_105.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_107.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_109.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_111.png)

以下是实现代码：
```python
# 计算剩余股票价值（2020年买入的）
last_price = new_df[‘close’].iloc[-1] # 获取最后一天的收盘价
last_stock_count = 2 * 100 # 假设2020年已过去2个月，买入了2手，共200股
last_money = last_price * last_stock_count

# 计算总收益
total_profit = receive + last_money - cost
print(f“剩余股票价值为：{last_money}”)
print(f“此策略总收益为：{total_profit}”)
```

![](img/7d53590e1e6003b6addacbc0e4bfb417_113.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_115.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_117.png)

## 总结

![](img/7d53590e1e6003b6addacbc0e4bfb417_119.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_121.png)

本节课中我们一起学习了一个完整的股票收益分析案例。我们模拟了“每月定投、年末卖出”的策略，并计算了其收益。

![](img/7d53590e1e6003b6addacbc0e4bfb417_123.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_125.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_127.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_129.png)

核心知识点总结如下：
*   **时间序列切片**：`df[‘start_date’:‘end_date’]`，用于筛选特定时间范围的数据。
*   **数据重采样**：`df.resample(‘M’).first()` 和 `df.resample(‘A’).last()`，用于按周期（月、年）获取特定数据点。
*   **收益计算逻辑**：总收益 = 卖出收入 + 持仓估值 - 买入成本。

![](img/7d53590e1e6003b6addacbc0e4bfb417_131.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_133.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_135.png)

![](img/7d53590e1e6003b6addacbc0e4bfb417_137.png)

通过本案例，我们不仅复习了Pandas的数据索引和切片操作，还掌握了`resample`这一强大的时间序列分析工具，这对于金融数据分析至关重要。