# Python金融分析+量化交易：P7：07 双均线策略的测试 📈

![](img/e6d3ed662a072a044b9fc7060105d8a3_1.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_3.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_5.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_7.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_9.png)

在本节课中，我们将学习如何基于双均线策略（金叉买入，死叉卖出）来模拟股票交易，并计算最终的收益情况。我们将从需求分析开始，逐步实现代码逻辑，最终评估该策略在历史数据上的表现。

![](img/e6d3ed662a072a044b9fc7060105d8a3_11.png)

## 需求分析

![](img/e6d3ed662a072a044b9fc7060105d8a3_13.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_15.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_17.png)

上一节我们识别出了股票数据中的金叉和死叉信号。本节中，我们将基于这些信号来模拟一个完整的交易过程。

![](img/e6d3ed662a072a044b9fc7060105d8a3_19.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_21.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_23.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_25.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_27.png)

具体的需求是：假设一个人从2010年开始，初始资金为10万元。当出现金叉信号时，他尽可能多地买入股票；当出现死叉信号时，他将持有的所有股票卖出。我们需要计算到数据截止日期为止，他的总收益是多少。

![](img/e6d3ed662a072a044b9fc7060105d8a3_29.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_31.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_33.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_35.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_37.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_39.png)

以下是实现这个需求需要分析的关键点：
*   **初始资金**：`first_money = 100000`。
*   **买卖时机**：金叉买入，死叉卖出。
*   **买卖单价**：使用信号出现当日的开盘价。
*   **交易单位**：股票交易以“手”为单位（1手=100股），因此买入数量必须是100股的整数倍。
*   **特殊情况**：如果数据最后一天是金叉（买入信号），则手中会持有未卖出的股票。这部分剩余股票的价值需要按最后一天的收盘价计算，并计入总收益。

## 代码实现

![](img/e6d3ed662a072a044b9fc7060105d8a3_41.png)

### 步骤一：整理交易信号序列

![](img/e6d3ed662a072a044b9fc7060105d8a3_43.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_45.png)

首先，我们需要将之前找出的金叉和死叉时间点，整理成一个按时间排序的序列，并用1和0分别标记金叉和死叉。

```python
# 创建金叉标识序列，值为1，索引为金叉时间
s1 = pd.Series(1, index=golden_date)
# 创建死叉标识序列，值为0，索引为死叉时间
s2 = pd.Series(0, index=death_date)
# 合并两个序列并按时间索引排序
s = pd.concat([s1, s2]).sort_index()
# 截取2010年之后的数据进行分析
s = s['2010':'2020']
```

![](img/e6d3ed662a072a044b9fc7060105d8a3_47.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_49.png)

### 步骤二：初始化交易变量

![](img/e6d3ed662a072a044b9fc7060105d8a3_51.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_53.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_55.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_57.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_59.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_61.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_63.png)

接下来，我们初始化交易过程中需要用到的几个核心变量。

![](img/e6d3ed662a072a044b9fc7060105d8a3_65.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_67.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_69.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_71.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_73.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_75.png)

```python
# 初始本金（不变）
first_money = 100000
# 可用资金（可变，用于买卖操作）
money = first_money
# 持有股票的数量（单位：股）
hold = 0
```

![](img/e6d3ed662a072a044b9fc7060105d8a3_77.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_79.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_81.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_83.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_85.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_87.png)

### 步骤三：遍历信号执行交易

现在，我们遍历信号序列`s`，根据值是1（金叉）还是0（死叉）来执行买入或卖出操作。

![](img/e6d3ed662a072a044b9fc7060105d8a3_89.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_91.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_93.png)

以下是遍历信号并执行交易的核心逻辑：
```python
# 遍历信号序列
for i in range(len(s)):
    # 获取当前信号对应的时间
    current_time = s.index[i]
    # 判断是否为金叉（买入信号）
    if s.iloc[i] == 1:
        # 1. 获取买入单价（金叉日的开盘价）
        buy_price = df.loc[current_time, 'open']
        # 2. 计算最多能买多少手（100股/手）
        # 公式：可用资金 // (单价 * 100)
        hand_count = money // (buy_price * 100)
        # 3. 计算买入股数，并更新持有数量
        buy_count = hand_count * 100
        hold += buy_count
        # 4. 从可用资金中扣除买入花费
        money -= buy_count * buy_price
    # 否则为死叉（卖出信号）
    else:
        # 1. 获取卖出单价（死叉日的开盘价）
        sell_price = df.loc[current_time, 'open']
        # 2. 卖出全部持有股票，获得资金
        money += hold * sell_price
        # 3. 清空持有股票数量
        hold = 0
```

![](img/e6d3ed662a072a044b9fc7060105d8a3_95.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_97.png)

### 步骤四：计算最终收益

循环结束后，我们需要计算总收益。这里需要处理可能持有的剩余股票。

![](img/e6d3ed662a072a044b9fc7060105d8a3_99.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_101.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_102.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_104.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_106.png)

```python
# 计算剩余股票的价值（按最后一天的收盘价计算）
last_price = df.iloc[-1]['close']
last_money = hold * last_price

![](img/e6d3ed662a072a044b9fc7060105d8a3_108.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_110.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_112.png)

# 计算总收益
# 公式：总收益 = (最终现金 + 剩余股票价值) - 初始本金
total_profit = (money + last_money) - first_money
print(f"最终总收益为：{total_profit}")
```

![](img/e6d3ed662a072a044b9fc7060105d8a3_114.png)

## 总结与拓展

![](img/e6d3ed662a072a044b9fc7060105d8a3_116.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_118.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_120.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_122.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_124.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_126.png)

本节课中我们一起学习了如何将双均线策略转化为可执行的交易代码。我们通过遍历历史金叉和死叉信号，模拟了“低买高卖”的过程，并最终计算出了策略在历史回测中的收益。

![](img/e6d3ed662a072a044b9fc7060105d8a3_128.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_130.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_132.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_134.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_136.png)

这个案例展示了量化交易的基本思路：**基于历史数据制定策略规则，并通过编程进行回测验证**。虽然这是基于过去数据的“事后分析”，但它可以帮助我们理解策略的潜在规律。在实际应用中，我们可以尝试优化参数（如均线周期），或将策略应用于未来的交易中。

![](img/e6d3ed662a072a044b9fc7060105d8a3_138.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_140.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_142.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_144.png)

![](img/e6d3ed662a072a044b9fc7060105d8a3_146.png)

如果你对量化交易产生了浓厚兴趣，可以探索一些专业的量化平台，例如**聚宽（JoinQuant）**。这些平台提供了丰富的金融数据、封装好的API以及回测系统，能够让你更便捷地开发和验证自己的交易策略。