# 量化交易与AI：P19：第十二章第1节：从回测到交易 🚀

![](img/7080e3cce629a07ddf74d8b6943d4ca1_1.png)

![](img/7080e3cce629a07ddf74d8b6943d4ca1_2.png)

![](img/7080e3cce629a07ddf74d8b6943d4ca1_3.png)

## 概述
在本节课中，我们将学习如何将强化学习中的Q-Learning算法，从理论概念应用到实际的量化交易策略构建中。我们将基于历史数据，构建一个简单的交易机器人，并将其与基于规则的手动策略进行对比。

---

## 数据准备与因子计算

上一节我们介绍了Q-Learning的基本原理，本节中我们来看看如何将其与金融数据结合。首先，我们需要准备数据和计算市场特征。

我们使用的数据是2008年至2012年标普500指数的日级别数据。核心是调整后的收盘价（`Adjusted close price`）。

为了将连续的价格数据转化为Q-Learning可以处理的离散状态，我们需要先计算一些技术指标（因子）。本节课我们选取三个经典因子：

1.  **简单移动平均线比率**：计算当前价格与20日简单移动平均线（SMA）的比值。
    *   **公式**: `price / sma(price, window=20)`
2.  **布林带位置**：计算价格在布林带中的相对位置。布林带由中轨（20日SMA）和上下轨（中轨 ± 2倍标准差）构成。
    *   **公式**: `(price - lower_band) / (upper_band - lower_band)`
3.  **动量因子**：计算过去20日的价格变化率。
    *   **公式**: `(price[t] - price[t-20]) / price[t-20]`

在计算前，我们会对价格序列进行归一化处理，将所有股票的价格除以起始日的价格，使其从1开始，便于跨资产比较。

以下是计算因子的核心步骤代码框架：

```python
def compute_indicators(prices, window=20):
    # 计算简单移动平均线
    sma = prices.rolling(window=window).mean()
    sma_ratio = prices / sma

    # 计算布林带
    rolling_std = prices.rolling(window=window).std()
    upper_band = sma + (2 * rolling_std)
    lower_band = sma - (2 * rolling_std)
    bb_position = (prices - lower_band) / (upper_band - lower_band)

    # 计算动量
    momentum = (prices - prices.shift(window)) / prices.shift(window)

    return sma_ratio, bb_position, momentum
```

---

![](img/7080e3cce629a07ddf74d8b6943d4ca1_5.png)

![](img/7080e3cce629a07ddf74d8b6943d4ca1_6.png)

## 构建基于规则的手动交易策略（Benchmark）

在引入Q-Learning之前，我们先建立一个基于上述三个因子的简单规则策略作为基准（Benchmark）。这有助于我们理解问题，并与后续的机器学习策略进行对比。

该策略的逻辑是均值回归思想：当价格同时低于均线、处于布林带下轨且动量向下时，认为价格被低估，执行买入操作；反之则卖出。

以下是策略逻辑的伪代码描述：

```python
def manual_trade_decision(sma_ratio, bb_pos, momentum, current_holdings):
    # 买入信号：三个因子同时满足条件
    if (momentum < -0.05) and (bb_pos < 0) and (sma_ratio < 0.95) and (current_holdings < 1000):
        return ‘BUY‘, 1000  # 信号为买入，数量为1000股
    # 卖出信号：因子条件相反
    elif (momentum > 0.05) and (bb_pos > 1) and (sma_ratio > 1.05) and (current_holdings > -1000):
        return ‘SELL‘, 1000 # 信号为卖出，数量为1000股
    else:
        return ‘HOLD‘, 0    # 否则持有不动
```

通过回测系统运行此策略，我们可以得到其资金曲线和各项绩效指标（如夏普比率、最大回撤），作为后续对比的基准。

---

## 应用Q-Learning构建交易策略

![](img/7080e3cce629a07ddf74d8b6943d4ca1_8.png)

现在，我们进入核心部分：将Q-Learning应用于交易。关键在于如何将交易问题建模成强化学习框架。

![](img/7080e3cce629a07ddf74d8b6943d4ca1_10.png)

![](img/7080e3cce629a07ddf74d8b6943d4ca1_12.png)

### 问题建模
1.  **状态（State）**：我们将三个因子的值离散化后组合成一个唯一的状态标识。例如，每个因子被均匀划分为10个区间（0-9），那么三个因子组合出的总状态数就是 10 * 10 * 10 = 1000种。状态 `‘445‘` 可能代表第一个因子值在第4区间，第二个在第4区间，第三个在第5区间。
2.  **动作（Action）**：定义3个动作：`0`（卖出/做空）、`1`（持有）、`2`（买入/做多）。
3.  **奖励（Reward）**：奖励函数设计为当日持仓带来的损益。即 `reward = holdings * daily_price_change`。我们还可以在奖励中减去交易成本（手续费和市场冲击），以鼓励减少不必要的交易。

