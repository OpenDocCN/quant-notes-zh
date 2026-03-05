# Python金融量化：P16：双均线策略的测试 🧪

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_1.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_3.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_5.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_7.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_9.png)

在本节课中，我们将学习如何基于之前识别出的金叉和死叉信号，模拟一个完整的股票交易策略，并计算其最终收益。我们将从2010年开始，使用10万元初始资金，在金叉时尽可能多地买入股票，在死叉时全部卖出，并计算到今天的总收益。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_11.png)

## 策略分析与设计思路

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_13.png)

上一节我们识别出了股票历史数据中的金叉和死叉时间点。本节中，我们将利用这些信号来模拟交易。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_15.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_17.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_19.png)

首先，我们需要明确策略的几个核心规则：
1.  **买卖价格**：使用交易当日的开盘价作为买卖单价。
2.  **买卖时机**：在金叉时间点买入股票，在死叉时间点卖出股票。
3.  **交易单位**：股票交易以“手”为单位（1手=100股），因此买入时需按手数计算。
4.  **特殊情况**：如果数据最后一天是金叉（即买入后没有后续的死叉卖出），则手中会持有剩余股票。这部分股票的价值将按最后一天的收盘价计入总资产。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_21.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_23.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_25.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_27.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_29.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_31.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_33.png)

基于以上分析，我们的程序需要跟踪几个关键变量：
*   `first_money`：初始本金（10万元），用于最终计算收益。
*   `money`：动态资金，随买卖股票增减。
*   `hold`：当前持有的股票数量（单位：股）。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_35.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_37.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_39.png)

## 代码实现步骤

以下是实现双均线策略测试的具体步骤。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_41.png)

### 步骤一：整合与筛选交易信号

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_43.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_45.png)

首先，我们需要将金叉和死叉时间点整合到一个序列中，并筛选出2010年之后的信号。

```python
# 创建Series，用1标记金叉时间，用0标记死叉时间
s1 = pd.Series(1, index=golden_date)  # golden_date 为金叉日期列表
s2 = pd.Series(0, index=death_date)   # death_date 为死叉日期列表

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_47.png)

# 合并并排序
s = s1.append(s2).sort_index()

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_49.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_51.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_53.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_55.png)

# 筛选2010年之后的数据
s = s['2010':'2020']
```
此时，序列 `s` 的索引是日期，值为1或0，分别代表该日期是金叉或死叉。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_57.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_59.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_61.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_63.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_65.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_67.png)

### 步骤二：初始化交易变量

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_69.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_71.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_73.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_75.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_77.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_79.png)

接下来，我们初始化策略所需的变量。
```python
first_money = 100000  # 初始本金
money = first_money   # 可用资金，随交易变动
hold = 0              # 持有股票数量（股）
```

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_81.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_83.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_85.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_87.png)

### 步骤三：遍历信号执行交易

核心逻辑是遍历信号序列 `s`，根据值是1还是0来决定买入或卖出。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_89.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_91.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_93.png)

```python
for i in range(len(s)):
    # 获取当前信号的日期和类型（1或0）
    current_date = s.index[i]
    signal = s.iloc[i]

    if signal == 1:  # 金叉，买入
        # 1. 获取买入单价（当日开盘价）
        buy_price = df.loc[current_date, 'open']

        # 2. 计算最多能买多少手（用可用资金除以一手的价格）
        hand_count = money // (buy_price * 100)

        # 3. 如果至少能买1手，则执行买入
        if hand_count > 0:
            # 更新持有股数
            hold += hand_count * 100
            # 从可用资金中扣除买入花费
            money -= hold * buy_price

    else:  # 死叉，卖出
        # 1. 获取卖出单价（当日开盘价）
        sell_price = df.loc[current_date, 'open']

        # 2. 卖出全部持有股票
        # 将卖出所得加入可用资金
        money += hold * sell_price
        # 持有股数清零
        hold = 0
```

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_95.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_97.png)

### 步骤四：计算最终收益并处理剩余股票

循环结束后，需要计算总收益。如果最后一天是金叉，`hold` 可能不为0，表示有股票未卖出。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_99.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_101.png)

```python
# 计算剩余股票的价值（以最后一天的收盘价计算）
last_price = df.iloc[-1]['close']
last_money = hold * last_price

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_102.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_104.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_106.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_108.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_110.png)

# 计算总收益
# 总资产 = 剩余现金 + 剩余股票价值
# 总收益 = 总资产 - 初始本金
total_money = money + last_money
total_profit = total_money - first_money

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_112.png)

print(f"最终总资产：{total_money}")
print(f"总收益：{total_profit}")
```

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_114.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_116.png)

## 总结与延伸

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_118.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_120.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_122.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_124.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_126.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_128.png)

本节课中我们一起学习了如何将双均线策略的信号转化为可执行的交易逻辑，并通过模拟回测计算了策略的历史收益。我们实现了资金管理、买卖点触发以及特殊情况（剩余股票）的处理。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_130.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_132.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_134.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_136.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_138.png)

这个策略是基于历史数据的静态回测，它可以帮助我们验证一个交易想法在过去的有效性。在实际的金融量化中，我们可以将此类策略部署到如**聚宽（JoinQuant）**、米筐（RiceQuant）等专业的量化平台上，利用平台提供的实时数据、更复杂的API和回测引擎，进行更深入、更动态的策略研究和实盘模拟。

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_140.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_142.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_144.png)

![](img/c3b13bdb5ef45604b8d728e2ba74cccb_146.png)

通过本案例，你应该对如何使用Python进行简单的金融策略回测有了一个直观的认识。记住，任何策略都需要经过严谨的风险评估和多样化测试，历史表现不代表未来收益。