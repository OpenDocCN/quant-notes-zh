# 量化交易：P18：第一十一章第1节： 量化交易的特征工程 🧠

![](img/30ec22493bec8e4603a9f5b6fa4070b3_1.png)

在本节课中，我们将学习强化学习中的 **Q-Learning** 算法。我们将通过一个简单的项目——让一个机器人在网格地图中找到走出迷宫的最佳路径——来直观地理解 Q-Learning 的核心概念和工作原理。下节课，我们会将学到的知识应用到股票交易场景中。

## 问题设定与目标 🎯

我们面临的问题是一个简化的环境：一个 10x10 的网格地图。

*   **状态 (State)**：机器人在网格中的每一个位置（共100个）对应一个唯一的状态。我们将二维坐标 `(row, column)` 离散化为一个状态编号，例如 `state = row * 10 + column`。
*   **动作 (Action)**：机器人在每个状态下可以采取四种离散动作：向上、向下、向左、向右移动一步。
*   **奖励 (Reward)**：环境会根据机器人的动作给予即时反馈。
    *   移动到普通空地（标记为0）：奖励为 **+1**。
    *   移动到墙壁（标记为1）：无法移动，保持原位，奖励为 **+1**（可以理解为尝试移动的代价）。
    *   移动到流沙（标记为5）：可以移动，但会获得很大的负奖励 **-100**，并且该位置会被标记为已访问过的流沙（标记为6）。
    *   移动到目标点（标记为3）：游戏结束，获得奖励 **+1**。
*   **目标**：智能体（机器人）的目标是学习一个策略，使得从起点（标记为2）到终点（标记为3）所获得的**长期累计奖励最大化**（或累计惩罚最小化）。这类似于交易中追求长期正收益的目标。

这个设定是股票交易的简化版。在交易中，动作（买、卖、持有）和状态（市场情况、持仓）可能更复杂，但核心思想一致：根据历史经验（状态-动作-奖励）学习最优决策。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_3.png)

## Q-Learning 核心思想 💡

Q-Learning 是一种无模型的强化学习算法。其核心是维护一个 **Q-Table**。这个表格记录了在每一个特定状态 `s` 下，采取每一个可能动作 `a` 所能获得的**预期长期回报值**，即 **Q值**。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_5.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_7.png)

智能体通过与环境的交互（尝试不同的动作，获得奖励）来不断更新这张 Q-Table。更新的原则是：**将历史经验与新的探索结果相结合**。

智能体的决策基于 Q-Table：在状态 `s` 时，选择能使 `Q(s, a)` 值最大的动作 `a`（利用）。同时，为了探索未知的可能更好的策略，会以一定概率随机选择动作（探索）。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_9.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_11.png)

## Q-Table 更新公式 📈

![](img/30ec22493bec8e4603a9f5b6fa4070b3_13.png)

这是 Q-Learning 算法最核心的部分。当我们执行了动作 `a`，从状态 `s` 转移到新状态 `s‘`，并收到即时奖励 `r` 后，我们按以下公式更新 `Q(s, a)`：

**`Q(s, a) = (1 - α) * Q(s, a) + α * [ r + γ * max(Q(s‘, a‘)) ]`**

让我们分解这个公式：

![](img/30ec22493bec8e4603a9f5b6fa4070b3_15.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_17.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_19.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_21.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_23.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_25.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_27.png)

*   **`Q(s, a)`**：在状态 `s` 下采取动作 `a` 的旧 Q 值。
*   **`α` (学习率 Learning Rate)**：取值范围 0 到 1。它控制了新信息覆盖旧信息的速度。`α` 越大，智能体越依赖于最新的经验。
*   **`(1 - α) * Q(s, a)`**：代表对**历史经验**的保留。
*   **`α * [ ... ]`**：代表对**新经验**的学习。
*   **`r`**：执行动作后获得的**即时奖励**。
*   **`γ` (折扣因子 Discount Factor)**：取值范围 0 到 1。它决定了未来奖励的现值。`γ` 越大，智能体越有远见，更重视长期回报。
*   **`max(Q(s‘, a‘))`**：在新状态 `s‘` 下，所有可能动作 `a‘` 中对应的最大 Q 值。这代表了**对未来最佳回报的估计**。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_29.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_31.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_33.png)

**公式直观理解**：新的 Q 值是旧 Q 值和新获得的经验估计值的加权平均。新经验估计不仅包含即时奖励 `r`，还包含了对未来可能获得的最佳回报的折现预期 `γ * max(Q(s‘, a‘))`。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_35.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_37.png)

## 代码实现步骤 🖥️

![](img/30ec22493bec8e4603a9f5b6fa4070b3_39.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_41.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_43.png)

我们将实现一个 `QLearner` 类。以下是需要完成的核心方法：

![](img/30ec22493bec8e4603a9f5b6fa4070b3_45.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_47.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_49.png)

### 1. 初始化 `__init__`

![](img/30ec22493bec8e4603a9f5b6fa4070b3_51.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_53.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_55.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_57.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_59.png)

我们需要初始化 Q-Table 和相关参数。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_61.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_63.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_65.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_67.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_69.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_71.png)

