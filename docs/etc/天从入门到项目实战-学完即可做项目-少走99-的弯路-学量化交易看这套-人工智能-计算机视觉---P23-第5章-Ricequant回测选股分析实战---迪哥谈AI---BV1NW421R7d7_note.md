# 量化交易入门：P23：第5章：Ricequant回测选股分析实战 📈

## 概述
在本节课中，我们将学习如何在Ricequant平台上构建一个简单的量化交易策略。我们将通过一个具体案例——从沪深300指数中动态筛选并持有表现最佳的10只股票——来熟悉平台的核心API和策略开发流程。学完本节，你将掌握策略初始化、数据获取、信号生成和交易执行的基本方法。

![](img/50d8f42beadbca615cf215b302036f53_1.png)

![](img/50d8f42beadbca615cf215b302036f53_2.png)

## 策略目标与设计思路
上一节我们介绍了量化交易的基本概念，本节中我们来看看如何将其落地为一个具体的策略。

我们的目标是构建一个策略，该策略始终持有沪深300指数中“表现最好”的10只股票。这里的“表现”可以基于各种财务指标，例如净利润。策略的核心逻辑是定期（如每日）重新评估所有股票，根据选定的指标进行排序，并调整持仓，使其始终与排名前10的股票保持一致。

为了实现这个目标，我们需要完成以下三个核心步骤：
1.  **初始化**：设定股票池（沪深300成分股）。
2.  **每日盘前处理**：获取股票池中所有股票的财务数据，根据选定指标排序，选出前10名。
3.  **每日交易处理**：对比当前持仓与计算出的“最佳10股”名单，卖出不在名单中的股票，买入名单中尚未持有的股票。

接下来，我们将分步实现这个策略。

## 第一步：策略初始化
在Ricequant平台中，策略的初始化工作在 `init` 函数中完成。这里我们需要定义策略的全局变量和初始设置。

以下是初始化步骤：
*   点击平台上的“新建策略”按钮。
*   为策略命名，例如 `simple_strategy`。
*   在生成的代码框架中，找到 `init` 函数。

在 `init` 函数中，我们的首要任务是获取沪深300指数的成分股作为我们的股票池。这可以通过Ricequant的API `index_components` 来实现。

```python
def init(context):
    # 获取沪深300指数成分股，作为我们的股票池
    context.hs300 = index_components('000300.XSHG')
    # 我们可以打印一下看看数量是否正确
    logger.info("沪深300成分股数量: " + str(len(context.hs300)))
```

## 第二步：盘前数据准备与选股
在每天交易开始前，我们需要执行数据获取和计算工作。这部分逻辑应放在 `before_trading` 函数中，该函数在每个交易日早上9点（或设定的时间）自动执行。

在 `before_trading` 函数中，我们需要：
1.  从 `context.hs300` 股票池中获取我们关心的财务指标数据（例如，净利润）。
2.  对这些股票按指标值进行降序排序。
3.  选取排名前10的股票代码，并存储起来，供当日的交易逻辑使用。

Ricequant提供了 `get_fundamentals` 函数来查询财务数据。

```python
def before_trading(context):
    # 查询股票池中所有股票的最新净利润数据
    # 这里以查询季度报告（‘q’）中的净利润（‘net_profit’）为例
    fundamental_df = get_fundamentals(
        query(fundamentals.eod_derivative_indicator.net_profit).filter(
            fundamentals.eod_derivative_indicator.stockcode.in_(context.hs300)
        )
    )
    
    # 将数据按净利润从大到小排序
    sorted_df = fundamental_df.sort_values(by='net_profit', ascending=False)
    
    # 获取净利润最高的前10只股票的代码列表
    context.top_10_stocks = sorted_df.head(10)['stockcode'].tolist()
    
    # 记录今日选出的股票
    logger.info("今日选出的Top 10股票: " + str(context.top_10_stocks))
```

**注意**：实际应用中，财务指标的选择和计算可能更复杂，例如使用市盈率(PE)、市净率(PB)或自定义的复合指标。这里使用净利润仅作演示。

## 第三步：交易逻辑执行
盘前准备工作完成后，就到了每日的核心交易时段。交易逻辑写在 `handle_bar` 函数中，该函数在每根K线（如每日）结束时被调用。

在 `handle_bar` 函数中，我们需要：
1.  获取当前的持仓情况。
2.  对比当前持仓的股票列表与 `context.top_10_stocks`（今日最佳股票列表）。
3.  执行调仓操作：
    *   **卖出**：当前持有但不在 `top_10_stocks` 列表中的股票。
    *   **买入**：在 `top_10_stocks` 列表中但当前未持有的股票。

为了简化，我们假设将资金平均分配到这10只股票上。

```python
def handle_bar(context, bar_dict):
    # 1. 获取当前持仓的所有股票
    current_positions = list(context.portfolio.positions.keys())
    
    # 2. 确定需要卖出和买入的股票
    # 需要卖出的股票：当前持有，但不在今日Top10名单中
    stocks_to_sell = [s for s in current_positions if s not in context.top_10_stocks]
    # 需要买入的股票：在今日Top10名单中，但当前未持有
    stocks_to_buy = [s for s in context.top_10_stocks if s not in current_positions]
    
    # 3. 执行卖出操作
    for stock in stocks_to_sell:
        # order_target_percent 函数可以将目标股票的仓位调整到指定比例
        # 这里设置为0，即全部卖出
        order_target_percent(stock, 0)
        logger.info("卖出股票: " + stock)
    
    # 4. 执行买入操作
    # 计算每只目标股票应分配的资金比例 (平均分配)
    if len(context.top_10_stocks) > 0:
        weight = 1.0 / len(context.top_10_stocks)
        for stock in stocks_to_buy:
            order_target_percent(stock, weight)
            logger.info("买入股票: " + stock)
```

## 总结
本节课中我们一起学习了如何在Ricequant平台上从零开始构建一个完整的量化选股策略。我们首先在 `init` 函数中定义了策略的股票池（沪深300）。接着，在 `before_trading` 函数中完成了每日的数据获取和选股逻辑，筛选出财务指标最佳的10只股票。最后，在 `handle_bar` 函数中实现了核心的交易逻辑，通过对比持仓与目标股票列表，自动完成调仓操作。

![](img/50d8f42beadbca615cf215b302036f53_4.png)

![](img/50d8f42beadbca615cf215b302036f53_5.png)

这个策略虽然简单，但涵盖了量化策略开发的核心流程：**数据准备 -> 信号生成 -> 交易执行**。你可以在此基础上进行扩展，例如尝试不同的选股指标、加入止损止盈逻辑、或者优化资金分配方案，从而构建更复杂、更强大的交易系统。