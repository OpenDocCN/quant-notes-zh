# 量化交易入门：P24：1-策略任务分析 📊

![](img/6b82a9b9674d124f8b25abbb224fdd29_1.png)

在本节课中，我们将学习如何使用交易平台构建一个简单的量化交易策略，并熟悉相关的API接口。我们将通过一个具体案例——从沪深300指数中动态选择并持有表现最佳的10只股票——来演示策略开发的完整流程。

## 策略目标概述 🎯

我们的核心目标是：构建一个策略，使其始终持有沪深300指数中表现最好的10只股票。策略需要定期（例如每日）根据选定的财务指标（如盈利能力）对所有股票进行排序，更新持仓，卖出不再属于前十的股票，并买入新进入前十的股票。

## 策略开发步骤详解 🔧

上一节我们概述了策略目标，本节中我们来看看实现这个策略的具体步骤和代码模块。

### 第一步：创建策略文件

在交易平台中，点击“新建策略”。在弹出窗口中，为策略命名，例如 `simple_demo`。选择股票作为交易品种，点击确定后，平台会生成一个可编辑的代码界面。

### 第二步：理解代码结构

生成的代码模板包含几个关键函数模块，我们需要在其中填充逻辑：

*   **初始化函数 (`__init__`)**: 策略启动时执行一次，用于设置初始参数。
*   **盘前处理函数 (`before_trading`)**: 在每个交易日开始前执行，用于数据准备和计算。
*   **行情处理函数 (`handle_bar`)**: 在每次收到新的K线数据（如每分钟或每日）时执行，是核心的交易逻辑所在。

### 第三步：在初始化中设定股票池

在 `__init__` 函数中，我们需要获取沪深300指数的所有成分股作为我们的初始股票池。

```python
def __init__(self):
    # 获取沪深300指数成分股代码列表
    self.stock_pool = get_index_stocks('000300.SH')
```

### 第四步：在盘前处理中筛选股票

在 `before_trading` 函数中，我们需要完成每日的数据查询和排序工作。以下是该步骤的核心任务列表：

*   **查询数据**：获取股票池中每只股票的选定财务指标（例如，净利润）。
*   **进行排序**：根据该指标对股票进行降序排序。
*   **选取前十**：取出排名前十的股票代码。

```python
def before_trading(self):
    # 1. 查询财务数据（示例：净利润）
    # 假设 `get_fundamental` 是查询财务数据的函数
    fundamental_data = get_fundamental(self.stock_pool, ‘net_profit’)
    
    # 2. 按净利润降序排序
    sorted_stocks = fundamental_data.sort_values(ascending=False)
    
    # 3. 选取前十名股票代码
    self.top_10_stocks = sorted_stocks.index[:10].tolist()
```

### 第五步：在行情处理中执行交易

在 `handle_bar` 函数中，我们将实施具体的交易逻辑。核心是比对当前持仓与我们计算出的最佳前十股票列表。

以下是该步骤的核心任务列表：

*   **获取当前持仓**：查询账户当前持有的所有股票。
*   **比对与调整**：
    *   对于当前持仓中**不在** `self.top_10_stocks` 列表里的股票，执行卖出操作。
    *   对于 `self.top_10_stocks` 列表里**尚未持有**的股票，执行买入操作。
*   **资金管理**：在买入时，将可用资金平均分配给要买入的股票。

```python
def handle_bar(self, bar):
    # 获取当前持仓的股票代码列表
    current_positions = list(self.context.portfolio.positions.keys())
    
    # 卖出不再属于前十的股票
    for stock in current_positions:
        if stock not in self.top_10_stocks:
            # 卖出全部持仓
            order_target_value(stock, 0)
    
    # 买入新的前十股票（尚未持有的部分）
    stocks_to_buy = [s for s in self.top_10_stocks if s not in current_positions]
    if stocks_to_buy:
        # 计算可用于购买新股票的资金（平均分配）
        available_cash = self.context.portfolio.available_cash
        cash_per_stock = available_cash / len(stocks_to_buy)
        
        for stock in stocks_to_buy:
            # 买入股票，目标价值为平均分配的资金
            order_target_value(stock, cash_per_stock)
```

## 总结 📝

![](img/6b82a9b9674d124f8b25abbb224fdd29_3.png)

![](img/6b82a9b9674d124f8b25abbb224fdd29_5.png)

本节课中我们一起学习了如何构建一个完整的简单量化交易策略。我们首先明确了策略目标——动态持有沪深300中财务指标最佳的前十只股票。随后，我们分解了实现步骤：在 `__init__` 中初始化股票池，在 `before_trading` 中完成每日的数据筛选和排序，最后在 `handle_bar` 中执行具体的调仓交易逻辑。这个流程清晰地展示了策略开发中“数据准备-逻辑判断-执行交易”的核心循环，为后续学习更复杂的策略奠定了基础。