# Python金融量化分析：P26：策略效果演示与指标分析 📊

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_1.png)

在本节课中，我们将学习如何对量化策略进行效果演示与指标分析。我们将调整数据处理格式，实现一个简单的股票筛选与调仓策略，并解读回测结果中的关键指标。

## 数据处理格式调整

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_3.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_5.png)

上一节我们介绍了如何筛选股票，本节中我们来看看如何调整数据格式以便于后续处理。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_7.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_9.png)

我们通常希望数据是纵向排列的，即每一行代表一只股票及其财务指标。然而，当前数据是横向排列的，每个股票名称作为一列。

为了调整格式，我们只需要对数据框（DataFrame）执行一个转置操作。以下是实现代码：

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_11.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_13.png)

```python
# 对筛选结果进行转置
transposed_result = original_dataframe.T
print(transposed_result)
```

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_15.png)

转置后的结果如下图所示，数据变为纵向排列，索引是股票名称，后面是其对应的指标值。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_17.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_3.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_19.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_21.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_22.png)

此时，我们只需要获取转置后数据的索引，即股票名称列表。这个列表将作为我们策略的股票池。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_24.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_26.png)

```python
# 获取股票名称列表
stock_list = transposed_result.index.tolist()
# 将股票池存入上下文（context）中，供后续交易函数使用
context.stock_pool = stock_list
```

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_28.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_30.png)

至此，在交易开始前（`before_trading`），我们已经完成了股票的筛选工作，并将目标股票池存储起来。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_32.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_34.png)

## 实现交易逻辑（handle_bar）

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_36.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_38.png)

接下来，我们进入核心的交易逻辑部分。在`handle_bar`函数中，我们需要根据每日的股票池动态调整持仓。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_40.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_42.png)

首先，我们需要判断当前账户是否持有股票。这可以通过查询上下文（`context`）中的持仓信息（`position`）来实现。`position`是一个字典，包含了当前所有持仓的详细信息。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_44.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_46.png)

以下是判断和获取持仓的代码：

```python
# 获取当前持仓信息
current_positions = context.portfolio.positions
```

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_48.png)

### 持仓判断与调仓逻辑

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_50.png)

以下是实现调仓逻辑的步骤：

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_52.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_54.png)

1.  **判断初始状态**：如果当前持仓为空，说明是策略运行的第一天，我们需要买入股票池中的所有股票。
2.  **执行调仓操作**：如果当前持仓不为空，则需要将持仓与最新的股票池进行比较，卖出不在新股票池中的股票，买入新股票池中尚未持有的股票。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_56.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_58.png)

具体实现如下：

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_60.png)

```python
def handle_bar(context, bar_dict):
    # 获取当前持仓和新股票池
    current_positions = context.portfolio.positions
    target_pool = context.stock_pool

    # 如果当前没有持仓，则执行初始建仓
    if not current_positions:
        for stock in target_pool:
            # 平均分配资金买入股票池中的所有股票
            order_target_percent(stock, 1.0 / len(target_pool))
    else:
        # 调仓逻辑：卖出旧池中有但新池中没有的股票
        for stock in current_positions.keys():
            if stock not in target_pool:
                order_target_percent(stock, 0)  # 卖出全部持仓

        # 买入新池中有但当前未持有的股票（或调整持仓至目标比例）
        for stock in target_pool:
            order_target_percent(stock, 1.0 / len(target_pool))
```

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_62.png)

在上面的代码中，`order_target_percent(stock, percent)`是一个重要的交易API函数。它的作用是调整某只股票的持仓，使其市值占总资产的比例达到指定的`percent`。例如：
*   `order_target_percent(‘000001.XSHE’, 0.05)` 表示将平安银行的持仓调整至总资产的5%。
*   `order_target_percent(‘000002.XSHE’, 0)` 表示卖出全部万科A的持仓。

## 策略回测结果分析

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_64.png)

策略编写完成后，我们运行回测，得到如下结果界面：

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_66.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_68.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_74.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_70.png)

回测结果主要包含两部分：收益概况和交易详情。我们重点解读收益概况中的几个核心指标：

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_72.png)

1.  **累计收益率**：策略在整个回测期间的总收益。图中蓝线为策略收益，绿线为基准（如沪深300指数）收益。对比两者可以直观看出策略是否跑赢了市场。
2.  **年化收益率**：将累计收益率折算成年化的形式，便于与其他投资产品比较。
3.  **基准年化收益率**：参照基准（如指数）的年化收益率。
4.  **阿尔法（Alpha）与贝塔（Beta）**：
    *   **贝塔**：衡量策略收益相对于市场基准收益的波动性。贝塔为1表示与市场波动同步；大于1表示波动比市场大；小于1表示波动比市场小。
    *   **阿尔法**：衡量策略的超额收益，即扣除市场波动（贝塔收益）后，由策略自身能力带来的收益。阿尔法越高，说明策略选股或择时能力越强。
5.  **夏普比率（Sharpe Ratio）**：衡量策略承担单位风险所获得的超额回报。**公式**为：`(策略年化收益率 - 无风险利率) / 策略收益的年化波动率`。该比率越高，说明策略的风险调整后收益越好。
6.  **最大回撤（Max Drawdown）**：在选定周期内，资产净值从最高点到最低点的最大跌幅。这是衡量策略下行风险的关键指标，回撤越小，策略控制风险的能力越强。
7.  **收益波动率**：策略收益率的波动程度，即风险。波动率越低，收益曲线越平稳。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_74.png)

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_76.png)

在示例结果中，策略和基准均出现亏损，但策略的累计收益率曲线位于基准上方，表明策略相对基准减少了损失。最大回撤为17.22%，夏普比率为负（因为整体收益为负）。这些指标共同为我们评估策略的收益能力和风险水平提供了量化依据。

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_78.png)

此外，点击“交易详情”可以查看每一天的具体买卖记录、持仓变化和交易成本，用于更细致地分析策略行为。

## 总结

![](img/0b2531e8a6332fb744b6ec8b92fa7b1d_80.png)

本节课中我们一起学习了量化策略从数据处理到回测分析的全过程。我们首先调整了数据格式，然后实现了基于股票池的动态调仓交易逻辑，最后详细解读了回测报告中的关键绩效指标，包括累计收益、年化收益、阿尔法、贝塔、夏普比率和最大回撤等。理解这些指标对于评估和优化量化策略至关重要。