```python
import numpy as np

![](img/30ec22493bec8e4603a9f5b6fa4070b3_73.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_75.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_77.png)

class QLearner(object):
    def __init__(self, num_states, num_actions, alpha=0.2, gamma=0.9, random_action_rate=0.5, random_action_decay=0.99):
        self.num_states = num_states
        self.num_actions = num_actions
        self.alpha = alpha       # 学习率
        self.gamma = gamma       # 折扣因子
        self.random_action_rate = random_action_rate # 随机动作概率
        self.random_action_decay = random_action_decay # 随机动作概率衰减率

        # 初始化 Q-Table：维度为 [状态数量, 动作数量]
        # 使用均匀分布随机初始化，例如范围在 [-1, 1]
        self.q_table = np.random.uniform(low=-1.0, high=1.0, size=(num_states, num_actions))

        # 保存上一次的状态和动作，用于更新
        self.last_state = None
        self.last_action = None
```

![](img/30ec22493bec8e4603a9f5b6fa4070b3_79.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_81.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_83.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_85.png)

### 2. 查询动作 `query_set_state`

![](img/30ec22493bec8e4603a9f5b6fa4070b3_87.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_89.png)

此方法根据当前状态 `s` 和上一步的奖励 `r`（首次调用时 `r` 为 `None`），返回智能体应执行的动作 `a`。同时，如果提供了奖励 `r`，它会触发对上一次 `(s, a)` 的 Q 值更新。

```python
    def query_set_state(self, s, r=None):
        """
        s: 当前状态 (整数)
        r: 上一步动作获得的奖励 (float)。如果是第一步，则为 None。
        返回: 当前状态建议执行的动作 (整数，0到num_actions-1)
        """
        # 第一部分：如果有奖励r，则用上一次的(last_state, last_action)和当前的(s, r)来更新Q-Table
        if r is not None and self.last_state is not None and self.last_action is not None:
            self._update_q_table(self.last_state, self.last_action, s, r)

        # 第二部分：为当前状态s选择动作a
        # 以 random_action_rate 的概率随机选择动作（探索）
        if np.random.uniform() < self.random_action_rate:
            action = np.random.randint(0, self.num_actions)
        else:
            # 否则，选择当前状态下Q值最大的动作（利用）
            action = np.argmax(self.q_table[s, :])

        # 更新随机动作概率，使其随着时间衰减，让智能体后期更倾向于利用
        self.random_action_rate *= self.random_action_decay

        # 保存当前状态和动作用于下一次更新
        self.last_state = s
        self.last_action = action

        return action
```

### 3. 更新 Q-Table `_update_q_table`

![](img/30ec22493bec8e4603a9f5b6fa4070b3_91.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_93.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_95.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_97.png)

这是实现核心公式的私有方法。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_99.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_101.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_103.png)

```python
    def _update_q_table(self, s, a, s_prime, r):
        """
        根据公式更新 Q(s, a)
        s: 上一次的状态
        a: 上一次执行的动作
        s_prime: 执行动作a后到达的新状态
        r: 执行动作a后获得的即时奖励
        """
        # 获取旧的 Q 值
        old_q = self.q_table[s, a]

        # 计算新状态 s_prime 下的最大未来 Q 值估计
        max_future_q = np.max(self.q_table[s_prime, :])

        # Q-Learning 更新公式
        # Q(s,a) = (1-α)*Q(s,a) + α * [ r + γ * max(Q(s‘, a‘)) ]
        new_q = (1 - self.alpha) * old_q + self.alpha * (r + self.gamma * max_future_q)

        # 更新表格
        self.q_table[s, a] = new_q
```

![](img/30ec22493bec8e4603a9f5b6fa4070b3_105.png)

## 总结 🏁

![](img/30ec22493bec8e4603a9f5b6fa4070b3_107.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_109.png)

本节课我们一起学习了强化学习中的 **Q-Learning** 算法。

1.  **核心概念**：我们理解了 **状态 (State)**、**动作 (Action)**、**奖励 (Reward)** 和 **Q-Table** 在强化学习中的角色。
2.  **算法原理**：Q-Learning 通过与环境的交互来更新 Q-Table，其核心是 **`Q(s, a) = (1-α)*旧值 + α*(即时奖励+γ*未来估计)`** 的更新公式，平衡了历史经验与新探索。
3.  **代码实现**：我们实现了 `QLearner` 类的三个关键部分：初始化 Q-Table、根据状态查询动作、以及最重要的 Q-Table 更新逻辑。
4.  **与交易的关联**：这个网格寻路问题是股票交易的简化抽象。状态对应市场情况，动作对应交易决策，奖励对应盈亏。Q-Learning 提供了一种让机器从历史交易数据中自主学习策略的框架。

![](img/30ec22493bec8e4603a9f5b6fa4070b3_111.png)

![](img/30ec22493bec8e4603a9f5b6fa4070b3_113.png)

通过这个项目，你已经掌握了 Q-Learning 的基础。在下节课中，我们将尝试把这种思想应用到实际的股票交易数据中，探索如何用强化学习来训练一个简单的交易机器人。