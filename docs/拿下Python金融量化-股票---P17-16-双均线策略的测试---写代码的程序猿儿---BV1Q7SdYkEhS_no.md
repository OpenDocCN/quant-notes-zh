# Python金融量化：P17：双均线策略的测试与实现 📈

![](img/cf4b64cab4715c2072c2a0c00893b0b5_1.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_3.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_5.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_7.png)

在本节课中，我们将学习如何将之前识别出的金叉与死叉信号，转化为一个可执行的股票交易策略，并计算其最终收益。我们将模拟一个投资者从2010年开始，使用10万元初始资金，遵循“金叉买入，死叉卖出”的规则进行交易，并评估其最终收益情况。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_9.png)

## 策略需求分析

![](img/cf4b64cab4715c2072c2a0c00893b0b5_11.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_13.png)

上一节我们识别出了股票数据中的金叉和死叉时间点。本节中，我们将基于这些信号来构建一个完整的交易策略。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_15.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_17.png)

具体的需求是：假设一位投资者从2010年开始，初始资金为10万元。当出现金叉信号时，他使用可用资金尽可能多地买入股票；当出现死叉信号时，他将手中持有的股票全部卖出。我们需要计算到数据截止日（即“今天”）为止，他的总收益是多少。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_19.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_21.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_23.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_25.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_27.png)

以下是几个关键点的分析：
*   **初始资金**：`first_money = 100000`。
*   **“金叉尽量买入”**：因为股票交易以“手”为单位（1手=100股），无法按股购买。10万元可能无法恰好买完所有资金，例如股价为3元，一手需300元，10万元无法被300整除，因此是“尽量”买入。
*   **“死叉全部卖出”**：在死叉信号出现时，卖出当前持有的所有股票。
*   **交易价格**：我们使用信号出现当日的**开盘价**作为买卖股票的单价。
*   **特殊情况**：如果数据最后一天是金叉（即买入信号），则手中会持有未卖出的股票。这部分剩余股票的价值需要计入总收益，其单价使用数据最后一天的**收盘价**来衡量。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_29.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_31.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_33.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_35.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_37.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_39.png)

## 代码实现步骤

### 1. 整合与筛选交易信号

![](img/cf4b64cab4715c2072c2a0c00893b0b5_41.png)

首先，我们需要将金叉和死叉的时间点整合到一个序列中，并筛选出2010年之后的信号。

```python
# 假设 golden_date 是金叉时间列表， death_date 是死叉时间列表
# 创建Series，用1标记金叉，用0标记死叉
s1 = pd.Series(1, index=golden_date)  # 金叉标识
s2 = pd.Series(0, index=death_date)   # 死叉标识

![](img/cf4b64cab4715c2072c2a0c00893b0b5_43.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_45.png)

# 合并两个Series
s = s1.append(s2)
# 按时间索引排序
s = s.sort_index()

# 筛选出2010年之后的数据
s = s['2010':]
```

![](img/cf4b64cab4715c2072c2a0c00893b0b5_47.png)

现在，`s` 这个Series的索引是交易时间，其值（1或0）明确指示了该时间是买入（金叉）还是卖出（死叉）信号。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_49.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_51.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_53.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_55.png)

### 2. 初始化交易变量

![](img/cf4b64cab4715c2072c2a0c00893b0b5_57.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_59.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_61.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_63.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_65.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_67.png)

接下来，我们初始化策略执行所需的几个关键变量。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_69.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_71.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_73.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_75.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_77.png)

```python
first_money = 100000  # 初始本金，固定不变
money = first_money   # 可用资金，买卖股票时变动
hold = 0              # 当前持有股票的数量（单位：股）
```

![](img/cf4b64cab4715c2072c2a0c00893b0b5_79.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_81.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_83.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_85.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_87.png)

*   `first_money`：用于最终计算收益的本金基准。
*   `money`：动态变化的资金，买入时减少，卖出时增加。
*   `hold`：记录当前持有多少股股票。

