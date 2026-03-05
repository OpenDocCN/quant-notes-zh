# Python金融量化：P16：双均线策略的测试 🧪

![](img/ddab405da28148176e8d77bfe2f664cf_1.png)

![](img/ddab405da28148176e8d77bfe2f664cf_3.png)

![](img/ddab405da28148176e8d77bfe2f664cf_5.png)

![](img/ddab405da28148176e8d77bfe2f664cf_7.png)

![](img/ddab405da28148176e8d77bfe2f664cf_9.png)

在本节课中，我们将学习如何基于双均线策略的金叉和死叉信号，模拟一个完整的股票买卖过程，并计算最终的收益情况。我们将从2010年开始，使用10万元初始资金，在金叉时尽可能多地买入股票，在死叉时全部卖出，并考虑所有可能的情况，例如最终可能持有未卖出的股票。

![](img/ddab405da28148176e8d77bfe2f664cf_11.png)

## 策略逻辑分析

![](img/ddab405da28148176e8d77bfe2f664cf_13.png)

![](img/ddab405da28148176e8d77bfe2f664cf_15.png)

![](img/ddab405da28148176e8d77bfe2f664cf_17.png)

上一节我们介绍了如何识别金叉和死叉信号。本节中，我们来看看如何将这些信号转化为实际的买卖操作。

![](img/ddab405da28148176e8d77bfe2f664cf_19.png)

![](img/ddab405da28148176e8d77bfe2f664cf_21.png)

![](img/ddab405da28148176e8d77bfe2f664cf_23.png)

![](img/ddab405da28148176e8d77bfe2f664cf_25.png)

首先，我们需要明确几个核心规则：
*   **买卖时机**：金叉买入，死叉卖出。
*   **买卖价格**：使用信号出现当日的开盘价作为交易单价。
*   **交易单位**：股票交易以“手”为单位，1手等于100股。因此，买入时需计算最多能买入多少手。
*   **特殊情况**：如果数据最后一天是金叉（买入），则这部分股票没有对应的死叉卖出。我们需要用最后一天的收盘价来估算这部分剩余股票的价值，并将其计入总收益。

![](img/ddab405da28148176e8d77bfe2f664cf_27.png)

![](img/ddab405da28148176e8d77bfe2f664cf_29.png)

![](img/ddab405da28148176e8d77bfe2f664cf_31.png)

![](img/ddab405da28148176e8d77bfe2f664cf_33.png)

![](img/ddab405da28148176e8d77bfe2f664cf_35.png)

![](img/ddab405da28148176e8d77bfe2f664cf_37.png)

## 数据准备与信号整合

![](img/ddab405da28148176e8d77bfe2f664cf_39.png)

首先，我们需要将之前识别出的金叉和死叉时间点整合到一个序列中，并筛选出2010年之后的数据。

![](img/ddab405da28148176e8d77bfe2f664cf_41.png)

以下是整合与标记金叉、死叉信号的步骤：

![](img/ddab405da28148176e8d77bfe2f664cf_43.png)

![](img/ddab405da28148176e8d77bfe2f664cf_45.png)

1.  **创建金叉标识序列**：创建一个`Series`，索引为金叉时间，值全部设为`1`，作为金叉的标识。
    ```python
    s1 = pd.Series(1, index=golden_date)
    ```
2.  **创建死叉标识序列**：创建一个`Series`，索引为死叉时间，值全部设为`0`，作为死叉的标识。
    ```python
    s2 = pd.Series(0, index=death_date)
    ```
3.  **合并与排序**：将两个序列合并，并按时间索引排序，得到一个交替出现`1`（金叉）和`0`（死叉）的信号序列。
    ```python
    s = pd.concat([s1, s2]).sort_index()
    ```
4.  **筛选时间范围**：根据需求，只保留2010年及之后的信号。
    ```python
    s = s[‘2010’:‘2020’]
    ```
现在，`s`这个序列就清晰地记录了从2010年到2020年每一个买卖信号点及其类型。

## 模拟交易过程

有了信号序列，我们就可以开始模拟交易了。我们需要定义几个关键变量来跟踪资金和持仓状态。

![](img/ddab405da28148176e8d77bfe2f664cf_47.png)

![](img/ddab405da28148176e8d77bfe2f664cf_49.png)

![](img/ddab405da28148176e8d77bfe2f664cf_51.png)

![](img/ddab405da28148176e8d77bfe2f664cf_53.png)

![](img/ddab405da28148176e8d77bfe2f664cf_55.png)

以下是模拟交易所需的核心变量：
*   `first_money = 100000`：初始本金，固定不变，用于最终计算收益。
*   `money = first_money`：动态资金，买卖股票时从中扣除或增加。
*   `hold = 0`：当前持有的股票数量（单位：股）。

![](img/ddab405da28148176e8d77bfe2f664cf_57.png)

![](img/ddab405da28148176e8d77bfe2f664cf_59.png)

![](img/ddab405da28148176e8d77bfe2f664cf_61.png)

![](img/ddab405da28148176e8d77bfe2f664cf_63.png)

![](img/ddab405da28148176e8d77bfe2f664cf_65.png)

![](img/ddab405da28148176e8d77bfe2f664cf_67.png)

![](img/ddab405da28148176e8d77bfe2f664cf_69.png)

接下来，我们遍历信号序列`s`，根据每个信号是`1`（金叉）还是`0`（死叉）执行相应的操作。

![](img/ddab405da28148176e8d77bfe2f664cf_71.png)

![](img/ddab405da28148176e8d77bfe2f664cf_73.png)

