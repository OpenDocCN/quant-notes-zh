# Python金融时间序列分析与量化交易实战教程：P35：34.股票池筛选 📊

![](img/6678404e39967274dbf10e47c6d13aa1_0.png)

![](img/6678404e39967274dbf10e47c6d13aa1_2.png)

![](img/6678404e39967274dbf10e47c6d13aa1_4.png)

![](img/6678404e39967274dbf10e47c6d13aa1_6.png)

![](img/6678404e39967274dbf10e47c6d13aa1_8.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子预处理到最终股票选择的完整流程。

![](img/6678404e39967274dbf10e47c6d13aa1_10.png)

![](img/6678404e39967274dbf10e47c6d13aa1_12.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来筛选股票。

![](img/6678404e39967274dbf10e47c6d13aa1_14.png)

![](img/6678404e39967274dbf10e47c6d13aa1_16.png)

![](img/6678404e39967274dbf10e47c6d13aa1_18.png)

## 因子预处理流程回顾

![](img/6678404e39967274dbf10e47c6d13aa1_20.png)

![](img/6678404e39967274dbf10e47c6d13aa1_22.png)

![](img/6678404e39967274dbf10e47c6d13aa1_24.png)

![](img/6678404e39967274dbf10e47c6d13aa1_26.png)

首先，我们需要对原始因子数据进行一系列预处理操作，包括去除离群值、标准化和中性化。

![](img/6678404e39967274dbf10e47c6d13aa1_28.png)

![](img/6678404e39967274dbf10e47c6d13aa1_30.png)

![](img/6678404e39967274dbf10e47c6d13aa1_32.png)

![](img/6678404e39967274dbf10e47c6d13aa1_34.png)

以下是完整的预处理步骤：

![](img/6678404e39967274dbf10e47c6d13aa1_36.png)

![](img/6678404e39967274dbf10e47c6d13aa1_38.png)

1.  **去除离群值**：使用三西格玛（3σ）法则处理因子数据中的极端值。
    ```python
    fundamental = three_sigma(factor_data)
    ```
2.  **标准化**：将因子数据转换为均值为0、标准差为1的标准正态分布。
    ```python
    standardized_factor = standard_scaler(fundamental)
    ```
3.  **中性化**：消除因子与市值等风格因子的相关性，使因子收益更纯粹。
    ```python
    neutralized_factor = neutralize(standardized_factor, market_cap)
    ```

![](img/6678404e39967274dbf10e47c6d13aa1_40.png)

![](img/6678404e39967274dbf10e47c6d13aa1_42.png)

![](img/6678404e39967274dbf10e47c6d13aa1_44.png)

完成以上步骤后，我们得到了一个经过清洗和调整的、可用于选股的因子值。

![](img/6678404e39967274dbf10e47c6d13aa1_46.png)

![](img/6678404e39967274dbf10e47c6d13aa1_48.png)

## 基于因子进行股票筛选

![](img/6678404e39967274dbf10e47c6d13aa1_50.png)

![](img/6678404e39967274dbf10e47c6d13aa1_52.png)

![](img/6678404e39967274dbf10e47c6d13aa1_54.png)

预处理完成后，下一步是基于这个因子值来筛选股票。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据策略假设，这些股票可能具有更高的预期收益。

![](img/6678404e39967274dbf10e47c6d13aa1_56.png)

![](img/6678404e39967274dbf10e47c6d13aa1_58.png)

![](img/6678404e39967274dbf10e47c6d13aa1_60.png)

![](img/6678404e39967274dbf10e47c6d13aa1_62.png)

以下是筛选股票的具体步骤：

![](img/6678404e39967274dbf10e47c6d13aa1_64.png)

![](img/6678404e39967274dbf10e47c6d13aa1_66.png)

![](img/6678404e39967274dbf10e47c6d13aa1_68.png)

1.  **设定筛选阈值**：我们选择因子值从小到大排序的前20%的股票。
    ```python
    threshold = neutralized_factor.quantile(0.2)
    ```
2.  **执行筛选条件**：选出所有因子值小于或等于该阈值的股票。
    ```python
    selected_stocks = neutralized_factor[neutralized_factor <= threshold].index
    ```
3.  **存储筛选结果**：将选出的股票列表存入上下文变量中，供后续交易函数使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/6678404e39967274dbf10e47c6d13aa1_70.png)

![](img/6678404e39967274dbf10e47c6d13aa1_72.png)

![](img/6678404e39967274dbf10e47c6d13aa1_74.png)

![](img/6678404e39967274dbf10e47c6d13aa1_76.png)

至此，我们得到了一个根据特定因子策略筛选出的目标股票池。

![](img/6678404e39967274dbf10e47c6d13aa1_78.png)

![](img/6678404e39967274dbf10e47c6d13aa1_80.png)

![](img/6678404e39967274dbf10e47c6d13aa1_82.png)

![](img/6678404e39967274dbf10e47c6d13aa1_84.png)

## 执行调仓操作

![](img/6678404e39967274dbf10e47c6d13aa1_86.png)

![](img/6678404e39967274dbf10e47c6d13aa1_88.png)

![](img/6678404e39967274dbf10e47c6d13aa1_90.png)

![](img/6678404e39967274dbf10e47c6d13aa1_92.png)

获得目标股票池后，我们需要根据它来调整当前的投资组合。这涉及到卖出不再属于目标池的股票，并买入新入选的股票。

![](img/6678404e39967274dbf10e47c6d13aa1_94.png)

![](img/6678404e39967274dbf10e47c6d13aa1_96.png)

![](img/6678404e39967274dbf10e47c6d13aa1_98.png)

以下是调仓逻辑的实现步骤：

![](img/6678404e39967274dbf10e47c6d13aa1_100.png)

![](img/6678404e39967274dbf10e47c6d13aa1_102.png)

![](img/6678404e39967274dbf10e47c6d13aa1_104.png)

![](img/6678404e39967274dbf10e47c6d13aa1_106.png)

1.  **识别待卖出股票**：找出当前持仓中但不在新股票池里的股票。
    ```python
    # 获取当前持仓的股票列表
    current_holdings = context.portfolio.positions.keys()
    # 计算需要卖出的股票（在当前持仓中但不在新股票池里）
    stocks_to_sell = set(current_holdings).difference(context.stock_list)
    ```
2.  **执行卖出操作**：对识别出的股票执行卖出指令。
    ```python
    for stock in stocks_to_sell:
        order_target_percent(stock, 0)  # 将该股票仓位调整为0，即全部卖出
    ```
3.  **执行买入操作**：将资金平均分配给目标股票池中的所有股票。
    ```python
    if len(context.stock_list) > 0:
        weight = 1.0 / len(context.stock_list)  # 计算每只股票的权重
        for stock in context.stock_list:
            order_target_percent(stock, weight)  # 买入股票至目标权重
    ```

![](img/6678404e39967274dbf10e47c6d13aa1_108.png)

![](img/6678404e39967274dbf10e47c6d13aa1_110.png)

![](img/6678404e39967274dbf10e47c6d13aa1_112.png)

通过以上步骤，我们就完成了一个完整的基于单因子的股票池筛选与调仓策略。

![](img/6678404e39967274dbf10e47c6d13aa1_114.png)

![](img/6678404e39967274dbf10e47c6d13aa1_116.png)

![](img/6678404e39967274dbf10e47c6d13aa1_118.png)

![](img/6678404e39967274dbf10e47c6d13aa1_120.png)

本节课中我们一起学习了量化交易中股票池筛选的完整流程。我们从预处理后的因子出发，设定了筛选条件，得到了目标股票列表，并最终实现了根据新股票池调整投资组合的调仓逻辑。这个过程是构建自动化交易策略的核心环节之一。