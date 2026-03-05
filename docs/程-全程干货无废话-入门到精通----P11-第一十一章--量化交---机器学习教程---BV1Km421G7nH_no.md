# AI量化交易实战课程：11：量化交易的特征工程 - Q-Learning入门教程 🧠

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_1.png)

## 概述
在本节课中，我们将学习强化学习中的一个核心算法——Q-Learning。我们将通过一个简单的“机器人逃离地图”项目来直观理解其工作原理，并了解如何将其思想应用于量化交易场景。课程内容将尽可能简单直白，确保初学者能够跟上。

---

## 问题设定与直观理解 🗺️

首先，我们来明确本节课要解决的具体问题。我们有一个10x10的网格地图，其中包含不同的元素：
*   **起始位置 (2)**：机器人的出发点。
*   **目标位置 (3)**：机器人需要到达的终点。
*   **墙壁 (1)**：不可穿越的障碍物。
*   **普通路径 (0)**：可以自由通行，每走一步获得默认奖励（例如-1，表示消耗）。
*   **流沙 (5)**：可以通行，但会带来巨大的负奖励（例如-100）。

机器人的目标是找到从起点到终点的路径，并且**最小化累计惩罚（或最大化累计奖励）**。这类似于交易中追求长期正收益的目标。

上一节我们介绍了问题的基本设定，本节中我们来看看如何形式化这个问题，并引入强化学习的概念。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_3.png)

### 与股票交易的类比
这个简化问题与股票交易有相似之处：
1.  **状态 (State)**：地图中的每个位置对应一个状态。在交易中，状态可以是市场行情、持仓情况等。
2.  **动作 (Action)**：机器人可以执行的动作是离散的：上、下、左、右。在简化交易模型中，动作可以是买入、卖出、持有。
3.  **奖励 (Reward)**：执行动作后获得的即时反馈。在交易中，奖励可以是单笔交易的盈亏。

核心区别在于，现实中的交易动作和状态空间可能是连续的，但我们可以通过离散化来应用Q-Learning。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_5.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_7.png)

---

## Q-Learning核心概念与算法 🔧

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_9.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_11.png)

Q-Learning是一种**无模型**的强化学习算法，其核心是学习一个名为 **Q-Table** 的表格。这个表格记录了在每一个状态 `s` 下，采取每一个可能动作 `a` 所能获得的**长期期望奖励**。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_13.png)

### 算法流程
Q-Learning的学习过程是一个不断与环境交互、试错并更新的循环：

1.  **初始化**：随机初始化Q-Table（`Q[s, a]`）。
2.  **选择动作**：在当前状态 `s` 下，根据某种策略（如ε-greedy）选择一个动作 `a`。
3.  **执行动作**：执行动作 `a`，环境反馈给智能体新的状态 `s‘` 和即时奖励 `r`。
4.  **更新Q-Table**：根据观察到的 `(s, a, r, s‘)` 元组，使用以下公式更新Q-Table中 `Q[s, a]` 的值。
5.  **状态转移**：将当前状态更新为 `s‘`，重复步骤2-4。

### 更新公式
Q-Learning的核心在于其更新规则，它结合了历史经验和新获得的经验：

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_15.png)

`Q[s, a] = (1 - α) * Q[s, a] + α * (r + γ * max(Q[s‘, a‘]))`

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_17.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_19.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_21.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_23.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_25.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_27.png)

让我们来分解这个公式：
*   `Q[s, a]`：在状态 `s` 下采取动作 `a` 的长期价值。
*   `α (alpha)`：**学习率** (0 < α ≤ 1)。控制新信息覆盖旧信息的程度。α=1表示完全信任新估计，α=0表示完全不学习。
*   `r`：执行动作 `a` 后获得的**即时奖励**。
*   `γ (gamma)`：**折扣因子** (0 ≤ γ < 1)。衡量未来奖励相对于即时奖励的重要性。γ=0表示只关心眼前利益，γ接近1表示非常重视长期回报。
*   `max(Q[s‘, a‘])`：在新的状态 `s‘` 下，所有可能动作 `a‘` 中最大的Q值。这代表了**对未来最佳可能回报的估计**。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_29.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_31.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_33.png)

**公式解读**：新的Q值 `Q[s, a]` 是旧Q值（历史经验）和新的价值估计（即时奖励+对未来最佳状态的估计）的加权平均。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_35.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_37.png)

---

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_39.png)

## 代码实现解析 💻

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_41.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_43.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_45.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_47.png)