![](img/ddab405da28148176e8d77bfe2f664cf_75.png)

![](img/ddab405da28148176e8d77bfe2f664cf_77.png)

![](img/ddab405da28148176e8d77bfe2f664cf_79.png)

![](img/ddab405da28148176e8d77bfe2f664cf_81.png)

### 金叉买入逻辑

![](img/ddab405da28148176e8d77bfe2f664cf_83.png)

![](img/ddab405da28148176e8d77bfe2f664cf_85.png)

![](img/ddab405da28148176e8d77bfe2f664cf_87.png)

当信号为`1`时，执行买入操作。

![](img/ddab405da28148176e8d77bfe2f664cf_89.png)

1.  **获取买入单价**：获取金叉日期对应的开盘价。
    ```python
    buy_time = s.index[i] # 获取金叉日期
    buy_price = df.loc[buy_time, ‘open’] # 获取该日开盘价作为买入单价
    ```
2.  **计算可买数量**：用当前资金`money`除以一手（100股）所需金额，得到最多能买多少手，再转换为股数。
    ```python
    hand_count = money // (buy_price * 100) # 计算最多能买多少手
    buy_count = hand_count * 100 # 转换为股数
    ```
3.  **更新持仓与资金**：增加持仓数量，并从动态资金中扣除买入股票的花费。
    ```python
    hold += buy_count
    money -= buy_count * buy_price
    ```

![](img/ddab405da28148176e8d77bfe2f664cf_91.png)

![](img/ddab405da28148176e8d77bfe2f664cf_93.png)

![](img/ddab405da28148176e8d77bfe2f664cf_95.png)

![](img/ddab405da28148176e8d77bfe2f664cf_97.png)

### 死叉卖出逻辑

![](img/ddab405da28148176e8d77bfe2f664cf_99.png)

当信号为`0`时，执行卖出操作。

1.  **获取卖出单价**：获取死叉日期对应的开盘价。
    ```python
    sell_time = s.index[i] # 获取死叉日期
    sell_price = df.loc[sell_time, ‘open’] # 获取该日开盘价作为卖出单价
    ```
2.  **更新资金与持仓**：将持有的所有股票按卖出单价变现，加入动态资金，并将持仓数量清零。
    ```python
    money += hold * sell_price
    hold = 0 # 股票已全部卖出，持仓清零
    ```

![](img/ddab405da28148176e8d77bfe2f664cf_101.png)

![](img/ddab405da28148176e8d77bfe2f664cf_103.png)

![](img/ddab405da28148176e8d77bfe2f664cf_104.png)

![](img/ddab405da28148176e8d77bfe2f664cf_106.png)

![](img/ddab405da28148176e8d77bfe2f664cf_108.png)

![](img/ddab405da28148176e8d77bfe2f664cf_110.png)

## 计算最终收益

![](img/ddab405da28148176e8d77bfe2f664cf_112.png)

![](img/ddab405da28148176e8d77bfe2f664cf_114.png)

遍历完所有信号后，交易模拟基本完成。但还需要处理一种特殊情况：如果最后一个信号是金叉，则会有股票未卖出。

![](img/ddab405da28148176e8d77bfe2f664cf_116.png)

![](img/ddab405da28148176e8d77bfe2f664cf_118.png)

1.  **处理剩余股票价值**：用数据最后一天的收盘价估算剩余股票的价值。
    ```python
    last_price = df.iloc[-1][‘close’] # 获取最后一天的收盘价
    last_money = hold * last_price # 计算剩余股票价值
    ```
2.  **计算总收益**：总收益 = （动态资金 + 剩余股票价值） - 初始本金。
    ```python
    total_profit = (money + last_money) - first_money
    print(f“最终总收益为：{total_profit}元”)
    ```
    如果`total_profit`为正数，表示盈利；为负数，表示亏损。

![](img/ddab405da28148176e8d77bfe2f664cf_120.png)

![](img/ddab405da28148176e8d77bfe2f664cf_122.png)

![](img/ddab405da28148176e8d77bfe2f664cf_124.png)

![](img/ddab405da28148176e8d77bfe2f664cf_126.png)

![](img/ddab405da28148176e8d77bfe2f664cf_128.png)

## 总结与延伸

![](img/ddab405da28148176e8d77bfe2f664cf_130.png)

![](img/ddab405da28148176e8d77bfe2f664cf_132.png)

![](img/ddab405da28148176e8d77bfe2f664cf_134.png)

![](img/ddab405da28148176e8d77bfe2f664cf_136.png)

![](img/ddab405da28148176e8d77bfe2f664cf_138.png)

本节课中我们一起学习了如何将双均线策略的信号转化为一个可回测的交易模型。我们定义了清晰的买卖规则，使用Python模拟了从2010年开始的完整交易过程，并计算了策略的最终收益。

![](img/ddab405da28148176e8d77bfe2f664cf_140.png)

![](img/ddab405da28148176e8d77bfe2f664cf_142.png)

![](img/ddab405da28148176e8d77bfe2f664cf_144.png)

![](img/ddab405da28148176e8d77bfe2f664cf_146.png)

![](img/ddab405da28148176e8d77bfe2f664cf_148.png)

这个策略是基于历史数据进行的“回测”，它可以帮助我们验证某个交易想法在过去的市场中是否有效。虽然过去的表现不能完全代表未来，但严谨的回测是量化交易中不可或缺的一步。如果你对此感兴趣，可以尝试在“聚宽”等量化平台上，使用更丰富的金融数据和内置API来构建和测试更复杂的策略。