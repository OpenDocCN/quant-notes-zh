# Python金融量化投资：P56：均值回归理论实现 📈

![](img/30e6cbacfef5b5296ca3ff337cda4441_1.png)

## 概述
在本节课中，我们将学习如何将均值回归理论转化为一个可执行的量化选股策略。我们将使用Python实现一个策略框架，该框架通过计算股票价格相对于其移动平均线的偏离程度来筛选股票，并定期调整投资组合。

## 策略框架与参数设置
上一节我们介绍了均值回归的理论基础，本节中我们来看看如何用代码实现它。策略的实现框架与我们之前学习的因子选股框架类似。

首先，我们需要设置一些基础参数。以下是初始化部分的关键代码：

```python
# 设置基准为沪深300指数
benchmark = '000300.XSHG'
# 设置股票池为沪深300成分股
universe = set_universe('000300.XSHG')
# 设置计算均线的天数，例如30天
MA_DAYS = 30
# 设置持仓股票数量
STOCK_NUM = 10
# 设置手续费
commission = Commission(buycost=0.0003, sellcost=0.0013, unit='perValue')
```

代码解释：
*   `benchmark` 定义了策略比较的基准指数。
*   `universe` 定义了策略可以选择的股票范围。
*   `MA_DAYS` 是计算移动平均线（MA）所用的天数，这是一个可调整的核心参数。
*   `STOCK_NUM` 决定了投资组合中持有股票的数量。
*   `commission` 设置了买卖股票的交易成本。

## 策略核心逻辑：选股
策略的核心在于每月执行一次的 `handle_data` 函数。其逻辑是先卖出不在新选股列表中的股票，再买入新选出的股票。关键问题是如何选出新的股票列表（`to_hold`）。

我们的目标是计算股票池中每只股票的“偏离程度”并据此打分。偏离程度的计算公式为：

**偏离程度 = (MA - P) / MA**

其中：
*   **P** 代表股票当前价格。
*   **MA** 代表该股票的移动平均价格。

这个值越大，表示当前价格 **P** 向下偏离移动平均线 **MA** 的程度越大。根据均值回归理论，我们预期这类股票未来上涨（回归均值）的可能性更高。

以下是实现选股逻辑的步骤：

首先，我们创建一个Pandas Series对象来存储每只股票的偏离程度得分。

![](img/30e6cbacfef5b5296ca3ff337cda4441_3.png)

```python
import pandas as pd

![](img/30e6cbacfef5b5296ca3ff337cda4441_5.png)

def handle_data(account):
    # 创建一个Series，索引为股票代码，值初始为空
    score_sr = pd.Series(index=account.universe)

    # 遍历股票池中的每只股票
    for stock in score_sr.index:
        # 计算该股票的N日移动平均线（MA）
        hist_data = account.get_attribute_history(stock, MA_DAYS)
        ma_price = hist_data['close'].mean()

        # 获取该股票的当前价格（P），例如使用当日开盘价
        current_price = account.get_current_data(stock).day_open

        # 计算偏离程度比率
        ratio = (ma_price - current_price) / ma_price

        # 将比率存入Series
        score_sr[stock] = ratio

    # 接下来，我们需要根据得分选出股票
```

![](img/30e6cbacfef5b5296ca3ff337cda4441_7.png)

![](img/30e6cbacfef5b5296ca3ff337cda4441_9.png)

## 筛选与交易执行
在计算出所有股票的偏离程度得分后，我们需要筛选出得分最高的若干只股票进行投资。

以下是筛选和交易执行的步骤：

我们可以使用 `nlargest` 方法快速找出得分最高的N只股票。这个方法在算法效率上通常比先排序再切片要快。

```python
    # 选出偏离程度最大的前STOCK_NUM只股票
    to_hold_stocks = score_sr.nlargest(STOCK_NUM).index.values

    # 卖出当前持有但不在新选股列表中的股票
    for stock in account.valid_secpos:
        if stock not in to_hold_stocks:
            order_to(stock, 0)

    # 买入新选出的股票，并等权重分配资金
    position_per_stock = account.referencePortfolioValue / len(to_hold_stocks)
    for stock in to_hold_stocks:
        order_value(stock, position_per_stock)
```

代码解释：
1.  `score_sr.nlargest(STOCK_NUM)` 选出得分最高的前N只股票，`.index.values` 获取这些股票的代码数组。
2.  遍历当前持仓，卖出不在新股票列表中的股票。
3.  将总资产平均分配给新选出的股票，并执行买入操作。

## 策略优化与思考
运行上述代码后，我们得到了一个基本的均值回归策略。然而，这个初始版本的表现可能只是勉强跑赢大盘，这引出了策略优化的重要性。

一个完整的策略绝不仅仅是几十行代码。以下是一些可以考虑的优化和深入研究方向：

*   **参数调优**：核心参数如 `MA_DAYS`（均线天数）和 `STOCK_NUM`（持仓数量）对策略表现有显著影响。尝试不同的参数组合（如10天、60天均线）进行回测，寻找更优配置。
*   **股票池调整**：将股票池从“沪深300”扩大到“全A股”（代码：`000002.XSHG`），会发现策略表现曲线发生变化，因为可选择的股票范围和基准指数特性都不同了。
*   **增加风险过滤**：当前策略没有考虑股票的基本面风险。例如，偏离程度大的股票可能本身波动性极高或存在退市风险（如ST股票）。可以加入财务指标或风险过滤条件来排除这类股票。
*   **构建多因子模型**：均值回归本身可以看作一个“因子”。可以将其与其他有效因子（如市值因子、动量因子）结合，构建多因子选股模型，可能获得更稳健的收益。
*   **结合择时与风控**：在选股基础上，加入大盘择时判断、个股止盈止损逻辑，可以进一步控制回撤，提升策略风险收益比。

![](img/30e6cbacfef5b5296ca3ff337cda4441_11.png)

## 总结
本节课中我们一起学习了均值回归理论的代码实现。我们构建了一个完整的量化选股策略，其核心是计算价格对移动平均线的偏离度并买入偏离度最大的股票。我们了解到，从一个理论到基本策略实现只是第一步，后续的参数优化、风险控制和多策略融合才是量化投资研究的深水区。