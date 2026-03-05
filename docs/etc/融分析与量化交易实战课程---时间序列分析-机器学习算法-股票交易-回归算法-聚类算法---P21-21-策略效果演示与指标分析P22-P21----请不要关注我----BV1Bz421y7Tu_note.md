# 量化交易实战课程：P21：策略效果演示与指标分析

![](img/b703c490cd773a8a826bdc900b8eaaac_0.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_2.png)

## 概述
在本节课中，我们将学习如何对量化策略进行回测，并分析其效果。我们将重点讲解如何调整数据结构、实现买卖逻辑，并解读回测报告中的关键指标。

![](img/b703c490cd773a8a826bdc900b8eaaac_4.png)

---

![](img/b703c490cd773a8a826bdc900b8eaaac_6.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_8.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_10.png)

上一节我们介绍了如何筛选股票池，本节中我们来看看如何基于这个股票池构建一个完整的交易策略，并分析其回测效果。

![](img/b703c490cd773a8a826bdc900b8eaaac_12.png)

### 数据结构的调整
在筛选出股票后，我们通常希望以更直观的方式查看数据。原始数据可能是横向排列的，但我们更习惯纵向查看每只股票的详细信息。

![](img/b703c490cd773a8a826bdc900b8eaaac_14.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_16.png)

以下是调整数据展示方式的步骤：
1.  对存储股票数据的 `DataFrame` 进行转置操作。
2.  转置后，数据变为纵向排列，每一行代表一只股票及其对应的财务指标。
3.  我们只需要获取股票的索引（即股票代码或名称）作为最终结果。

![](img/b703c490cd773a8a826bdc900b8eaaac_18.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_20.png)

核心操作是使用 `DataFrame` 的 `.T` 属性进行转置：
```python
transposed_df = original_df.T
```
转置后，我们可以方便地获取股票列表：
```python
selected_stocks = transposed_df.index.tolist()
```
这样，我们就得到了一个包含筛选后股票代码的列表，可以将其存储在策略的上下文（`context`）中，供后续交易逻辑使用。

![](img/b703c490cd773a8a826bdc900b8eaaac_22.png)

---

![](img/b703c490cd773a8a826bdc900b8eaaac_24.png)

### 实现交易逻辑
在 `handle_bar` 函数中，我们需要实现具体的买卖决策。逻辑的核心是：每日检查当前持仓，卖出不在最新股票池中的股票，并买入股票池中尚未持有的股票。

![](img/b703c490cd773a8a826bdc900b8eaaac_26.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_28.png)

以下是实现交易逻辑的步骤：

首先，我们需要判断当前是否有持仓。可以通过 `context.portfolio.positions` 来获取，这是一个包含所有持仓信息的字典。

![](img/b703c490cd773a8a826bdc900b8eaaac_30.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_32.png)

```python
# 获取当前持仓
current_positions = context.portfolio.positions
```

![](img/b703c490cd773a8a826bdc900b8eaaac_34.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_36.png)

接下来，我们根据持仓情况进行操作：
1.  **卖出逻辑**：遍历当前持仓，如果某只股票不在最新的股票池中，则将其全部卖出。
2.  **买入逻辑**：遍历最新的股票池，如果某只股票当前未持有，则进行买入。

![](img/b703c490cd773a8a826bdc900b8eaaac_38.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_40.png)

这里使用 `order_target_percent` 函数进行交易，它可以按目标市值占总资产的比例来下单。

![](img/b703c490cd773a8a826bdc900b8eaaac_42.png)

```python
# 卖出不在股票池中的股票
for stock in current_positions.keys():
    if stock not in selected_stocks:
        # 卖出全部持仓，即目标比例为0
        order_target_percent(stock, 0)

# 买入股票池中的股票（平均分配资金）
for stock in selected_stocks:
    # 买入，使该股票持仓占总资产的比例为 1/股票池数量
    order_target_percent(stock, 1.0 / len(selected_stocks))
```

![](img/b703c490cd773a8a826bdc900b8eaaac_44.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_46.png)

---

![](img/b703c490cd773a8a826bdc900b8eaaac_48.png)

### 策略回测与指标解读
运行策略后，我们会得到一份回测报告。报告包含了评估策略性能的关键指标。

![](img/b703c490cd773a8a826bdc900b8eaaac_50.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_52.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_54.png)

以下是几个核心指标的解读：
*   **回测年化收益**：你的策略在回测期间的年化收益率。
*   **基准年化收益**（如沪深300指数）：同期市场基准的年化收益率。对比两者可以判断策略是否跑赢大盘。
*   **最大回撤**：策略净值从高点回落至低点的最大幅度。**这个指标非常重要**，它衡量了策略可能面临的最大亏损风险。
*   **夏普比率**：衡量每承担一单位风险所获得的超额回报。通常为正数且越高越好，若收益为负，该指标也可能为负。

![](img/b703c490cd773a8a826bdc900b8eaaac_56.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_58.png)

![](img/b703c490cd773a8a826bdc900b8eaaac_60.png)

初步回测结果可能不理想（例如出现亏损），这很正常。策略效果受测试时间段、参数设置等多种因素影响。我们需要通过分析这些指标，不断迭代和优化策略逻辑。

![](img/b703c490cd773a8a826bdc900b8eaaac_62.png)

---

## 总结
本节课中我们一起学习了量化策略的完整实现流程。我们从调整数据展示开始，实现了基于动态股票池的每日调仓逻辑，包括卖出旧仓位和买入新仓位。最后，我们运行回测并学习了如何解读年化收益、最大回撤、夏普比率等关键绩效指标，为后续的策略优化奠定了基础。