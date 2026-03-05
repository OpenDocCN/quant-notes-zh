# AI量化交易实战课程：12：从回测到交易 - 机器学习教程

![](img/1a77687c3d65af8e9fdc39e6210acabb_1.png)

![](img/1a77687c3d65af8e9fdc39e6210acabb_2.png)

![](img/1a77687c3d65af8e9fdc39e6210acabb_3.png)

## 概述
在本节课中，我们将学习如何将强化学习中的Q-Learning算法应用到实际的股票交易策略中。我们将基于上一节课构建的Q-Learner类，通过定义交易环境的状态、动作和奖励函数，构建一个完整的量化交易策略，并与基于简单规则的手动策略进行对比。

---

## 数据准备与因子计算

上一节我们介绍了Q-Learning的基本原理和实现。本节中我们来看看如何将其应用于交易。首先，我们需要准备数据和定义市场特征。

我们使用的数据是2008年至2012年标普500成分股的日线数据。数据主要包含调整后的收盘价。为了便于模型处理，我们首先对价格数据进行归一化处理，将每日价格除以初始日的价格，使价格序列从1开始。

**核心概念：价格归一化**
```python
def normalize_prices(prices):
    # 使用前向和后向填充处理缺失值
    prices_filled = prices.fillna(method='ffill').fillna(method='bfill')
    # 归一化：每日价格除以初始日价格
    normalized = prices_filled.div(prices_filled.iloc[0])
    return normalized
```

Q-Learning模型不能直接处理连续的价格数据。因此，我们需要将市场状态离散化。我们通过计算技术因子，并将因子值离散化为有限的状态来实现这一点。

本节课我们选取三个经典的技术因子作为市场状态的表征：

1.  **简单移动平均线比率**：计算当前价格与20日简单移动平均线的比值。
    *   **公式**: `price / SMA(price, window=20)`
2.  **布林带位置**：计算价格在布林带中的相对位置。布林带由20日移动平均线及其上下两倍标准差构成。
    *   **公式**: `(price - lower_band) / (upper_band - lower_band)`
3.  **动量因子**：计算过去20日的价格变化率。
    *   **公式**: `(price[t] - price[t-20]) / price[t-20]`

以下是计算这些因子的核心函数：

```python
def compute_factors(normalized_prices, window=20):
    # 计算简单移动平均
    sma = normalized_prices.rolling(window=window).mean()
    sma_ratio = normalized_prices / sma

    # 计算布林带
    rolling_std = normalized_prices.rolling(window=window).std()
    upper_band = sma + 2 * rolling_std
    lower_band = sma - 2 * rolling_std
    bb_position = (normalized_prices - lower_band) / (upper_band - lower_band)

    # 计算动量
    momentum = (normalized_prices - normalized_prices.shift(window)) / normalized_prices.shift(window)

    return sma_ratio, bb_position, momentum
```

---

![](img/1a77687c3d65af8e9fdc39e6210acabb_5.png)

![](img/1a77687c3d65af8e9fdc39e6210acabb_6.png)

## 构建基于规则的手动交易策略

在引入Q-Learning之前，我们先建立一个简单的基于规则的策略作为基准。这个策略结合了上述三个因子，制定买卖规则。

该策略的逻辑是：当市场出现极端情况（价格低于均线、处于布林带下轨且动量向下）时，认为市场会均值回归，从而执行买入操作。

以下是该策略的核心决策逻辑：

```python
def manual_trading_decision(sma_ratio, bb_pos, momentum, current_holdings):
    # 定义买入信号条件
    buy_signal = (momentum < -0.05) and (bb_pos < 0) and (sma_ratio < 0.95) and (current_holdings < 1000)
    # 定义卖出信号条件
    sell_signal = (momentum > 0.05) and (bb_pos > 1) and (sma_ratio > 1.05) and (current_holdings > -1000)

    if buy_signal:
        return ‘BUY‘, 1000  # 动作：买入1000股
    elif sell_signal:
        return ‘SELL‘, 1000 # 动作：卖出1000股
    else:
        return ‘HOLD‘, 0    # 动作：持仓不变
```

我们使用一个回测框架来模拟该策略的交易，计算其每日持仓、现金流、组合价值以及夏普比率、最大回撤等绩效指标。

---

## 应用Q-Learning构建交易策略

现在，我们将Q-Learning框架应用到交易中。核心在于如何将交易问题建模成强化学习问题。

![](img/1a77687c3d65af8e9fdc39e6210acabb_8.png)

![](img/1a77687c3d65af8e9fdc39e6210acabb_10.png)

