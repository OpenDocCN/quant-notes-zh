# Python量化48：Martingale摆动交易策略 - P1 - 📈

## 概述
在本节课中，我们将学习如何将马丁格尔资金管理方法应用于一个基于支撑阻力位和蜡烛形态的获胜交易策略。我们将通过Python代码进行回测，分析结合马丁格尔方法后策略表现的变化。

![](img/d7165f7d239a589abff6283b03ea9d0e_1.png)

---

## 1️⃣：策略基础与数据准备

上一节我们介绍了课程目标，本节中我们来看看策略的基础构成和所需数据的准备工作。

![](img/d7165f7d239a589abff6283b03ea9d0e_3.png)

首先，我们需要加载并清理历史价格数据。本教程使用2003年至2021年的欧元/美元日线数据。

```python
# 示例：加载并检查数据
import pandas as pd
# 假设数据已加载到变量 `data` 中
print(data.tail())
```

![](img/d7165f7d239a589abff6283b03ea9d0e_5.png)

我们使用自定义函数来识别图表中的支撑位和阻力位。这些函数在之前的视频中有详细解释。

此外，我们还需要其他函数来识别特定的蜡烛形态，例如“看跌吞没”或“看涨吞没”形态。

![](img/d7165f7d239a589abff6283b03ea9d0e_7.png)

以下是检测形态是否接近关键价位的函数示例：

```python
def is_near_resistance(row, levels, tolerance=0.005):
    """检查价格是否接近阻力位"""
    for level in levels:
        if abs(row[‘High’] - level) / level < tolerance:
            return True
    return False
```

---

## 2️⃣：信号生成逻辑

在准备好数据和函数后，本节我们将探讨如何根据规则生成交易信号。

我们的策略逻辑如下：当出现特定的看跌蜡烛形态（如看跌吞没）且价格接近阻力位时，产生卖出信号。反之，当出现看涨形态且价格接近支撑位时，产生买入信号。

以下是生成信号的核心逻辑代码片段：

```python
# 假设已计算出支撑阻力位列表：support_levels, resistance_levels
# 假设已有函数判断形态：is_bearish_engulfing(), is_bullish_engulfing()

def generate_signal(row):
    signal = 0  # 0表示无信号
    # 检查卖出条件
    if is_bearish_engulfing(row) and is_near_resistance(row, resistance_levels):
        signal = 1  # 卖出信号
    # 检查买入条件
    elif is_bullish_engulfing(row) and is_near_support(row, support_levels):
        signal = 2  # 买入信号
    return signal

# 应用函数到数据框
data[‘Signal’] = data.apply(generate_signal, axis=1)
```

数据框中将新增一列“Signal”，其值为0、1或2，分别代表无信号、卖出和买入。

---

## 3️⃣：回测框架与基础策略表现

在生成信号之后，我们需要一个回测框架来评估策略表现。本节我们将使用`backtesting.py`库构建回测，并首先观察不使用马丁格尔方法时的策略结果。

我们定义一个继承自`Strategy`类的自定义策略。初始头寸规模设置为账户资金的5%（即`position_size = 0.05`）。

以下是策略类的框架：

```python
from backtesting import Strategy, Backtest

class SwingStrategy(Strategy):
    # 初始头寸规模为账户的5%
    position_size = 0.05

    def init(self):
        # 初始化指标
        pass

    def next(self):
        # 获取当前信号
        current_signal = self.data.Signal[-1]
        # 检查是否有未平仓头寸
        if not self.position:
            if current_signal == 2:  # 买入信号
                self.buy(size=self.position_size)
            elif current_signal == 1:  # 卖出信号
                self.sell(size=self.position_size)
```

使用1万美元初始资金进行回测（暂不考虑佣金），该基础策略获得了约**5.12%**的回报率，胜率约为60%。这表明信号本身是有效的。

---

## 4️⃣：引入马丁格尔资金管理方法

上一节我们看到基础策略表现尚可，本节中我们将核心的马丁格尔方法引入策略，观察其影响。

**马丁格尔方法**的核心公式是：在亏损交易后，将下一笔交易的头寸规模加倍，以期用一次盈利弥补之前的连续亏损。
> 头寸规模 = 基础规模 × 2^连续亏损次数

我们需要修改策略的`next`方法，在开仓前检查上一笔交易是否亏损，并据此调整本次交易的头寸大小。

以下是整合了马丁格尔逻辑的代码修改部分：

```python
class MartingaleSwingStrategy(SwingStrategy):
    def next(self):
        current_signal = self.data.Signal[-1]
        if not self.position:
            # 动态计算头寸规模
            trade_size = self.position_size
            # 检查是否有已平仓交易，且最后一笔是亏损的
            if len(self.trades) > 0 and self.trades[-1].pl < 0:
                trade_size = trade_size * 2  # 亏损后加倍
            elif len(self.trades) > 0 and self.trades[-1].pl > 0:
                trade_size = self.position_size  # 盈利后重置

            # 根据信号开仓
            if current_signal == 2:
                self.buy(size=trade_size)
            elif current_signal == 1:
                self.sell(size=trade_size)
```

启用马丁格尔方法后，策略回报率从**5.12%**提升至约**9.49%**。权益曲线显示，在经历连续亏损后，一次加仓的盈利交易有效地拉高了整体净值。

---

## 5️⃣：参数实验与风险警示

在见证了马丁格尔的潜在增益后，本节我们通过调整参数进行实验，并重点揭示其伴随的巨大风险。

首先，我们尝试改变基础头寸规模。将每笔交易投入的资金比例从5%提高到10%。

```python
# 将初始头寸规模改为10%
MartingaleSwingStrategy.position_size = 0.10
```

回测结果显示，在连续亏损发生时，由于头寸规模指数级增长（10% -> 20% -> 40%...），账户资金可能迅速被侵蚀殆尽，导致爆仓。

以下是需要警惕的关键点：

*   **高风险性**：连续亏损会导至头寸急剧放大，可能单次亏损就抹去大量本金。
*   **资金需求**：策略要求账户有极其充裕的资金来应对多次加倍下注。
*   **策略依赖**：马丁格尔并不能将亏损策略变为盈利，它只是资金管理方式。策略本身必须具备正期望值。

> **重要提示**：本教程中的马丁格尔方法仅用于教育与演示目的。在实际交易中运用此方法需要极其谨慎的资金管理和风险控制。

---

## 总结
本节课中我们一起学习了如何将一个基于支撑阻力与蜡烛形态的摆动交易策略与马丁格尔资金管理方法相结合。

![](img/d7165f7d239a589abff6283b03ea9d0e_9.png)

我们首先回顾了策略的信号生成逻辑，然后使用回测框架评估了其基础表现。接着，我们引入了马丁格尔方法，看到了它在特定条件下对回报率的提升作用。最后，我们通过参数实验深刻认识到该方法所蕴含的巨额风险。

![](img/d7165f7d239a589abff6283b03ea9d0e_10.png)

核心收获是：马丁格尔是一把双刃剑，它能放大收益，但更能加速亏损。任何资金管理方法都应在理解其原理和风险的基础上，结合具有正期望值的交易策略审慎使用。