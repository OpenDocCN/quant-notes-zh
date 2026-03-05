# Python金融量化：P13：股票买卖收益分析 📈

![](img/b0082c8c19e1a67e8bf85b975ab00afc_1.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_3.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_5.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_7.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_9.png)

在本节课中，我们将学习如何分析一个特定的股票交易策略的收益情况。我们将模拟一个从2010年1月1日开始，每月第一个交易日买入一手股票，并在每年最后一个交易日卖出所有股票的投资策略，并计算其总收益。通过这个案例，我们将掌握Pandas的时间序列数据处理、数据重采样以及基于特定条件进行数据筛选和计算的核心技能。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_11.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_13.png)

## 需求分析

![](img/b0082c8c19e1a67e8bf85b975ab00afc_15.png)

上一节我们明确了课程目标，本节中我们来看看具体的需求。我们需要计算一个特定交易策略的收益。首先，我们对需求进行详细分析。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_17.png)

以下是需求分析的关键点：

1.  **时间范围**：策略从2010年1月1日开始，到当前日期（案例中以2020年2月18日为例）为止。我们需要将数据时间节点控制在2010年至2020年之间。
2.  **交易单位**：“一手”股票代表100股。因此，每次买入操作是100股。
3.  **交易规则**：
    *   **买入**：每月的第一个交易日，买入一手（100股）股票。
    *   **卖出**：每年的最后一个交易日，卖出当年持有的所有股票。
4.  **收益计算**：总收益 = （卖出股票所得总金额 + 年末手中剩余股票估值） - 买入股票所花费总金额。
5.  **价格基准**：为简化计算，我们统一使用**开盘价**作为买卖股票的单价。对于年末手中未卖出股票的估值，使用最近一个交易日（昨日）的**收盘价**。

## 数据准备与时间筛选

![](img/b0082c8c19e1a67e8bf85b975ab00afc_19.png)

首先，我们需要确保数据的时间索引格式正确，并筛选出2010年至2020年之间的数据。假设原始数据`df`的行索引已经是日期时间类型。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_21.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_23.png)

```python
# 筛选2010-01-01至2020-02-18期间的数据
new_df = df[‘2010-01-01‘:‘2020-02-18‘]
```

![](img/b0082c8c19e1a67e8bf85b975ab00afc_25.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_27.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_29.png)

执行此操作后，`new_df`包含了我们分析所需时间范围内的所有交易日数据。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_31.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_33.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_35.png)

## 计算买入股票的总花费

![](img/b0082c8c19e1a67e8bf85b975ab00afc_37.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_39.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_41.png)

接下来，我们计算投资者买入股票所花费的总金额。根据规则，我们需要找到每月第一个交易日的开盘价。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_43.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_45.png)

我们可以使用Pandas的`resample`方法，按月份对数据进行重采样，并取每个月的第一行数据。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_47.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_49.png)

```python
# 获取每月第一个交易日的数据行
df_monthly = new_df.resample(‘M‘).first()
```

![](img/b0082c8c19e1a67e8bf85b975ab00afc_51.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_53.png)

`df_monthly`中存储了每个月的第一个交易日对应的所有数据（开盘价、收盘价等）。我们只需要其中的开盘价。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_55.png)

买入总花费的计算公式为：
**总花费 = 每月买入一手股票的开盘价之和 × 100**

![](img/b0082c8c19e1a67e8bf85b975ab00afc_57.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_59.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_61.png)

```python
# 计算买入股票的总花费
cost = df_monthly[‘open‘].sum() * 100
print(f“买入股票总花费：{cost}“)
```

![](img/b0082c8c19e1a67e8bf85b975ab00afc_63.png)

## 计算卖出股票的总收入

![](img/b0082c8c19e1a67e8bf85b975ab00afc_65.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_67.png)

现在，我们计算卖出股票所得的总金额。根据规则，我们需要找到每年最后一个交易日的数据。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_69.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_71.png)

同样使用`resample`方法，按年份进行重采样，并取每年的最后一行数据。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_73.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_75.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_77.png)

```python
# 获取每年最后一个交易日的数据行
df_yearly = new_df.resample(‘A‘).last()
```

![](img/b0082c8c19e1a67e8bf85b975ab00afc_79.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_81.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_83.png)

这里有一个特殊情况：当前年份（2020年）尚未结束，因此2020年买入的股票不应被卖出。我们需要将2020年的数据从卖出数据中剔除。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_85.png)

```python
# 剔除2020年的数据（因为2020年未结束，股票未卖出）
df_yearly = df_yearly[:-1]
```

![](img/b0082c8c19e1a67e8bf85b975ab00afc_87.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_89.png)

`df_yearly`现在包含了2010年至2019年每年最后一个交易日的数据。每年需要卖出当年累计买入的所有股票。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_91.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_93.png)

一个完整年份会买入12手股票，即1200股。因此，卖出总收入的计算公式为：
**卖出总收入 = 每年最后一个交易日的开盘价之和 × 1200**

![](img/b0082c8c19e1a67e8bf85b975ab00afc_95.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_97.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_99.png)

```python
# 计算卖出股票的总收入
receive = df_yearly[‘open‘].sum() * 1200
print(f“卖出股票总收入：{receive}“)
```

![](img/b0082c8c19e1a67e8bf85b975ab00afc_101.png)

## 计算剩余股票价值及总收益

![](img/b0082c8c19e1a67e8bf85b975ab00afc_103.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_105.png)

投资者手中还持有2020年买入的股票（2020年1月和2月），这部分股票尚未卖出，但其价值应计入总资产。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_107.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_109.png)

首先，确定剩余股票数量：2020年已过去2个月，故买入2手，即200股。
其次，确定估值单价：使用最近一个交易日（`new_df`的最后一行）的收盘价。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_111.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_113.png)

```python
# 获取最近一个交易日的收盘价，作为剩余股票的单价
last_price = new_df[‘close‘][-1]

![](img/b0082c8c19e1a67e8bf85b975ab00afc_115.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_117.png)

# 计算剩余股票的价值
last_money = last_price * 200
print(f“手中剩余股票估值：{last_money}“)
```

最后，计算该投资策略的总收益：
**总收益 = (卖出总收入 + 剩余股票价值) - 买入总花费**

![](img/b0082c8c19e1a67e8bf85b975ab00afc_119.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_121.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_123.png)

```python
# 计算总收益
total_profit = (receive + last_money) - cost
print(f“此策略总收益为：{total_profit}“)
```

## 总结

![](img/b0082c8c19e1a67e8bf85b975ab00afc_125.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_127.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_129.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_131.png)

本节课中，我们一起学习并实现了一个股票交易策略的收益分析案例。我们首先对需求进行了拆解，明确了时间范围、交易规则和收益计算公式。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_133.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_135.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_137.png)

在实现过程中，我们运用了以下关键技能：
*   使用日期时间索引对数据进行切片，筛选特定时间范围。
*   利用`resample(‘M‘).first()`方法高效地获取每月第一个交易日的数据。
*   利用`resample(‘A‘).last()`方法获取每年最后一个交易日的数据，并处理了未完结年份的边界情况。
*   基于清晰的业务逻辑（买入单价×股数，卖出单价×股数），进行数据计算，最终得出策略总收益。

![](img/b0082c8c19e1a67e8bf85b975ab00afc_139.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_141.png)

![](img/b0082c8c19e1a67e8bf85b975ab00afc_143.png)

这个案例综合运用了Pandas在金融时间序列数据分析中的常见操作，是数据清洗、转换和计算的一个典型实践。