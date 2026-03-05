# Python金融量化+业务数据分析：P16：3.双均线策略的测试 📈

![](img/82f078983a9d7e79ce5faba4ae760059_1.png)

![](img/82f078983a9d7e79ce5faba4ae760059_3.png)

![](img/82f078983a9d7e79ce5faba4ae760059_5.png)

在本节课中，我们将学习如何基于双均线策略（金叉买入，死叉卖出）来模拟股票交易，并计算从2010年开始，使用10万元初始资金进行交易至今的总收益。我们将通过Python代码一步步实现这个策略的测试。

![](img/82f078983a9d7e79ce5faba4ae760059_7.png)

![](img/82f078983a9d7e79ce5faba4ae760059_9.png)

---

![](img/82f078983a9d7e79ce5faba4ae760059_11.png)

## 策略需求分析

![](img/82f078983a9d7e79ce5faba4ae760059_13.png)

![](img/82f078983a9d7e79ce5faba4ae760059_15.png)

![](img/82f078983a9d7e79ce5faba4ae760059_17.png)

上一节我们识别出了股票历史数据中的金叉和死叉时间点。本节中，我们来看看如何基于这些信号执行买卖操作，并计算最终收益。

![](img/82f078983a9d7e79ce5faba4ae760059_19.png)

![](img/82f078983a9d7e79ce5faba4ae760059_21.png)

具体的需求是：假设一个人从2010年开始，初始资金为10万元。在出现金叉信号时，尽可能多地买入股票；在出现死叉信号时，将所有持有的股票卖出。我们需要计算到今天为止，他的炒股总收益是多少。

![](img/82f078983a9d7e79ce5faba4ae760059_23.png)

![](img/82f078983a9d7e79ce5faba4ae760059_25.png)

![](img/82f078983a9d7e79ce5faba4ae760059_27.png)

![](img/82f078983a9d7e79ce5faba4ae760059_29.png)

![](img/82f078983a9d7e79ce5faba4ae760059_31.png)

以下是实现此需求的关键分析点：
1.  **买卖单价**：使用交易当日的开盘价作为买卖股票的单价。
2.  **买卖时机**：金叉时间点买入，死叉时间点卖出。
3.  **特殊情况处理**：如果最后一个交易日是金叉（即买入后没有后续的死叉卖出），则手中会持有剩余股票。这部分剩余股票的价值需要计入总收益，其单价使用最后一个交易日的收盘价来衡量。

![](img/82f078983a9d7e79ce5faba4ae760059_33.png)

![](img/82f078983a9d7e79ce5faba4ae760059_35.png)

![](img/82f078983a9d7e79ce5faba4ae760059_37.png)

![](img/82f078983a9d7e79ce5faba4ae760059_39.png)

---

## 代码实现步骤

![](img/82f078983a9d7e79ce5faba4ae760059_41.png)

### 步骤一：准备金叉与死叉信号序列

首先，我们需要将之前找出的金叉和死叉时间点，整合并筛选到2010年之后。

![](img/82f078983a9d7e79ce5faba4ae760059_43.png)

![](img/82f078983a9d7e79ce5faba4ae760059_45.png)

```python
# 假设 golden_date 是金叉时间列表，death_date 是死叉时间列表
# 创建Series，用1标记金叉，用0标记死叉
s1 = pd.Series(1, index=golden_date)  # 金叉标识
s2 = pd.Series(0, index=death_date)   # 死叉标识

# 合并两个Series并按时间索引排序
s = pd.concat([s1, s2]).sort_index()

# 筛选出2010年之后的数据
s = s['2010':'2020']
```
现在，Series `s` 的索引是时间，值1代表金叉（买入信号），值0代表死叉（卖出信号）。

![](img/82f078983a9d7e79ce5faba4ae760059_47.png)

![](img/82f078983a9d7e79ce5faba4ae760059_49.png)

![](img/82f078983a9d7e79ce5faba4ae760059_51.png)

![](img/82f078983a9d7e79ce5faba4ae760059_53.png)

### 步骤二：初始化交易变量

![](img/82f078983a9d7e79ce5faba4ae760059_55.png)

![](img/82f078983a9d7e79ce5faba4ae760059_57.png)

![](img/82f078983a9d7e79ce5faba4ae760059_59.png)

![](img/82f078983a9d7e79ce5faba4ae760059_61.png)

![](img/82f078983a9d7e79ce5faba4ae760059_63.png)

接下来，我们初始化交易所需的变量。

![](img/82f078983a9d7e79ce5faba4ae760059_65.png)

![](img/82f078983a9d7e79ce5faba4ae760059_67.png)

![](img/82f078983a9d7e79ce5faba4ae760059_69.png)

![](img/82f078983a9d7e79ce5faba4ae760059_71.png)

```python
first_money = 100000  # 初始本金，固定不变
money = first_money   # 可变资金，用于买卖操作
hold = 0              # 持有股票的数量（单位：股）
```
*   `first_money` 是初始的10万元本金，用于最终计算收益。
*   `money` 是账户可变资金，买入时减少，卖出时增加。
*   `hold` 记录当前持有多少股股票。

![](img/82f078983a9d7e79ce5faba4ae760059_73.png)

![](img/82f078983a9d7e79ce5faba4ae760059_75.png)

![](img/82f078983a9d7e79ce5faba4ae760059_77.png)

![](img/82f078983a9d7e79ce5faba4ae760059_79.png)

![](img/82f078983a9d7e79ce5faba4ae760059_81.png)

### 步骤三：遍历信号执行交易

![](img/82f078983a9d7e79ce5faba4ae760059_83.png)

