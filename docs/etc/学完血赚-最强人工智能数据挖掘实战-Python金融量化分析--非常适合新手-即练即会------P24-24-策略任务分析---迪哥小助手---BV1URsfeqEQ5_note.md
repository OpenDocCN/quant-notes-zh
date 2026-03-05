# 人工智能数据挖掘实战：P24：策略任务分析 📈

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_1.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_2.png)

在本节课中，我们将学习如何使用交易平台构建一个简单的量化交易策略。我们将通过一个具体的任务——从沪深300指数中动态选择并持有表现最佳的10只股票——来熟悉平台的核心API和策略编写流程。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_4.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_5.png)

## 1. 创建新策略 🆕

首先，我们需要在交易平台中创建一个新的策略。

以下是创建新策略的步骤：
1.  点击“新建策略”按钮。
2.  为策略命名，例如 `simple_demo`。
3.  选择股票作为交易标的。
4.  点击“确定”完成创建。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_7.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_8.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_9.png)

创建成功后，平台会提供一个代码编辑界面，我们将在这里编写策略逻辑。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_10.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_11.png)

## 2. 策略模块与任务概述 🔧

上一节我们创建了策略文件，本节中我们来看看策略代码的基本结构。平台策略通常包含几个核心函数模块，如 `initialize`（初始化）、`before_trading`（盘前处理）和 `handle_bar`（盘中处理）。这些模块需要我们自己编写代码来实现。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_13.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_15.png)

现在，我们来明确本次策略的任务需求。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_17.png)

我们的目标是：从沪深300指数的300只成分股中，始终持有财务表现（例如盈利能力）最好的10只股票。由于股票的财务数据每日都在变化，我们需要每天重新评估并调整持仓，卖出不再属于“前十”的股票，买入新进入“前十”的股票。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_18.png)

## 3. 策略逻辑拆解 🧩

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_20.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_21.png)

有了基本目标之后，我们来具体分析每个模块应该实现什么功能。

### 3.1 初始化模块 (`initialize`)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_23.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_24.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_25.png)

在初始化函数中，我们需要完成一些一次性设置。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_26.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_27.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_28.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_29.png)

以下是初始化模块的主要任务：
*   **获取股票池**：获取沪深300指数的所有成分股代码列表。
*   **代码示例**：`self.stock_pool = get_index_stocks(‘000300.XSHG’)`

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_31.png)

### 3.2 盘前处理模块 (`before_trading`)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_32.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_33.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_34.png)

在每天交易开始前，我们需要进行数据准备和计算，这部分操作与实际下单交易无关。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_36.png)

以下是盘前处理模块的主要任务：
*   **获取数据**：获取股票池中所有股票的财务数据（例如净利润）。
*   **排序筛选**：根据选定的财务指标（如净利润）对股票进行排序。
*   **选出目标**：选取排名前10的股票代码，作为当日的目标持仓列表。
*   **核心概念**：`target_stocks = sorted(stock_data, key=lambda x: x[‘net_profit’], reverse=True)[:10]`

### 3.3 盘中处理模块 (`handle_bar`)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_38.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_39.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_40.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_41.png)

这个函数在每次Bar（如每分钟或每天）数据更新时被调用，是执行实际交易逻辑的地方。

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_42.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_43.png)

以下是盘中处理模块的主要任务：
*   **获取当前持仓**：查询账户当前持有的所有股票。
*   **逻辑判断**：对比当前持仓与今日目标持仓列表 (`target_stocks`)。
*   **执行调仓**：
    *   卖出当前持有但不在目标列表中的股票。
    *   用剩余资金买入目标列表中尚未持有的股票。
*   **核心逻辑**：目标是让持仓与 `target_stocks` 列表保持一致。

## 4. 总结 📝

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_45.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_46.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_47.png)

![](img/c1e77a0e440d6d259ddbcf8f60fa626e_48.png)

本节课中我们一起学习了如何为一个简单的量化交易策略进行任务分析。我们明确了策略目标——动态持有沪深300中财务表现最佳的10只股票，并将实现逻辑拆解到三个核心模块中：`initialize` 负责初始设置，`before_trading` 负责每日的数据处理和选股，`handle_bar` 负责执行具体的买卖交易以调整持仓。这个流程是构建大多数量化策略的基础框架。