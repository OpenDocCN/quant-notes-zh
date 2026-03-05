# 基于Python的股票分析与量化交易入门到实践 - P33：9.1 股市投资第一步：Python基本面量化选股概况 📈

在本节课中，我们将要学习量化选股的基本概念、核心注意事项，并通过一个实战案例来理解如何利用Python进行基本面量化选股。

## 概述

量化选股是利用数量化的方法选择股票组合，期望该组合能够获得超越市场的超额收益。它主要分为技术面选股和基本面选股两大类。本章节我们将重点介绍基本面量化选股。

## 量化选股基本概况

![](img/484c8f8ee43f8adf452d1a3da1989819_1.png)

量化选股，顾名思义，就是利用数量化的方法选择股票组合。其核心目标是构建一个能够获得超额收益的股票投资组合。

量化选股一般分为两类：
1.  **技术面选股**：通过各种技术理论和技术指标（如均线、MACD等）来分析和预测股票的未来价格趋势。这部分知识体系繁杂，对金融和数学知识要求较高，本章暂不深入讨论。
2.  **基本面选股**：通过对上市公司的外部环境（如行业前景）和自身因素（如财务指标、发展潜力）进行分析，并将这些因素量化，从而筛选出有潜力的股票组合。本章我们将重点探讨这种方法。

## 量化选股的注意事项 ⚠️

在开始实践之前，了解量化选股的一些基本原则至关重要。以下是几个核心注意事项：

*   **分散投资**：这是最基本的原则，即“鸡蛋不要放在一个篮子里”。量化选股的目标是构建一个**股票组合**，而非押注单只股票，必须减少单股重仓的情况。
*   **全面研究**：不能仅依赖一两个指标就做出投资决策。需要综合多个维度的指标进行分析，增强选股逻辑的支撑。
*   **主动调仓**：量化策略应能根据市场变化和算法信号，主动进行仓位轮动和调整，而非买入后被动持有。
*   **正确认识工具**：量化选股（及量化交易）是**提高投资胜率的工具之一**，而非稳赚不赔的“圣杯”。任何策略都有盈利和亏损的可能，必须做好风险管理。

## 实战：基于基本面的白马股选股策略 💻

上一节我们介绍了量化选股的概况和注意事项，本节中我们来看看如何通过代码实现一个经典的基本面选股策略——寻找“白马股”。

白马股通常指那些市值大、业绩优良、回报率高、流通性好的股票。我们的策略将基于以下几个量化条件进行筛选和排名：

**筛选条件：**
1.  总市值 > 50亿元（市值大的公司流动性好，竞争力强）。
2.  上市天数 > 750个交易日（约3年，证明公司经营稳定）。
3.  流通盘比例 > 95%（减少限售股解禁带来的股价大幅波动影响）。
4.  销售毛利率 > 20%（盈利能力强的表现）。
5.  扣除非经常性损益后的净资产收益率（ROE）> 20%（衡量公司真实盈利能力的核心指标）。

**排名条件：**
将符合上述条件的股票，按照**总市值从大到小**进行排序。

以下是该策略在聚宽平台上的核心代码实现框架：

