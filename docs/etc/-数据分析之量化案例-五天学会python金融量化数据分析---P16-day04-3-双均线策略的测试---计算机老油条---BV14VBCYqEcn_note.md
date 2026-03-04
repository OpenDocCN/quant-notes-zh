# 数据分析之量化案例：P16：双均线策略的测试 📈

![](img/ea39131e006c69336e5fe57d35cbacec_1.png)

![](img/ea39131e006c69336e5fe57d35cbacec_3.png)

![](img/ea39131e006c69336e5fe57d35cbacec_5.png)

![](img/ea39131e006c69336e5fe57d35cbacec_7.png)

![](img/ea39131e006c69336e5fe57d35cbacec_9.png)

在本节课中，我们将学习如何基于之前找出的金叉和死叉信号，模拟一个完整的股票交易策略，并计算其最终收益。我们将从2010年开始，使用10万元初始资金，遵循“金叉买入，死叉卖出”的原则进行交易，并考虑所有可能的情况，例如无法整除的股票数量和最终可能持有的剩余股票。

![](img/ea39131e006c69336e5fe57d35cbacec_11.png)

## 策略分析与设计

![](img/ea39131e006c69336e5fe57d35cbacec_13.png)

![](img/ea39131e006c69336e5fe57d35cbacec_15.png)

![](img/ea39131e006c69336e5fe57d35cbacec_17.png)

上一节我们识别出了股票历史数据中的金叉和死叉时间点。本节中，我们来看看如何将这些信号转化为实际的买卖操作，并计算策略的最终收益。

![](img/ea39131e006c69336e5fe57d35cbacec_19.png)

![](img/ea39131e006c69336e5fe57d35cbacec_21.png)

![](img/ea39131e006c69336e5fe57d35cbacec_23.png)

![](img/ea39131e006c69336e5fe57d35cbacec_25.png)

我们的策略基于以下核心规则：
*   **买入时机**：金叉出现时，使用当天的开盘价作为单价，用可用资金尽可能多地买入股票（以手为单位，1手=100股）。
*   **卖出时机**：死叉出现时，使用当天的开盘价作为单价，卖出持有的全部股票。
*   **初始条件**：初始资金 `first_money = 100000` 元。
*   **特殊情况**：如果最后一个交易日是金叉（即买入后没有后续的死叉卖出），则需要将手中剩余的股票按最后一天的收盘价折算成现金，计入总资产。

![](img/ea39131e006c69336e5fe57d35cbacec_27.png)

![](img/ea39131e006c69336e5fe57d35cbacec_29.png)

![](img/ea39131e006c69336e5fe57d35cbacec_31.png)

![](img/ea39131e006c69336e5fe57d35cbacec_33.png)

![](img/ea39131e006c69336e5fe57d35cbacec_35.png)

![](img/ea39131e006c69336e5fe57d35cbacec_37.png)

![](img/ea39131e006c69336e5fe57d35cbacec_39.png)

为了实现这个策略，我们需要跟踪几个关键变量：
*   `money`：动态资金，初始等于 `first_money`，买卖股票时从中扣除或增加。
*   `hold`：当前持有的股票数量（单位：股）。
*   `first_money`：固定本金，用于最终计算收益。

## 整合买卖信号

![](img/ea39131e006c69336e5fe57d35cbacec_41.png)

首先，我们需要将金叉和死叉的时间点整合成一个有序的序列，并过滤出2010年之后的数据。

![](img/ea39131e006c69336e5fe57d35cbacec_43.png)

![](img/ea39131e006c69336e5fe57d35cbacec_45.png)

以下是整合与筛选信号的步骤：

```python
# 创建Series，用1标记金叉时间，用0标记死叉时间
s1 = pd.Series(1, index=golden_date)  # golden_date 是金叉日期列表
s2 = pd.Series(0, index=death_date)   # death_date 是死叉日期列表

![](img/ea39131e006c69336e5fe57d35cbacec_47.png)

# 合并并按键（日期）排序
s = s1.append(s2).sort_index()

![](img/ea39131e006c69336e5fe57d35cbacec_49.png)

![](img/ea39131e006c69336e5fe57d35cbacec_51.png)

![](img/ea39131e006c69336e5fe57d35cbacec_53.png)

![](img/ea39131e006c69336e5fe57d35cbacec_55.png)

# 筛选出2010年之后的数据
s = s[‘2010’:‘2020’]
```
现在，Series `s` 的索引是日期，值是1（金叉）或0（死叉），并且按时间顺序排列。

![](img/ea39131e006c69336e5fe57d35cbacec_57.png)

![](img/ea39131e006c69336e5fe57d35cbacec_59.png)

![](img/ea39131e006c69336e5fe57d35cbacec_61.png)

![](img/ea39131e006c69336e5fe57d35cbacec_63.png)

![](img/ea39131e006c69336e5fe57d35cbacec_65.png)

![](img/ea39131e006c69336e5fe57d35cbacec_67.png)

![](img/ea39131e006c69336e5fe57d35cbacec_69.png)

## 模拟交易过程

![](img/ea39131e006c69336e5fe57d35cbacec_71.png)

![](img/ea39131e006c69336e5fe57d35cbacec_73.png)

![](img/ea39131e006c69336e5fe57d35cbacec_75.png)

![](img/ea39131e006c69336e5fe57d35cbacec_77.png)

