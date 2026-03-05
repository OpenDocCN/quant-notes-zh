# 量化投资策略：P62：羊驼交易法则 🦙

![](img/d294534302534e99fde96d4d027c9a65_1.png)

在本节课中，我们将学习一个名为“羊驼交易法则”的量化交易策略。这个策略最初源于一个随机选股的噱头，但我们将对其进行改进，使其更具逻辑性。我们将探讨其原始思想、实现方法，以及如何通过引入反转策略的概念来优化它，并最终用Python代码实现。

## 策略起源与核心思想

羊驼交易法则的起源颇具趣味性。最初，有国外电视台做了一个实验，让猩猩随机选择股票，并定期淘汰表现最差的几只，再随机补充新的股票。实验声称其收益超过了部分华尔街专家。后来，国内也有电视台用羊驼进行了类似的实验，因此得名。

该策略的核心思想可以概括为八个字：**随机初选股，周期调仓**。其运作模式模拟了“物竞天择，适者生存”的进化论：
1.  初始时，随机买入N支股票，构建一个投资组合。
2.  在每个调仓日（例如每月），卖出组合中收益率最差的M支股票。
3.  同时，再随机买入M支新的股票。

这样，经过长期迭代，理论上组合中会留下“优胜”的股票。然而，纯粹的随机性使得策略效果不稳定且难以复现。

![](img/d294534302534e99fde96d4d027c9a65_3.png)

## 策略改进：引入反转效应

上一节我们介绍了原始的、基于随机性的羊驼法则。本节中我们来看看如何改进它。纯粹的随机选股缺乏逻辑支撑，因此我们引入“反转效应”来指导选股。

![](img/d294534302534e99fde96d4d027c9a65_5.png)

**反转效应**是指：过去一段时间内表现较差的股票，在未来一段时间内可能会有更好的表现（即“超跌反弹”）。反之，过去表现过好的股票则可能回调。

因此，我们对羊驼法则进行如下关键改进：
*   **买入逻辑**：不再随机买入。初始买入时，选择过去一段时间内收益率**最差**的N支股票（期待其反弹）。
*   **调仓逻辑**：在每个调仓日，卖出当前持有的N支股票中，在最近一个调仓周期内表现**最差**的M支。然后，从全市场股票池中，选择当前周期内表现**最差**的M支（且不在现有持仓中）买入。

这样，策略就转变为：**始终持有近期表现不佳的股票，并定期淘汰持仓中持续表现不佳的个体，补充新的“潜在反弹”股。**

## 策略实现代码

理解了改进后的策略逻辑后，我们来看看如何用Python代码实现它。以下代码基于量化平台编写，核心是`handle_data`函数。

首先，我们需要设置策略参数和初始化状态。

```python
# 策略初始化函数，只在策略开始时运行一次
def initialize(context):
    # 设置股票池，例如沪深300成分股
    g.security = get_index_stocks('000300.XSHG')
    # 设置回看收益率的时间窗口（单位：天）
    g.period = 20
    # 设置持仓股票数量
    g.N = 10
    # 设置每次调仓买卖的股票数量
    g.M = 1
    # 初始化标志，用于区分首次建仓和后续调仓
    g.init = True
    # 设置调仓周期（每20个交易日运行一次）
    run_monthly(rebalance, monthday=1, time='open')
```

接下来是核心的交易逻辑函数。我们封装了一个辅助函数`get_sorted_stocks`用于按收益率排序。