### 训练与测试流程
我们采用历史数据分段的方式进行：
1.  **训练阶段**：使用2008年的数据来训练和更新Q表。算法通过不断尝试不同的动作，并根据获得的奖励更新Q值，学习在特定市场状态下应采取的最佳动作。
2.  **测试阶段**：使用2009年的数据来测试训练好的策略。此时，策略根据当前状态查询Q表，选择Q值最高的动作执行，不再更新Q表。

### 代码框架
以下是策略学习器的核心框架：

```python
class StrategyLearner:
    def __init__(self):
        self.q_learner = QLearner(num_states=1000, num_actions=3, alpha=0.2, gamma=0.9)
        # ... 其他初始化

    def train(self, training_data):
        """使用历史数据训练Q表"""
        states = discretize_indicators(training_data) # 离散化状态
        for i in range(len(states)-1):
            s = states[i]
            a = self.q_learner.querysetstate(s) # 查询当前动作
            # 执行动作a，计算收益（奖励）
            reward = calculate_reward(a, training_data, i)
            s_prime = states[i+1] # 下一个状态
            # 用<s, a, r, s‘>更新Q表
            self.q_learner.update(s, a, reward, s_prime)

    def test(self, test_data):
        """使用训练好的Q表进行测试交易"""
        states = discretize_indicators(test_data)
        trades = []
        for s in states:
            a = self.q_learner.querysetstate(s) # 仅查询，不更新
            trades.append(convert_action_to_order(a)) # 将动作转换为订单
        return backtest(trades, test_data) # 回测生成绩效
```

---

## 策略对比与深入思考

运行代码后，我们可以将Q-Learning策略与手动规则策略的回测结果进行对比。通常会发现，Q-Learning策略在训练期可能表现较好，但在样本外测试时可能会出现过拟合或性能下降的情况。

![](img/7080e3cce629a07ddf74d8b6943d4ca1_14.png)

![](img/7080e3cce629a07ddf74d8b6943d4ca1_16.png)

这引出了几个值得深入思考和优化的方向：

1.  **状态离散化方法**：均匀分箱是否最优？是否可以基于波动率聚集特性进行非均匀分箱？或者使用更精细的离散化方法？
2.  **因子与状态空间**：
    *   增加因子数量会指数级扩大状态空间（“维度灾难”），如何筛选有效且低相关的因子？
    *   能否使用主成分分析等降维技术处理更多因子？
3.  **奖励函数设计**：当前的奖励只考虑了短期收益。是否可以加入风险调整（如惩罚回撤）、考虑交易成本、或引入长期折扣收益？
4.  **模型更新机制**：静态使用一年数据训练出的Q表可能无法适应市场变化。是否需要实现滚动窗口训练，定期用最新数据更新模型？
5.  **实战化考量**：在实盘中，计算因子需考虑性能。例如，移动平均线的更新可以用迭代方式 `sma_today = 0.95 * sma_yesterday + 0.05 * price_today`，避免重复计算。

---

## 总结与作业

本节课中我们一起学习了将Q-Learning算法从回测环境应用到交易策略构建的全过程。我们掌握了如何将金融市场数据转化为强化学习中的状态、动作和奖励，并构建了一个简单的交易机器人。

![](img/7080e3cce629a07ddf74d8b6943d4ca1_18.png)

**核心要点总结**：
*   量化交易与强化学习的结合点在于**问题建模**：定义状态、动作和奖励。
*   **因子计算与离散化**是将连续市场映射到离散状态的关键步骤。
*   通过**训练/测试分离**和**基准对比**来评估学习策略的有效性。
*   简单的模型实现后，存在大量可优化和深入研究的空间，这往往是策略提升的关键。

**建议的课后作业**：
1.  **实践**：将课程代码中的美股数据替换为A股数据，调整策略（如禁止卖空），观察策略表现。
2.  **优化**：尝试修改状态离散化的区间数（如从10个改为5个或20个），或改变奖励函数，观察对策略绩效的影响。
3.  **拓展**：思考并尝试实现“滚动训练”模式，让Q-Learning模型能够定期用最新数据更新，以适应市场变化。

![](img/7080e3cce629a07ddf74d8b6943d4ca1_20.png)

通过动手实践这些步骤，你将更深刻地理解如何将机器学习模型应用于复杂的金融交易场景中。