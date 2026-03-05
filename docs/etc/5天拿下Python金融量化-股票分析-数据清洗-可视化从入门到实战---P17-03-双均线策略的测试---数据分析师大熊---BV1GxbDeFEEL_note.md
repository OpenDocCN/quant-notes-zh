# Python金融量化：P17：03 双均线策略的测试 📈

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_1.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_3.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_5.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_7.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_9.png)

在本节课中，我们将学习如何基于之前识别出的金叉和死叉信号，构建并测试一个完整的双均线交易策略。我们将模拟一个投资者从2010年开始，使用10万元初始资金，按照金叉买入、死叉卖出的规则进行交易，并最终计算其总收益。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_11.png)

## 策略需求分析

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_13.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_15.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_17.png)

上一节我们识别出了股票历史数据中的金叉和死叉时间点。本节中，我们将利用这些信号来模拟交易。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_19.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_21.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_23.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_25.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_27.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_29.png)

具体的需求是：假设一位投资者从2010年1月1日开始，初始资金为10万元。每当出现金叉信号时，他使用可用资金尽可能多地买入股票；每当出现死叉信号时，他将手中持有的所有股票卖出。我们需要计算到数据截止日（即“今天”）为止，他的总收益是多少。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_31.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_33.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_35.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_37.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_39.png)

这里有几个关键点需要注意：
*   **“尽可能多”买入**：因为股票交易以“手”为单位（1手=100股），所以资金可能无法恰好全部用完。
*   **交易价格**：我们使用信号出现当日的开盘价作为买卖股票的单价。
*   **最终持仓处理**：如果数据最后一天是金叉（即买入后没有后续的死叉卖出），那么手中会剩余股票。这部分剩余股票的价值需要计入总收益，我们使用最后一天的收盘价来估算其价值。

## 数据准备：整合交易信号

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_41.png)

首先，我们需要将金叉和死叉的时间点整合到一个序列中，并筛选出2010年之后的数据。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_43.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_45.png)

以下是整合信号的步骤：
1.  创建一个Series，其索引为金叉时间，值全部设为1，作为金叉标识。
2.  创建另一个Series，其索引为死叉时间，值全部设为0，作为死叉标识。
3.  将两个Series合并，并按时间索引排序，得到一个交错显示金叉（1）和死叉（0）的信号序列。
4.  截取2010年之后的数据作为我们的交易信号源。

```python
# 创建金叉标识序列，值为1
s1 = pd.Series(1, index=golden_dates)
# 创建死叉标识序列，值为0
s2 = pd.Series(0, index=death_dates)
# 合并并排序
s = pd.concat([s1, s2]).sort_index()
# 筛选2010年之后的数据
s = s[‘2010’:]
```

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_47.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_49.png)

## 策略模拟实现

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_51.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_53.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_55.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_57.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_59.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_61.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_63.png)

接下来，我们根据信号序列模拟整个交易过程。我们将定义几个关键变量来跟踪资金和持仓状态。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_65.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_67.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_69.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_71.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_73.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_75.png)

以下是模拟交易的核心变量与逻辑：
*   `first_money`：初始本金（100,000），固定不变，用于最终计算收益。
*   `money`：可用资金，初始等于本金，买卖股票时从中增减。
*   `hold`：持有股票的数量（单位：股）。
*   **循环信号序列**：遍历`s`中的每个元素（0或1）。
    *   如果值为1（金叉），则计算以当日开盘价最多能买多少手股票，更新`hold`和`money`。
    *   如果值为0（死叉），则以当日开盘价卖出全部`hold`数量的股票，更新`money`，并将`hold`重置为0。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_77.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_79.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_81.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_83.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_85.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_87.png)

```python
# 初始化变量
first_money = 100000
money = first_money  # 可用资金
hold = 0             # 持有股数

# 遍历交易信号
for i in range(len(s)):
    # 获取当前信号的日期
    current_time = s.index[i]
    # 获取当前信号的值（1或0）
    signal = s.iloc[i]

    if signal == 1:  # 金叉，买入
        # 获取买入单价（当日开盘价）
        buy_price = df.loc[current_time, ‘open’]
        # 计算最多能买多少手
        hand_count = money // (buy_price * 100)
        # 计算买入股数，并更新持仓
        buy_count = hand_count * 100
        hold += buy_count
        # 计算买入花费，并从可用资金中扣除
        cost = buy_price * buy_count
        money -= cost

    else:  # 死叉，卖出
        # 获取卖出单价（当日开盘价）
        sell_price = df.loc[current_time, ‘open’]
        # 计算卖出总收入，并更新可用资金
        revenue = sell_price * hold
        money += revenue
        # 清空持仓
        hold = 0
```

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_89.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_91.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_93.png)

## 计算最终收益

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_95.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_97.png)

交易循环结束后，我们需要计算总收益。这里需要特别处理可能存在的剩余股票（即最后一天是金叉的情况）。

计算总收益的步骤如下：
1.  计算剩余股票的价值：`hold`（股） × 最后一天的收盘价。
2.  总收益 = (`money` + 剩余股票价值) - `first_money`。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_99.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_101.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_102.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_104.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_106.png)

```python
# 计算剩余股票价值（使用最后一天的收盘价）
last_price = df.iloc[-1][‘close’]  # 获取最后一天的收盘价
last_money = hold * last_price

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_108.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_110.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_112.png)

# 计算总收益
total_profit = (money + last_money) - first_money
print(f“最终总收益为：{total_profit}元”)
```

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_114.png)

## 总结与拓展

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_116.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_118.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_120.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_122.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_124.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_126.png)

本节课中我们一起学习了如何将一个理论上的双均线策略转化为可执行的代码，并进行历史回测。我们定义了清晰的交易规则，处理了买卖信号、资金变动、持仓管理以及最终收益计算等完整流程。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_128.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_130.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_132.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_134.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_136.png)

通过这个案例，你可以感受到金融量化分析的核心：基于历史数据制定策略规则，并通过编程进行验证和回测。虽然这是基于过去数据的“事后”分析，但成熟的策略往往依赖于从历史规律中寻找未来可能重复的模式。

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_138.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_140.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_142.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_144.png)

![](img/a83e0a4c39baf0d5d0f733b1fcc52989_146.png)

如果你对这个领域有更浓厚的兴趣，可以探索一些专业的量化平台，例如**聚宽（JoinQuant）**，它提供了丰富的金融数据API和回测框架，能让你更方便地编写和测试更复杂的交易策略。