# 量化金融分析师.AQF：04：优矿框架中的Account和Position对象

![](img/9b81e0c0638fd1fde5428250055e0712_0.png)

![](img/9b81e0c0638fd1fde5428250055e0712_1.png)

在本节课中，我们将学习优矿量化平台中两个最核心的对象：`Account`（账户）和`Position`（持仓）。它们是策略执行和资金管理的基石，理解它们对于编写有效的量化策略至关重要。

## 核心框架概述

上一节我们介绍了控制交易环境的 `context` 对象。本节中我们来看看与之交互的 `Account` 对象。整个优矿平台的逻辑框架主要围绕 `context` 和 `account` 这两个核心对象构建。`context` 负责整个交易环境的调度控制，而 `account` 则对应策略的股票账户。账户与交易环境之间可以相互沟通和交互。

## Account对象详解

账户的配置在策略初始化阶段就已经完成。在优矿中，我们通过 `context` 对象来获取管理当前策略的账户实例。

```python
# 在 handle_data 函数中获取账户实例
account = context.get_account()
```

这行代码是必需的，它返回一个 `Account` 类的具体实例。这个实例 `account` 拥有许多属性和方法，用于管理账户的资金和交易。

以下是 `Account` 实例最常用的两个属性：

*   **`account.cash`**：获取当前账户的可用资金。
*   **`account.portfolio_value`**：获取当前账户的总价值，包括现金和持有的股票市值。

接下来，我们看看 `Account` 实例的关键方法。这些方法主要用于获取持仓信息和执行交易。

### 获取持仓信息的方法

`Account` 对象提供了两种获取持仓信息的方法，它们返回的是 `Position` 对象（我们稍后会详细讲解）。

*   **`account.get_position(symbol)`**：获取**指定单一资产**（如某只股票）的持仓实例。
*   **`account.get_positions()`**：获取当前账户**所有持仓**的情况。它返回一个字典，其中键（key）是股票代码，值（value）是对应股票的 `Position` 实例。

简单来说，`get_position` 用于查询某只特定股票的持仓，而 `get_positions` 用于获取整个持仓组合的概况。

### 执行交易的方法

执行交易是策略的核心动作，`Account` 对象提供了多种下单方法。

**1. `account.order(symbol, amount)`**

这是最简单的下单方法。
*   `symbol`：要交易的股票代码。
*   `amount`：交易数量。正数表示买入，负数表示卖出。

例如，`account.order(‘000001.XSHE’， 100)` 表示买入100股平安银行。

**2. `account.order_to(symbol, amount)`**

此方法用于将某只股票的持仓**调整到**目标数量。
*   `symbol`：要调整的股票代码。
*   `amount`：目标持仓数量。

假设原来持有股票A 100股，执行 `account.order_to(‘A’， 1000)` 后，系统会自动计算差额（1000 - 100 = 900），并买入900股，使最终持仓达到1000股。

**3. `account.order_percent(symbol, percent)`**

此方法按照账户总价值的固定比例进行交易。
*   `symbol`：要交易的股票代码。
*   `percent`：交易金额占账户总价值 (`portfolio_value`) 的比例。正数买入，负数卖出。

**重要提示**：这里的比例基准是账户总价值，而**不是**可用现金 (`cash`)。例如，一个100万的账户，执行 `account.order_percent(‘B’， 0.1)` 意味着买入价值10万元（100万 * 10%）的股票B，无论当前账户里已有多少股票持仓。

**4. `account.order_percent_to(symbol, percent)`**

此方法用于将某只股票的持仓市值**调整到**账户总价值的固定比例。
*   `symbol`：要调整的股票代码。
*   `percent`：目标持仓市值占账户总价值的比例。

假设账户总价值100万，股票C当前持仓市值占2%（即2万元）。执行 `account.order_percent_to(‘C’， 0.1)` 后，系统会买入股票C，使其持仓市值达到10万元（100万 * 10%），即净买入8万元。

**5. `account.close_positions()`**

此方法用于**平仓**，即卖出账户内所有持有的资产。

## Position对象详解

在了解了如何通过 `Account` 获取持仓后，我们来看看 `Position` 对象本身。`Position` 可以看作是一个独立的类，它通过 `account.get_position(symbol)` 或 `account.get_positions()` 返回的实例，代表了某只股票具体的持仓情况。

例如，获取单只股票的持仓实例：
```python
pos = account.get_position(‘000001.XSHE’)
```

或者，获取所有持仓字典后，再提取特定股票：
```python
all_positions = account.get_positions() # 返回一个字典
pos_a = all_positions[‘000001.XSHE’] # 获取股票A的持仓实例
```

`Position` 实例包含以下重要属性，反映了该持仓的实时状态：

*   **`pos.amount`**：该股票的持仓数量。
*   **`pos.available_amount`**：该股票当前可卖出的数量（考虑T+1等交易规则）。
*   **`pos.profit`**：该持仓的浮动盈亏。
*   **`pos.cost`**：该股票的平均持仓成本。
*   **`pos.value`**：该股票的当前持仓市值。这个值会随着股票市场价格波动而变化。

## 对象间的关系与总结

![](img/9b81e0c0638fd1fde5428250055e0712_3.png)

本节课中我们一起学习了优矿框架中最重要的三个对象/类：`Context`， `Account` 和 `Position`。它们之间的关系与交互方式如下：

1.  **Context 与 Account**：通过 `context.get_account()` 方法，交易环境可以获取并操作对应的策略账户。
2.  **Account 与 Position**：通过 `account.get_position(symbol)` 或 `account.get_positions()` 方法，账户可以查询和管理其具体的持仓对象。

![](img/9b81e0c0638fd1fde5428250055e0712_5.png)

![](img/9b81e0c0638fd1fde5428250055e0712_7.png)

这张关系图清晰地展示了三者的交互逻辑：`Context` 作为总控，管理着 `Account`；`Account` 作为资金和交易执行单元，管理着多个 `Position`。理解这个框架后，我们已经能够看懂并编写一些基础的量化策略代码了。在接下来的课程中，我们将通过实际策略案例来巩固对这些对象的运用。