```python
# 辅助函数：根据过去period天的收益率对股票池进行排序（从小到大，收益最差的排前面）
def get_sorted_stocks(security_list, period, context):
    df = get_price(security_list, count=period+1, end_date=context.current_dt, fields='close', skip_paused=True)
    returns = (df.iloc[-1] / df.iloc[0]) - 1  # 计算period日收益率
    returns = returns.sort_values()  # 按收益率升序排列
    return returns.index.tolist()  # 返回排序后的股票代码列表

# 核心调仓函数
def rebalance(context):
    # 获取当前时间
    current_dt = context.current_dt

    if g.init:
        # === 首次建仓 ===
        # 获取按收益率排序的股票列表（收益最差的在前）
        sorted_stocks = get_sorted_stocks(g.security, g.period, context)
        # 选择最差的N支股票作为初始持仓
        to_hold = sorted_stocks[:g.N]
        # 计算总资金的90%用于投资（留有余地）
        cash_per_stock = context.portfolio.total_value * 0.9 / g.N

        for stock in to_hold:
            # 为目标股票下单，买入金额为cash_per_stock
            order_value(stock, cash_per_stock)
            log.info(f"初始买入 {stock}")

        # 将初始化标志设为False，之后进入常规调仓逻辑
        g.init = False

    else:
        # === 定期调仓 ===
        # 1. 卖出当前持仓中表现最差的M支股票
        # 获取当前持仓的所有股票
        current_holdings = list(context.portfolio.positions.keys())
        if len(current_holdings) > 0:
            # 对当前持仓股票按近期收益率排序
            sorted_holdings = get_sorted_stocks(current_holdings, g.period, context)
            # 选择持仓中表现最差的M支卖出
            to_sell = sorted_holdings[:g.M]
            for stock in to_sell:
                order_target(stock, 0)  # 卖出全部持仓
                log.info(f"调仓卖出 {stock}")

        # 2. 买入全市场股票池中表现最差的M支（且不在当前持仓中）
        # 获取全市场股票的排序列表
        all_sorted = get_sorted_stocks(g.security, g.period, context)
        cash_to_use = context.portfolio.cash
        bought_count = 0

        for stock in all_sorted:
            # 如果股票不在当前持仓中，则买入
            if stock not in context.portfolio.positions:
                order_value(stock, cash_to_use / g.M)
                log.info(f"调仓买入 {stock}")
                bought_count += 1
                # 买够M支就停止
                if bought_count >= g.M:
                    break
```

以下是代码关键步骤的说明：

![](img/d294534302534e99fde96d4d027c9a65_7.png)

1.  **初始化 (`initialize`)**：设定股票池、回看周期`period`、持仓数`N`、调仓数`M`。`run_monthly`确保策略每月调仓一次。
2.  **收益率排序 (`get_sorted_stocks`)**：这是一个工具函数，计算指定股票列表在`period`天数内的收益率，并返回按收益率从低到高（即从最差到最好）排序的列表。
3.  **首次建仓**：当标志`g.init`为`True`时，执行首次建仓逻辑。买入全市场中最差的`N`支股票。
4.  **定期调仓**：
    *   **卖出**：对当前持仓的股票按近期收益率排序，卖出其中最差的`M`支。
    *   **买入**：对全市场股票按近期收益率排序，买入其中最差的`M`支（且要求不在现有持仓中，以避免重复）。

![](img/d294534302534e99fde96d4d027c9a65_9.png)

## 策略总结与延伸

本节课中我们一起学习了羊驼交易法则及其改进版本。我们从其充满噱头的起源开始，分析了原始策略“随机初选股，周期调仓”的核心理念及其缺陷。为了提高策略的逻辑性和效果，我们引入了**反转效应**，将随机选股改为**买入近期表现最差的股票**，从而得到了一个改进的、有理论依据的策略版本。最后，我们详细解析了该策略的Python实现代码。

需要强调的是，量化策略的世界远不止于此。羊驼法则的改进过程本身也说明，**现有的策略都可以根据你的理解进行修改和优化**。例如，调整参数（`N`、`M`、`period`）、结合其他指标（如成交量、波动率），或者融入更复杂的模型（如机器学习），都可能产生不同的效果。

此外，市场上还存在许多其他经典策略，如海龟交易法则、鳄鱼交易法则，以及基于多因子模型（如Fama-French三因子、五因子模型）的策略。这些策略通常需要更深入的金融知识作为基础。

对于初学者而言，掌握将策略思想转化为代码的能力是关键的第一步。你可以尝试在量化平台上复现并回测各种想法，即使是从简单的均线交叉开始。通过不断实践、分析和迭代，你将逐步积累经验，形成自己的交易逻辑。

---

![](img/d294534302534e99fde96d4d027c9a65_11.png)

**免责声明**：本教程所有内容仅供学习与交流之用，不构成任何投资建议。金融市场存在风险，量化策略的回测结果不代表未来表现，请谨慎决策。