# Python金融时间序列分析与量化交易实战教程：P21：20.策略任务分析 📊

![](img/fb91c052d4949a659cc5c359a06eaceb_0.png)

![](img/fb91c052d4949a659cc5c359a06eaceb_2.png)

在本节课中，我们将学习如何在一个交易平台上构建一个简单的量化交易策略。我们将通过一个具体的任务来熟悉平台提供的API，并理解策略开发的基本流程。

![](img/fb91c052d4949a659cc5c359a06eaceb_4.png)

## 策略目标概述 🎯

我们的目标是构建一个简单的选股策略。具体来说，我们希望从沪深300指数的成分股中，始终持有表现最好的10只股票。这个“最好”的标准，我们暂时定义为财务数据中的“盈利”指标。策略会定期（例如每天）重新评估所有股票的盈利情况，并调整持仓，确保手中始终是盈利排名前10的股票。

![](img/fb91c052d4949a659cc5c359a06eaceb_6.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何将这个想法转化为具体的代码。

## 策略开发步骤 🛠️

### 第一步：创建策略文件

首先，我们需要在交易平台上创建一个新的策略。

![](img/fb91c052d4949a659cc5c359a06eaceb_0.png)

以下是创建新策略的步骤：
1.  点击平台上的“新建策略”按钮。
2.  在弹出的对话框中，为策略命名，例如 `simple_demo`。
3.  选择交易标的为“股票”。
4.  点击确定后，平台会生成一个可编辑的代码界面。

![](img/fb91c052d4949a659cc5c359a06eaceb_2.png)

### 第二步：理解策略代码结构

![](img/fb91c052d4949a659cc5c359a06eaceb_8.png)

生成的代码界面中，通常包含几个核心的函数模块，这些是需要我们填充逻辑的地方。

![](img/fb91c052d4949a659cc5c359a06eaceb_10.png)

![](img/fb91c052d4949a659cc5c359a06eaceb_4.png)

以下是主要的函数模块：
*   **初始化函数 (`initialize`)**：在策略启动时运行一次，用于设置策略参数、股票池等。
*   **盘前处理函数 (`before_trading`)**：在每个交易日开始前运行，用于获取数据、进行计算等非交易操作。
*   **盘中处理函数 (`handle_bar`)**：在每个交易时段（如每分钟或每天）运行，是执行买卖交易逻辑的核心位置。

### 第三步：设计策略逻辑

根据我们的目标，我们需要将任务分解到上述不同的函数中。

![](img/fb91c052d4949a659cc5c359a06eaceb_12.png)

![](img/fb91c052d4949a659cc5c359a06eaceb_6.png)

**1. 在 `initialize` 函数中：**
我们需要获取沪深300指数的所有成分股，作为我们的初始股票池。
```python
# 示例代码结构
def initialize(context):
    # 获取沪深300成分股列表
    stock_pool = get_index_stocks(‘000300.XSHG‘)
    context.stock_pool = stock_pool
    context.hold_num = 10  # 设定持仓股票数量
```

**2. 在 `before_trading` 函数中：**
我们需要每天盘前执行数据查询和计算。
以下是每天盘前需要执行的任务：
1.  从 `context.stock_pool` 中获取所有股票最新的财务数据（例如盈利指标）。
2.  根据盈利指标对股票进行排序。
3.  选出盈利排名前 `context.hold_num`（即10）的股票，并将列表保存起来，供交易函数使用。
```python
# 示例代码结构
def before_trading(context):
    # 获取股票池中所有股票的盈利数据
    fundamentals = get_fundamentals(query(valuation.code, valuation.net_profit), date=context.current_dt)
    # 按盈利降序排序
    sorted_stocks = fundamentals.sort_values(by=‘net_profit‘, ascending=False)
    # 选取前10只股票代码
    context.top_stocks = sorted_stocks[‘code‘].iloc[:context.hold_num].tolist()
```

**3. 在 `handle_bar` 函数中：**
这里是执行交易指令的核心。我们需要对比当前持仓与我们计算出的“最优股票列表”。

![](img/fb91c052d4949a659cc5c359a06eaceb_8.png)

以下是每个交易时段需要执行的逻辑：
1.  获取当前账户的所有持仓。
2.  遍历当前持仓，如果某只股票不在最新的 `context.top_stocks` 列表中，则卖出。
3.  计算可用资金，并等权买入 `context.top_stocks` 列表中我们尚未持有的股票。
```python
# 示例代码结构
def handle_bar(context, bar_dict):
    # 1. 卖出逻辑：卖出不在推荐列表中的股票
    for stock in context.portfolio.positions:
        if stock not in context.top_stocks:
            order_target_value(stock, 0)  # 卖出全部持仓
    # 2. 买入逻辑：等权重买入推荐列表中的股票
    cash_per_stock = context.portfolio.cash / context.hold_num
    for stock in context.top_stocks:
        if stock not in context.portfolio.positions:
            order_value(stock, cash_per_stock)  # 买入大约等金额的股票
```

## 策略逻辑总结 📝

本节课中我们一起学习了如何构思并分解一个简单的量化交易策略任务。我们策略的核心思想是**动态调仓**，始终持有根据特定指标（如盈利）筛选出的头部股票。

我们通过三个步骤来实现：
1.  **初始化 (`initialize`)**：设定股票池和策略参数。
2.  **盘前计算 (`before_trading`)**：获取数据并计算每日的“最优股票组合”。
3.  **盘中交易 (`handle_bar`)**：根据计算结果调整实际持仓，卖出不符合条件的股票，买入新的目标股票。

![](img/fb91c052d4949a659cc5c359a06eaceb_14.png)

这个策略框架虽然简单，但涵盖了数据获取、信号计算和订单执行这三个量化策略的基本环节。在后续课程中，我们可以在此基础上引入更复杂的选股指标、风险控制模块和绩效分析。