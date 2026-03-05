# 量化交易教程：P26：策略效果演示与指标分析 📊

![](img/d4ffe9bb184eb67f07e61674b28f4394_1.png)

在本节课中，我们将学习如何对量化策略进行回测，并分析其关键性能指标。我们将通过一个具体的例子，演示如何调整数据结构、执行买卖逻辑，并最终解读回测报告。

![](img/d4ffe9bb184eb67f07e61674b28f4394_3.png)

---

![](img/d4ffe9bb184eb67f07e61674b28f4394_5.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_6.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_7.png)

上一节我们介绍了策略的初始化与数据筛选逻辑。本节中，我们来看看如何执行交易逻辑并分析策略的最终效果。

## 数据结构调整 🔄

![](img/d4ffe9bb184eb67f07e61674b28f4394_9.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_10.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_12.png)

在 `before_trading` 函数中，我们筛选出的股票数据默认是横向排列的（每列一个股票）。但通常我们更习惯纵向查看数据（每行一个股票及其指标）。

![](img/d4ffe9bb184eb67f07e61674b28f4394_14.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_16.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_17.png)

以下是调整数据结构的步骤：

![](img/d4ffe9bb184eb67f07e61674b28f4394_18.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_20.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_22.png)

1.  对原始的 `DataFrame` 使用 `.T` 属性进行转置操作。
2.  转置后，数据变为纵向排列，索引（`index`）即为股票名称。
3.  我们的目标是在交易开始前，获取筛选出的股票列表。

![](img/d4ffe9bb184eb67f07e61674b28f4394_24.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_25.png)

核心操作代码如下：
```python
# 假设 df 是筛选后的原始DataFrame
df_transposed = df.T  # 进行转置
selected_stocks = df_transposed.index.tolist()  # 获取股票名称列表
# 将股票列表存入上下文，供后续交易函数使用
context.stock_pool = selected_stocks
```

![](img/d4ffe9bb184eb67f07e61674b28f4394_27.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_28.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_29.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_31.png)

完成转置并提取股票列表后，我们的准备工作就完成了。

![](img/d4ffe9bb184eb67f07e61674b28f4394_33.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_34.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_35.png)

## 交易逻辑实现 💹

![](img/d4ffe9bb184eb67f07e61674b28f4394_36.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_38.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_39.png)

接下来，我们进入 `handle_bar` 函数，实现每日的交易逻辑。核心思路是：对比当前持仓与最新股票池，卖出不在池中的股票，买入池中新增的股票。

![](img/d4ffe9bb184eb67f07e61674b28f4394_40.png)

首先，我们需要判断当前是否有持仓。可以通过 `context.portfolio.positions` 来获取，这是一个字典，包含了所有持仓信息。

![](img/d4ffe9bb184eb67f07e61674b28f4394_42.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_43.png)

以下是交易逻辑的具体步骤：

![](img/d4ffe9bb184eb67f07e61674b28f4394_45.png)

1.  **判断持仓状态**：检查 `context.portfolio.positions` 是否为空。
2.  **卖出逻辑**：如果持仓不为空，则遍历当前所有持仓股票。若某只股票不在最新的 `stock_pool` 中，则将其全部卖出。
3.  **买入逻辑**：遍历最新的 `stock_pool`。对于池中的每只股票，进行买入操作。为了简化，我们采用平均买入的策略。

![](img/d4ffe9bb184eb67f07e61674b28f4394_46.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_47.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_48.png)

关键API `order_target_percent` 用于调整持仓至目标比例。其参数为：
*   `id_or_ins`: 交易标的。
*   `percent`: 目标持仓占总资产的比例（0为清空，1为满仓）。

![](img/d4ffe9bb184eb67f07e61674b28f4394_50.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_51.png)

实现代码如下：
```python
def handle_bar(context):
    # 获取当前持仓
    current_positions = context.portfolio.positions
    # 获取最新股票池
    stock_pool = context.stock_pool

    # 卖出不在股票池中的持仓
    for stock in current_positions.keys():
        if stock not in stock_pool:
            # 卖出该股票，目标比例设为0（即全部卖出）
            order_target_percent(stock, 0)

    # 买入股票池中的股票（平均分配资金）
    if len(stock_pool) > 0:
        # 计算每只股票应占的资金比例
        weight = 1.0 / len(stock_pool)
        for stock in stock_pool:
            # 买入该股票，目标比例为计算出的权重
            order_target_percent(stock, weight)
```

![](img/d4ffe9bb184eb67f07e61674b28f4394_53.png)

## 回测结果分析 📈

运行策略后，我们会得到一份回测报告。报告主要包含两部分：**收益概况**和**交易详情**。

![](img/d4ffe9bb184eb67f07e61674b28f4394_55.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_56.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_57.png)

**收益概况**中的关键指标包括：
*   **策略收益率 vs 基准收益率**：对比策略收益与市场指数（如沪深300）的收益，观察策略是否跑赢大盘。
*   **年化收益率**：将策略总收益折算为年化收益，便于比较。
*   **最大回撤**：策略净值从高点回落至最低点的最大幅度，衡量策略的风险。
*   **夏普比率**：衡量单位风险所获得的超额回报，数值越高越好。公式为 `(策略年化收益率 - 无风险利率) / 策略收益波动率`。

![](img/d4ffe9bb184eb67f07e61674b28f4394_59.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_60.png)

**交易详情**则记录了每一天的具体交易操作、持仓变化和费用情况，用于深入分析策略的微观行为。

![](img/d4ffe9bb184eb67f07e61674b28f4394_62.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_64.png)

![](img/d4ffe9bb184eb67f07e61674b28f4394_65.png)

在本例的回测中，策略在所选时间段内可能并未获得正收益，但通过指标对比（如最大回撤小于基准），可以评估策略在控制风险方面的表现。

---

![](img/d4ffe9bb184eb67f07e61674b28f4394_67.png)

本节课中我们一起学习了量化策略回测的完整流程：从调整数据格式、实现基于股票池的每日调仓逻辑，到最终解读包含收益率、回撤、夏普比率等关键指标的回测报告。理解这些步骤和指标是评估和优化策略的基础。