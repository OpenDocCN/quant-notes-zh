# Python金融量化投资：P50：双均线策略实现 🐍📈

在本节课中，我们将学习如何用Python实现一个经典的双均线交易策略。我们将从策略逻辑讲起，逐步完成代码编写，并学习如何在量化平台上回测和可视化策略表现。

## 策略初始化与基础设置

上一节我们介绍了双均线策略的基本概念，本节中我们来看看如何在代码中实现它。首先，我们需要初始化策略并设置一些基础参数。

```python
def initialize(context):
    # 设定基准为沪深300指数
    set_benchmark('000300.XSHG')
    # 设置使用真实价格（复权）
    set_option('use_real_price', True)
    # 设置交易手续费
    set_order_cost(OrderCost(open_tax=0, close_tax=0.001, open_commission=0.0003, close_commission=0.0003, close_today_commission=0, min_commission=5), type='stock')
```

以上三行代码是策略的固定初始化步骤。`set_benchmark`用于设定比较基准，通常选择大盘指数。`set_option`确保我们使用复权后的价格进行计算，以避免分红、拆股等因素的影响。`set_order_cost`则设定了交易成本，包括印花税和佣金。

## 定义交易标的与策略逻辑

我们的双均线策略暂时不考虑选股，因此只针对单只股票进行操作。以下是定义交易标的和策略核心参数的代码。

```python
    # 定义交易股票，这里以中国平安为例
    g.security = ['601318.XSHG']
    # 定义均线周期：短期5日，长期10日
    g.p1 = 5
    g.p2 = 10
```

我们选择中国平安（`601318.XSHG`）作为交易标的，并定义短期均线周期为5天（`g.p1`），长期均线周期为10天（`g.p2`）。虽然目前只操作一只股票，但我们仍将其放入列表，为将来扩展为多股票策略预留结构。

## 每日交易处理函数

![](img/de1820cfc93814614dbeb5e613e660b3_1.png)

策略的核心逻辑在`handle_data`函数中执行。该函数在每个交易日被调用，我们需要在其中计算均线、判断买卖信号并执行交易。

以下是`handle_data`函数的整体框架和获取历史数据的部分：

```python
def handle_data(context, data):
    # 遍历股票池（当前只有一只股票）
    for stock in g.security:
        # 获取过去g.p2天的历史数据，用于计算均线
        df = attribute_history(stock, count=g.p2, fields=['close'], skip_paused=True, df=True)
```

我们使用`attribute_history`函数获取股票的历史收盘价数据。参数`count=g.p2`表示获取`g.p2`天（这里是10天）的数据，`fields=['close']`指定只获取收盘价，`skip_paused=True`会自动跳过停牌日，`df=True`确保返回DataFrame格式，便于后续计算。

![](img/de1820cfc93814614dbeb5e613e660b3_3.png)

## 计算均线与判断买卖信号

获取数据后，下一步是计算短期和长期均线的值，并据此判断是出现“金叉”（买入信号）还是“死叉”（卖出信号）。

以下是计算均线和判断信号的核心代码：

```python
        # 计算长期均线（10日）和短期均线（5日）的值
        ma_long = df['close'][-g.p2:].mean()  # 10日均线
        ma_short = df['close'][-g.p1:].mean() # 5日均线

        # 判断当前持仓状态
        cur_position = context.portfolio.positions[stock].total_amount if stock in context.portfolio.positions else 0

        # 死叉判断：短期均线下穿长期均线，且当前持有股票
        if ma_short < ma_long and cur_position > 0:
            # 卖出全部持仓
            order_target(stock, 0)
            log.info('死叉，卖出股票：', stock)

        # 金叉判断：短期均线上穿长期均线，且当前未持有股票
        elif ma_short > ma_long and cur_position == 0:
            # 用可用资金的80%买入股票
            cash = context.portfolio.available_cash
            order_value(stock, cash * 0.8)
            log.info('金叉，买入股票：', stock)
```

**金叉**的定义是：短期均线从下方上穿长期均线。由于上穿后短期均线会持续高于长期均线，我们只需在**短期均线大于长期均线且当前未持仓**时判定为金叉买入点。
**死叉**的定义是：短期均线从上方下穿长期均线。我们只需在**短期均线小于长期均线且当前持有股票**时判定为死叉卖出点。

这种判断方法避免了计算整个均线序列和寻找交叉点的复杂操作。

## 策略回测与参数优化

![](img/de1820cfc93814614dbeb5e613e660b3_5.png)

代码编写完成后，我们可以选择时间段进行回测，检验策略表现。回测时，可以灵活调整均线周期、股票标的和仓位管理方式。

![](img/de1820cfc93814614dbeb5e613e660b3_7.png)

以下是进行策略优化时可以尝试的方向：
*   **调整均线周期**：例如，将`(5, 10)`组合改为`(5, 60)`或`(10, 30)`，以适应不同的市场周期。
*   **修改交易标的**：将单只股票扩展为股票组合，并设计相应的仓位分配逻辑。
*   **优化买卖条件**：例如，在金叉时并非一次性投入全部资金，而是分批建仓。

## 结果可视化

除了查看收益曲线，我们还可以使用`record`函数记录每日的均线值，并在回测结果图中绘制出来，以便更直观地观察策略信号。

```python
        # 记录每日的均线值，用于绘图
        record(ma5=ma_short, ma10=ma_long)
```

`record`函数会在每个交易日记录指定的变量值。回测结束后，这些值会以曲线的形式展示在结果图中，与收益曲线并列，方便我们分析均线交叉与买卖操作是否吻合。

## 总结

![](img/de1820cfc93814614dbeb5e613e660b3_9.png)

本节课中我们一起学习了双均线策略的完整Python实现。我们从策略初始化开始，逐步完成了获取数据、计算均线、判断买卖信号以及执行交易的代码。关键点在于理解如何用 **`ma_short > ma_long 且未持仓`** 来识别金叉，用 **`ma_short < ma_long 且已持仓`** 来识别死叉。这是一个基础的策略框架，你可以通过调整参数、增加过滤条件或结合其他指标来优化它，使其更适应实战需求。