![](img/1a77687c3d65af8e9fdc39e6210acabb_12.png)

### 定义状态、动作与奖励

我们需要定义三个关键要素：

1.  **状态**：我们将三个因子值各自离散化为10个区间（0-9）。一个完整的状态由三个因子的离散值组合而成，例如“445”。因此，总状态数为 10 × 10 × 10 = 1000。
    *   **代码**: 使用`pd.cut`函数进行等宽离散化。
2.  **动作**：我们定义三个离散动作：`0`（做多/买入）、`1`（做空/卖出）、`2`（空仓/不操作）。
3.  **奖励**：奖励函数设计为每日的盈亏。即，当日持仓乘以价格变化。为了抑制过度交易，可以从奖励中减去一个基于交易量的冲击成本。
    *   **公式**: `reward = holdings * daily_price_change - impact_cost`

### 训练与测试流程

我们的策略学习器（`StrategyLearner`）工作流程分为两步：

1.  **训练阶段**：使用历史数据（例如2008年）来训练Q-Table。我们模拟在该时间段内的交易，并根据每一步的收益（奖励）来更新Q-Table。这个过程相当于在历史数据上寻找最优策略。
2.  **测试/交易阶段**：使用训练好的Q-Table，在新的数据（例如2009年）上进行交易。对于每一天，根据当前因子计算出的状态，查询Q-Table选择具有最高Q值的动作并执行。

以下是策略学习器与市场交互的核心循环：

```python
class StrategyLearner:
    def __init__(self):
        self.qlearner = QLearner(num_states=1000, num_actions=3, alpha=0.2, gamma=0.9, rar=0.5, radr=0.99)

    def train(self, training_data):
        # 在训练数据上循环多次，更新Q-Table
        for _ in range(iterations):
            state = self.discretize_state(training_data.iloc[0]) # 获取初始状态
            for i in range(1, len(training_data)):
                action = self.qlearner.querysetstate(str(state))
                # 执行动作，获取收益（考虑交易成本）
                reward = self.calculate_reward(action, training_data.iloc[i])
                # 获取新状态
                next_state = self.discretize_state(training_data.iloc[i])
                # 更新Q-Table
                next_action = self.qlearner.query(str(next_state), reward)
                state = next_state

    def test_policy(self, test_data):
        # 使用训练好的Q-Table在测试数据上交易
        trades = []
        state = self.discretize_state(test_data.iloc[0])
        for i in range(1, len(test_data)):
            # 查询最优动作，不更新Q-Table
            action = self.qlearner.querysetstate(str(state))
            trades.append(action)
            state = self.discretize_state(test_data.iloc[i])
        return trades
```

![](img/1a77687c3d65af8e9fdc39e6210acabb_14.png)

![](img/1a77687c3d65af8e9fdc39e6210acabb_16.png)

### 模型优化思考

我们实现的Q-Learning交易模型是一个基础框架。在实际应用中，有许多可以改进和探索的方向：

*   **状态设计**：离散化为10个区间是启发式的。可以根据因子值的分布（如使用分位数）进行非均匀离散化，或者使用降维技术处理更多因子。
*   **奖励函数**：当前的奖励只关注短期收益。可以引入风险调整，例如在奖励中减去收益的波动率，以鼓励更稳定的策略。
*   **动作空间**：当前动作是固定的买卖数量。可以扩展为多档位操作（如强信号时多买，弱信号时少买）。
*   **滚动训练**：策略可以定期（如每月）用最新的数据重新训练Q-Table，以适应市场变化。

---

## 总结

![](img/1a77687c3d65af8e9fdc39e6210acabb_18.png)

本节课中我们一起学习了如何将Q-Learning强化学习算法应用于量化交易策略的构建。

我们首先回顾了Q-Learning的原理，然后将其与量化交易流程结合：
1.  通过计算并离散化技术因子来定义市场状态。
2.  将买卖操作定义为智能体的动作。
3.  以每日盈亏为基础设计奖励函数。
4.  利用历史数据训练Q-Table，学习在不同市场状态下应采取的最优动作。
5.  使用学习到的策略在样本外数据进行测试，并评估其绩效。

![](img/1a77687c3d65af8e9fdc39e6210acabb_20.png)

我们还将该策略与一个基于简单规则的手动策略进行了对比。虽然本次演示的模型较为基础，但它提供了一个完整的、将机器学习模型嵌入回测系统的框架。真正的策略优化需要在此基础上，对状态表征、奖励函数、训练方式等进行更深入的研究和迭代。