### 3. 遍历信号执行交易

![](img/cf4b64cab4715c2072c2a0c00893b0b5_89.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_91.png)

核心部分是遍历筛选后的信号序列 `s`，根据每个信号是1（买）还是0（卖）来执行相应的操作。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_93.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_95.png)

```python
for i in range(len(s)):
    # 获取当前信号的日期
    current_time = s.index[i]
    # 获取当前信号的值（1或0）
    signal = s.iloc[i]

    if signal == 1:  # 金叉，买入信号
        # 获取买入单价（当日开盘价）
        buy_price = df.loc[current_time, 'open']

        # 计算最多能买多少手（1手=100股）
        # 一手股票所需金额 = buy_price * 100
        hand_count = money // (buy_price * 100)

        if hand_count > 0:  # 如果资金足够至少买一手
            # 计算买入的股数
            buy_count = hand_count * 100
            # 持有股票增加
            hold += buy_count
            # 资金减少（花费金额 = 买入股数 * 单价）
            money -= buy_count * buy_price

    else:  # 死叉，卖出信号
        if hold > 0:  # 如果持有股票，则卖出
            # 获取卖出单价（当日开盘价）
            sell_price = df.loc[current_time, 'open']
            # 卖出获得金额 = 持有股数 * 卖出单价
            money += hold * sell_price
            # 卖出后，持有股票数量清零
            hold = 0
```

**代码逻辑说明**：
*   **买入**：计算当前资金能购买的最大整手数，然后更新 `hold`（股数）和 `money`（资金）。
*   **卖出**：以当日开盘价卖出全部持有的股票（`hold` 股），将收入加入 `money`，并将 `hold` 重置为0。

### 4. 计算最终收益

![](img/cf4b64cab4715c2072c2a0c00893b0b5_97.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_99.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_100.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_102.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_104.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_106.png)

循环结束后，我们需要计算总收益。这里需考虑循环结束后可能仍持有股票（即最后一天是金叉）的情况。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_108.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_110.png)

```python
# 计算剩余股票的价值（以最后一天的收盘价计）
last_price = df.iloc[-1]['close']  # 最后一天的收盘价
last_money = hold * last_price

# 计算总收益
# 总收益 = 最终现金 + 剩余股票价值 - 初始本金
total_profit = money + last_money - first_money
print(f"最终总收益为：{total_profit} 元")
```

![](img/cf4b64cab4715c2072c2a0c00893b0b5_112.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_114.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_116.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_118.png)

如果最后一天是死叉，那么循环中已经卖出了所有股票，`hold` 为0，`last_money` 也为0，不影响计算结果。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_120.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_122.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_124.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_126.png)

## 总结

![](img/cf4b64cab4715c2072c2a0c00893b0b5_128.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_130.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_132.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_134.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_136.png)

本节课中我们一起学习了如何将一个理论上的双均线交易策略转化为具体的Python代码。我们实现了以下核心步骤：
1.  **整合信号**：将金叉和死叉时间点标记并整合到同一时间序列中。
2.  **模拟交易**：初始化资金和持仓变量，遍历交易信号，严格按照“金叉买入，死叉卖出”的规则执行交易，并更新资金和持仓状态。
3.  **计算收益**：在交易模拟结束后，结算所有资产（现金+股票市值），减去初始本金得出最终总收益。

![](img/cf4b64cab4715c2072c2a0c00893b0b5_138.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_140.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_142.png)

![](img/cf4b64cab4715c2072c2a0c00893b0b5_144.png)

通过这个案例，你可以感受到金融量化分析的基本流程：**获取数据 -> 分析/生成信号 -> 回测策略 -> 评估绩效**。虽然本例是基于历史数据的“事后”回测，但其思路和方法是构建更复杂、面向未来策略的基石。你可以尝试将此策略应用到其他股票数据上，或修改规则（如使用收盘价交易、加入交易手续费等），进行更深入的探索。