![](img/82f078983a9d7e79ce5faba4ae760059_85.png)

![](img/82f078983a9d7e79ce5faba4ae760059_87.png)

核心逻辑是遍历信号序列 `s`，根据值是1还是0来决定买入或卖出。

以下是交易逻辑的代码框架：

![](img/82f078983a9d7e79ce5faba4ae760059_89.png)

![](img/82f078983a9d7e79ce5faba4ae760059_91.png)

![](img/82f078983a9d7e79ce5faba4ae760059_93.png)

```python
# 遍历信号序列
for i in range(len(s)):
    # 获取当前信号对应的时间
    current_time = s.index[i]
    # 判断信号类型
    if s.iloc[i] == 1:  # 金叉，买入
        # 1. 获取买入单价（当天开盘价）
        buy_price = df.loc[current_time, 'open']
        # 2. 计算最多能买多少手（1手=100股）
        # 公式：可买手数 = 当前资金 // (买入单价 * 100)
        hand_count = money // (buy_price * 100)
        # 3. 更新持有股数
        hold += hand_count * 100
        # 4. 更新资金（扣除买入花费）
        money -= hold * buy_price

    else:  # 死叉，卖出
        # 1. 获取卖出单价（当天开盘价）
        sell_price = df.loc[current_time, 'open']
        # 2. 更新资金（增加卖出收入）
        money += hold * sell_price
        # 3. 卖出后，持有股数清零
        hold = 0
```
**代码关键点说明**：
*   `df` 是包含开盘价(`open`)等信息的原始股票数据DataFrame。
*   买入时，我们计算当前资金 `money` 最多能购买多少整手股票，然后更新 `hold` 和 `money`。
*   卖出时，将持有的所有股票(`hold`)按当日开盘价卖出，收入加入 `money`，并将 `hold` 重置为0。

![](img/82f078983a9d7e79ce5faba4ae760059_95.png)

![](img/82f078983a9d7e79ce5faba4ae760059_97.png)

### 步骤四：计算最终收益并处理剩余股票

循环结束后，需要计算总收益。这里有一个重要细节：如果最后一个信号是金叉，则循环结束后 `hold` 不为0，表示还有股票未卖出。

```python
# 计算剩余股票的价值（使用最后一天的收盘价）
last_price = df.iloc[-1]['close']  # 获取最后一天的收盘价
last_money = hold * last_price     # 剩余股票价值

![](img/82f078983a9d7e79ce5faba4ae760059_99.png)

![](img/82f078983a9d7e79ce5faba4ae760059_101.png)

![](img/82f078983a9d7e79ce5faba4ae760059_102.png)

# 计算总收益
# 公式：总收益 = (当前资金 + 剩余股票价值) - 初始本金
total_earn = (money + last_money) - first_money
print(f"最终总收益为：{total_earn}元")
```
*   如果最后一天是死叉，`hold` 为0，`last_money` 也为0，不影响计算结果。
*   总收益 = 账户可变资金 + 剩余股票价值 - 初始本金。

![](img/82f078983a9d7e79ce5faba4ae760059_104.png)

![](img/82f078983a9d7e79ce5faba4ae760059_106.png)

![](img/82f078983a9d7e79ce5faba4ae760059_108.png)

![](img/82f078983a9d7e79ce5faba4ae760059_110.png)

---

![](img/82f078983a9d7e79ce5faba4ae760059_112.png)

## 总结与拓展

![](img/82f078983a9d7e79ce5faba4ae760059_114.png)

本节课中我们一起学习了如何将双均线策略转化为可执行的Python代码，并进行历史回测，计算策略收益。我们实现了从信号生成、资金管理、买卖操作到最终收益计算的全流程。

![](img/82f078983a9d7e79ce5faba4ae760059_116.png)

![](img/82f078983a9d7e79ce5faba4ae760059_118.png)

![](img/82f078983a9d7e79ce5faba4ae760059_120.png)

![](img/82f078983a9d7e79ce5faba4ae760059_122.png)

这个策略是基于历史数据进行的“回测”，它可以帮助我们验证某个交易思路在过去的市场中是否有效。虽然过去的表现不能完全代表未来，但可以为未来的投资决策提供参考。

![](img/82f078983a9d7e79ce5faba4ae760059_124.png)

![](img/82f078983a9d7e79ce5faba4ae760059_126.png)

![](img/82f078983a9d7e79ce5faba4ae760059_128.png)

如果你对金融量化有更浓厚的兴趣，可以探索“聚宽”等量化平台。这些平台提供了丰富的金融数据和封装好的API，方便你更高效地编写、测试和实盘交易策略。

![](img/82f078983a9d7e79ce5faba4ae760059_130.png)

![](img/82f078983a9d7e79ce5faba4ae760059_132.png)

![](img/82f078983a9d7e79ce5faba4ae760059_134.png)

![](img/82f078983a9d7e79ce5faba4ae760059_136.png)

![](img/82f078983a9d7e79ce5faba4ae760059_138.png)

![](img/82f078983a9d7e79ce5faba4ae760059_140.png)

**核心公式回顾**：
*   可买手数：`hand_count = money // (buy_price * 100)`
*   总收益：`total_earn = (money + hold * last_price) - first_money`

![](img/82f078983a9d7e79ce5faba4ae760059_142.png)

![](img/82f078983a9d7e79ce5faba4ae760059_144.png)

![](img/82f078983a9d7e79ce5faba4ae760059_146.png)

通过本案例，你应该对Python在金融量化分析中的应用有了更直观的感受。量化交易的核心就是基于数据制定规则，并用程序自动执行这些规则。