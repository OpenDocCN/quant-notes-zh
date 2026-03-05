# Python量化25：对冲策略 - P1：网格交易策略入门 🧠

在本节课中，我们将学习一种名为“网格交易”的对冲策略。这是一种不依赖技术指标预测趋势的简单方法，通过同时在多空两个方向开仓来利用市场波动获利。我们将使用Python进行策略的回测和实现。

![](img/24f7580cd9e3efdcadba5e34989bb7f7_1.png)

## 概述 📋

网格交易策略的核心思想是在价格图表上建立一个虚拟的“网格”。当价格触及网格线时，系统会同时执行买入和卖出操作，并设置止盈止损。策略的优势在于无需判断市场方向，且参数调整简单。

上一节我们介绍了策略的基本概念，本节中我们来看看如何用Python代码实现它。

## 策略原理与参数设置 ⚙️

策略围绕一个中心价格构建网格。网格线之间的距离是一个关键参数。

**核心参数公式：**
*   **网格中间价 (mid_price)**: 策略构建网格的基准价格。
*   **网格距离 (grid_distance)**: 网格线之间的间隔，例如 `0.005`。
*   **网格范围 (grid_range)**: 网格覆盖的价格区间，例如中间价上下 `0.1`。

生成网格的Python函数可以这样表示：
```python
import numpy as np

def generate_grid(mid_price, grid_distance, grid_range):
    return np.arange(mid_price - grid_range, mid_price + grid_range, grid_distance)
```

![](img/24f7580cd9e3efdcadba5e34989bb7f7_3.png)

## 策略执行流程 🔄

以下是策略的核心操作步骤：

1.  **生成网格**：根据设定的中间价、距离和范围，生成一系列价格水平线。
2.  **监控价格**：遍历每个K线（如5分钟K线），检查收盘价是否穿越了任何一条网格线。
3.  **触发交易**：当价格穿越网格线时，信号触发。系统会同时开设一个多头头寸和一个空头头寸。
4.  **设置风控**：为每个新开的头寸立即设置止盈和止损订单。初始版本使用固定的网格距离作为止损。
5.  **重复过程**：价格每次穿越网格线，都会平掉一对已有的头寸（实现止盈），并同时新开一对多空头寸。

## 代码实现与回测 💻

我们将使用`backtesting.py`库进行回测。首先需要定义生成交易信号的函数。

**信号生成逻辑代码：**
```python
def signal_function(df):
    signals = [0] * len(df) # 初始化信号列表
    grid_lines = generate_grid(mid_price=1.065, grid_distance=0.005, grid_range=0.1)
    for i in range(1, len(df)):
        for line in grid_lines:
            if df['close'].iloc[i-1] < line <= df['close'].iloc[i] or df['close'].iloc[i-1] > line >= df['close'].iloc[i]:
                signals[i] = 1 # 价格穿越网格线，触发信号
                break
    return signals
```

在回测类中，根据信号执行交易：

```python
from backtesting import Strategy

class GridStrategy(Strategy):
    def init(self):
        self.signal1 = self.I(signal_function, self.data.df)
    def next(self):
        current_signal = self.signal1[-1]
        if current_signal == 1 and len(self.trades) < 4: # 限制最大持仓数
            # 开多头头寸
            sl_long = self.data.Close[-1] - grid_distance
            tp_long = self.data.Close[-1] + grid_distance * tp_sl_ratio
            self.buy(sl=sl_long, tp=tp_long)
            # 开空头头寸
            sl_short = self.data.Close[-1] + grid_distance
            tp_short = self.data.Close[-1] - grid_distance * tp_sl_ratio
            self.sell(sl=sl_short, tp=tp_short)
```

使用欧元/美元5分钟数据进行两个月回测，初始版本（固定网格止损）获得了约7.8%的回报，夏普比率约为2.45。

## 策略优化：引入ATR 📈

上一节我们使用了固定值作为止损，本节中我们来看看如何用ATR（平均真实波幅）来改进它。ATR能动态反映市场波动性，使风控更适应市场环境。

![](img/24f7580cd9e3efdcadba5e34989bb7f7_5.png)

只需修改止损计算部分：
```python
# 计算ATR
df['ATR'] = ta.ATR(df['high'], df['low'], df['close'], timeperiod=16)
# 在策略中使用ATR替代固定网格距离作为止损基准
sl_long = self.data.Close[-1] - self.data.ATR[-1]
tp_long = self.data.Close[-1] + self.data.ATR[-1] * tp_sl_ratio
```

![](img/24f7580cd9e3efdcadba5e34989bb7f7_7.png)

使用ATR优化后，回测回报率为6.2%，但夏普比率提升至约6.39，资金曲线增长更平稳，回撤更小。

![](img/24f7580cd9e3efdcadba5e34989bb7f7_9.png)

## 策略的优缺点与注意事项 ⚠️

![](img/24f7580cd9e3efdcadba5e34989bb7f7_11.png)

**优点：**
*   **无需预测**：不依赖趋势判断，规避了方向性错误的风险。
*   **管理简单**：主要需要管理的参数是网格距离，策略逻辑清晰。
*   **利用波动**：在市场震荡（有“噪音”）时表现良好。

**挑战与注意事项：**
*   **亏损头寸处理**：在网格边缘开仓的头寸可能无法触及止盈。需制定额外规则处理，例如在每日收盘或达到一定总利润时平仓所有头寸并重置网格。
*   **参数依赖**：网格距离、时间框架、最大并行交易数等参数需要根据交易品种和市况调整。
*   **未计入成本**：示例回测未包含手续费和滑点，实盘前必须考虑这些成本并进行参数优化。
*   **单边行情风险**：在强劲的单边趋势市场中，策略可能持续产生亏损。

![](img/24f7580cd9e3efdcadba5e34989bb7f7_13.png)

## 总结 🎯

![](img/24f7580cd9e3efdcadba5e34989bb7f7_15.png)

本节课中我们一起学习了网格交易对冲策略。我们从策略原理入手，介绍了如何设置网格参数，并详细讲解了策略的触发和执行流程。随后，我们使用Python和`backtesting.py`库实现了基础版本的策略回测，并进一步引入了ATR指标来动态管理风险，优化了策略表现。

![](img/24f7580cd9e3efdcadba5e34989bb7f7_17.png)

![](img/24f7580cd9e3efdcadba5e34989bb7f7_19.png)

该策略的核心在于利用市场波动，通过机械化的多空同时开仓来获取利润。它结构简单，是算法交易的一个良好起点。你可以下载示例代码，尝试调整参数（如网格距离、ATR周期、止盈止损比率）或修改离场逻辑，以找到更适合自己风险偏好的配置。记住，在实盘应用前，务必进行充分的历史回测和模拟盘验证。