```python
# 初始化函数，设置基准、交易成本等参数
def initialize(context):
    # 设定沪深300指数为基准
    set_benchmark('000300.XSHG')
    # 开启动态复权模式
    set_option('use_real_price', True)
    # 设置成交量比例
    set_option('order_volume_ratio', 1)
    # 设置交易费用（佣金万三，印花税千一，最低5元）
    set_order_cost(OrderCost(open_tax=0, close_tax=0.001,
                             open_commission=0.0003, close_commission=0.0003,
                             close_today_commission=0, min_commission=5),
                   type='stock')
    # 全局参数：持仓股票数量上限、调仓周期
    g.stock_num = 20
    g.refresh_rate = 100
    # 每日运行交易函数
    run_daily(trade, time='every_bar')

# 核心选股函数：根据基本面条件筛选股票
def check_stocks(context):
    # 使用query查询财务数据
    q = query(
        valuation.code, valuation.market_cap,  # 总市值
        valuation.circulating_cap,  # 流通市值
        indicator.gross_profit_margin,  # 销售毛利率
        indicator.roe  # 净资产收益率
    ).filter(
        valuation.market_cap > 50e8,  # 条件1：总市值>50亿
        valuation.circulating_cap / valuation.market_cap > 0.95,  # 条件3：流通盘比例>95%
        indicator.gross_profit_margin > 20,  # 条件4：销售毛利率>20%
        indicator.roe > 20  # 条件5：ROE>20%
    ).order_by(
        valuation.market_cap.desc()  # 按总市值降序排列
    ).limit(
        100  # 先取前100名，后续再过滤
    )
    # 获取查询结果
    df = get_fundamentals(q, date=context.current_dt)
    stock_list = list(df['code'])

    # 过滤上市时间不足750天的次新股
    stock_list = exclude_new_stocks(stock_list, context.current_dt, 750)
    # 过滤当日停牌的股票
    stock_list = filter_paused_stocks(stock_list, context.current_dt)

    # 返回最终候选股票列表（前g.stock_num只）
    return stock_list[:g.stock_num]

# 辅助函数：过滤停牌股票
def filter_paused_stocks(stock_list, current_dt):
    current_data = get_current_data()
    return [s for s in stock_list if not current_data[s].paused]

# 辅助函数：排除次新股
def exclude_new_stocks(stock_list, current_dt, days):
    stock_info = get_all_securities(types=['stock'], date=current_dt)
    delta = datetime.timedelta(days=days)
    return [s for s in stock_list if (current_dt - stock_info.loc[s, 'start_date']) > delta]

# 交易函数：执行调仓逻辑
def trade(context):
    # 判断是否到达调仓日
    if context.current_dt.date().weekday() != 0:  # 示例：仅在周一调仓
        return

    # 获取当前持仓列表和新的选股列表
    current_holdings = list(context.portfolio.positions.keys())
    new_stock_list = check_stocks(context)

    # 卖出不在新选股列表中的持仓
    for stock in current_holdings:
        if stock not in new_stock_list:
            order_target_value(stock, 0)

    # 计算可用资金，等权重买入新选股列表中的股票
    cash = context.portfolio.available_cash
    position_value_per_stock = cash / len(new_stock_list) if new_stock_list else 0

    for stock in new_stock_list:
        if stock not in current_holdings and position_value_per_stock > 0:
            order_target_value(stock, position_value_per_stock)
```

![](img/484c8f8ee43f8adf452d1a3da1989819_3.png)

**策略回测结果分析：**
假设从2015年7月1日到2020年7月1日，以10万元初始资金运行该策略（每100个交易日调仓一次），其年化收益率可达20%以上，显著跑赢了同期沪深300指数的基准收益。这证明了该基本面量化选股策略在特定历史阶段的有效性。**但必须强调，历史表现不代表未来，任何策略都无法保证稳赚不赔。**

## 总结

本节课中我们一起学习了量化选股的核心知识。我们首先了解了量化选股分为**技术面选股**和**基本面选股**，并明确了本章重点在于后者。接着，我们探讨了量化选股的四大注意事项：**分散投资、全面研究、主动调仓以及正确认识其工具属性**。

最后，我们通过一个寻找“白马股”的实战案例，完整演示了如何利用Python和聚宽平台实现一个基本面量化选股策略。该策略基于五个核心财务指标（**总市值、上市时间、流通盘比例、销售毛利率、ROE**）进行筛选和排序。回测结果显示，该策略在2015-2020年间取得了不错的超额收益。

![](img/484c8f8ee43f8adf452d1a3da1989819_5.png)

记住，量化选股是提高投资决策效率和系统性的有力工具，但成功的投资永远需要结合严谨的逻辑、充分的风险管理和对市场的深刻理解。