理解了算法原理后，我们来看如何用代码实现一个简单的Q-Learning智能体。以下是核心部分的伪代码和解释。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_49.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_51.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_53.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_54.png)

### 1. 智能体初始化
```python
class QLearner:
    def __init__(self, num_states, num_actions, alpha=0.2, gamma=0.9, epsilon=0.2):
        self.num_states = num_states
        self.num_actions = num_actions
        self.alpha = alpha  # 学习率
        self.gamma = gamma  # 折扣因子
        self.epsilon = epsilon  # 探索率（随机行动的概率）
        # 初始化Q-Table，可以随机初始化
        self.q_table = np.random.uniform(low=-1, high=1, size=(num_states, num_actions))
```

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_56.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_58.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_60.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_62.png)

### 2. 动作选择策略 (ε-greedy)
在每一步，智能体需要决定是**探索**（尝试新动作）还是**利用**（选择已知最好的动作）。
```python
def choose_action(self, state):
    # 以 epsilon 的概率进行随机探索
    if np.random.uniform() < self.epsilon:
        action = np.random.randint(0, self.num_actions)
    else:
        # 利用：选择当前状态下Q值最大的动作
        action = np.argmax(self.q_table[state, :])
    return action
```
随着学习进行，我们通常会逐渐减小 `epsilon`，让智能体从探索为主转向利用为主。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_64.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_66.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_68.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_70.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_72.png)

### 3. 核心更新函数
这是实现上述更新公式的关键函数。
```python
def learn(self, state, action, reward, next_state):
    # 当前状态-动作对的Q值
    current_q = self.q_table[state, action]
    # 新状态下所有动作中的最大Q值
    max_future_q = np.max(self.q_table[next_state, :])
    # 计算新的目标Q值
    target_q = reward + self.gamma * max_future_q
    # 应用Q-Learning更新公式
    new_q = (1 - self.alpha) * current_q + self.alpha * target_q
    # 更新Q-Table
    self.q_table[state, action] = new_q
```

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_74.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_76.png)

---

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_78.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_80.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_82.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_84.png)

## 从游戏到交易：概念迁移 💹

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_86.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_88.png)

现在，我们已经实现了一个能玩地图游戏的Q-Learning智能体。如何将这套框架迁移到股票交易上呢？关键在于重新定义三大要素：

以下是状态、动作和奖励在交易场景中的一种可能定义：

| 要素 | 地图游戏示例 | 股票交易迁移示例 |
| :--- | :--- | :--- |
| **状态 (State)** | 机器人在网格中的坐标 (x, y) | 离散化的市场状态组合，例如：`(价格趋势，波动率等级，持仓状态)` |
| **动作 (Action)** | 上，下，左，右 | 买入，卖出，持有 |
| **奖励 (Reward)** | 到达终点+100，踩到流沙-100，普通移动-1 | 单笔交易收益率，或资产组合的价值变化 |

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_90.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_92.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_94.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_96.png)

**核心思路一致**：智能体（交易算法）通过不断观察市场状态（State），做出交易决策（Action），并根据账户损益（Reward）来更新其内部模型（Q-Table），最终学会在何种市场状态下应采取何种交易动作以获得长期最大收益。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_98.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_100.png)

---

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_102.png)

## 总结与展望 🚀

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_104.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_106.png)

本节课我们一起学习了强化学习中的Q-Learning算法。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_108.png)

*   **我们首先**通过一个直观的网格世界问题，引入了状态、动作、奖励等核心概念。
*   **接着**，我们深入探讨了Q-Learning的算法原理，特别是其核心的Q-Table更新公式，该公式巧妙地平衡了历史经验与新知识。
*   **然后**，我们解析了代码实现的关键部分，包括智能体初始化、ε-greedy动作选择策略以及最重要的学习更新函数。
*   **最后**，我们探讨了如何将这套框架的思想迁移到量化交易领域，为下一节课将Q-Learning应用于实际交易数据奠定了基础。

Q-Learning的优势在于其概念清晰、实现相对简单，是理解强化学习的绝佳起点。然而，它也存在局限性，例如当状态空间非常庞大（“维度灾难”）时，Q-Table将变得难以维护。这时就需要引入深度学习，即 **Deep Q-Network (DQN)**，用神经网络来近似Q函数，这也是现代强化学习在复杂问题上取得成功的关键。

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_110.png)

![](img/05473ff2318f58f3c23d1b6a53d2ebe8_112.png)

在接下来的课程中，我们将利用本节课构建的QLearner，尝试将其应用于简单的股票交易场景，看看它是否能学习到有效的交易策略。