![](img/ea39131e006c69336e5fe57d35cbacec_79.png)

![](img/ea39131e006c69336e5fe57d35cbacec_81.png)

接下来，我们遍历这个信号序列，模拟整个交易过程。

![](img/ea39131e006c69336e5fe57d35cbacec_83.png)

![](img/ea39131e006c69336e5fe57d35cbacec_85.png)

![](img/ea39131e006c69336e5fe57d35cbacec_87.png)

以下是模拟交易的核心循环逻辑：

![](img/ea39131e006c69336e5fe57d35cbacec_89.png)

```python
# 初始化变量
first_money = 100000
money = first_money  # 动态资金
hold = 0             # 持有股数

![](img/ea39131e006c69336e5fe57d35cbacec_91.png)

![](img/ea39131e006c69336e5fe57d35cbacec_93.png)

![](img/ea39131e006c69336e5fe57d35cbacec_95.png)

![](img/ea39131e006c69336e5fe57d35cbacec_97.png)

# 遍历所有信号
for i in range(len(s)):
    # 获取当前信号的日期和类型（1或0）
    current_date = s.index[i]
    signal = s.iloc[i]

    if signal == 1:  # 金叉，买入
        # 获取买入单价（当天开盘价）
        buy_price = df.loc[current_date, ‘open‘]
        # 计算最多能买多少手（1手=100股）
        hand_count = money // (buy_price * 100)
        # 计算买入的股数，并更新持有数量
        buy_count = hand_count * 100
        hold += buy_count
        # 从动态资金中扣除买入花费
        money -= buy_count * buy_price

    else:  # signal == 0，死叉，卖出
        # 获取卖出单价（当天开盘价）
        sell_price = df.loc[current_date, ‘open‘]
        # 卖出全部持有股票，收入加入动态资金
        money += hold * sell_price
        # 清空持有股数
        hold = 0
```

![](img/ea39131e006c69336e5fe57d35cbacec_99.png)

## 计算最终收益

交易循环结束后，我们需要计算策略的总收益。这里必须考虑循环结束后可能仍持有股票的情况（即最后一天是金叉）。

![](img/ea39131e006c69336e5fe57d35cbacec_101.png)

![](img/ea39131e006c69336e5fe57d35cbacec_103.png)

![](img/ea39131e006c69336e5fe57d35cbacec_104.png)

![](img/ea39131e006c69336e5fe57d35cbacec_106.png)

![](img/ea39131e006c69336e5fe57d35cbacec_108.png)

![](img/ea39131e006c69336e5fe57d35cbacec_110.png)

以下是计算最终收益的步骤：

![](img/ea39131e006c69336e5fe57d35cbacec_112.png)

![](img/ea39131e006c69336e5fe57d35cbacec_114.png)

```python
# 计算剩余股票的价值（以最后一天的收盘价计算）
last_price = df.iloc[-1][‘close‘]  # 获取最后一天的收盘价
last_money = hold * last_price

![](img/ea39131e006c69336e5fe57d35cbacec_116.png)

![](img/ea39131e006c69336e5fe57d35cbacec_118.png)

# 计算总收益
# 总收益 = 最终动态资金 + 剩余股票价值 - 初始本金
total_profit = (money + last_money) - first_money
print(f“策略总收益为：{total_profit:.2f} 元“)
```
如果最后一天是死叉，那么 `hold` 在循环内已被清为0，`last_money` 也为0，计算依然正确。

![](img/ea39131e006c69336e5fe57d35cbacec_120.png)

![](img/ea39131e006c69336e5fe57d35cbacec_122.png)

![](img/ea39131e006c69336e5fe57d35cbacec_124.png)

![](img/ea39131e006c69336e5fe57d35cbacec_126.png)

![](img/ea39131e006c69336e5fe57d35cbacec_128.png)

## 总结与拓展

![](img/ea39131e006c69336e5fe57d35cbacec_130.png)

![](img/ea39131e006c69336e5fe57d35cbacec_132.png)

![](img/ea39131e006c69336e5fe57d35cbacec_134.png)

![](img/ea39131e006c69336e5fe57d35cbacec_136.png)

![](img/ea39131e006c69336e5fe57d35cbacec_138.png)

本节课中我们一起学习了如何将一个双均线策略从信号识别落实到具体的交易模拟与收益计算。我们定义了清晰的买卖规则，使用Python代码跟踪资金和持仓状态，并妥善处理了交易中的整数约束（整手买卖）以及策略末期可能持股的特殊情况。

![](img/ea39131e006c69336e5fe57d35cbacec_140.png)

![](img/ea39131e006c69336e5fe57d35cbacec_142.png)

![](img/ea39131e006c69336e5fe57d35cbacec_144.png)

![](img/ea39131e006c69336e5fe57d35cbacec_146.png)

![](img/ea39131e006c69336e5fe57d35cbacec_148.png)

这个案例展示了金融量化分析的基本流程：**基于历史数据制定策略规则 -> 编写代码模拟交易 -> 评估策略表现**。虽然这是一个基于历史数据的“事后”分析，但它可以帮助我们理解策略的逻辑并验证其历史表现。对于有兴趣深入学习的同学，可以探索如“聚宽”等量化平台，它们提供了更丰富的金融数据和封装好的API，便于进行更复杂的策略研究和实盘模拟。