# 量化投资入门：P35：4-股票池筛选

![](img/070ac115151636f73819a75f08dcf327_1.png)

![](img/070ac115151636f73819a75f08dcf327_3.png)

![](img/070ac115151636f73819a75f08dcf327_5.png)

![](img/070ac115151636f73819a75f08dcf327_7.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成因子预处理后的最后一步，即根据因子值选择股票，并构建一个动态的股票池。

![](img/070ac115151636f73819a75f08dcf327_9.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子进行股票筛选。

## 因子预处理流程回顾

![](img/070ac115151636f73819a75f08dcf327_11.png)

![](img/070ac115151636f73819a75f08dcf327_13.png)

首先，我们回顾并整合之前完成的因子预处理步骤。预处理的目标是得到一个干净、标准化且消除了风格暴露的因子值，以便进行后续的选股。

![](img/070ac115151636f73819a75f08dcf327_15.png)

![](img/070ac115151636f73819a75f08dcf327_17.png)

以下是完整的预处理操作步骤：

![](img/070ac115151636f73819a75f08dcf327_19.png)

![](img/070ac115151636f73819a75f08dcf327_21.png)

![](img/070ac115151636f73819a75f08dcf327_23.png)

1.  **离群值处理**：使用三西格玛法则剔除因子数据中的极端值，防止极值对后续分析产生过大影响。
    ```python
    fundamental = three_sigma(fundamental, factor_column)
    ```
2.  **标准化**：将因子值转换为均值为0、标准差为1的标准正态分布，使得不同因子之间具有可比性。
    ```python
    fundamental = standardize(fundamental)
    ```
3.  **中性化**：通过回归分析，剔除因子值与市值等风格因子的相关性，确保选股逻辑的纯粹性。
    ```python
    fundamental = neutralize(fundamental, style_factor)
    ```

![](img/070ac115151636f73819a75f08dcf327_25.png)

![](img/070ac115151636f73819a75f08dcf327_27.png)

完成以上三步后，我们得到了一个名为 `fundamental_preprocessed` 的 DataFrame，其中包含了经过完整预处理的因子数据。

## 基于因子值筛选股票

![](img/070ac115151636f73819a75f08dcf327_29.png)

获得处理后的因子数据后，下一步是根据特定的规则筛选股票。我们以市净率因子为例，假设经过研究，较低的市净率可能预示着更高的未来收益。

以下是筛选股票的具体步骤：

![](img/070ac115151636f73819a75f08dcf327_31.png)

![](img/070ac115151636f73819a75f08dcf327_33.png)

1.  **确定筛选阈值**：我们需要确定一个分位点，例如选择因子值最小的前20%的股票。首先计算该分位点对应的具体数值。
    ```python
    threshold = fundamental_preprocessed[‘pb_ratio’].quantile(0.2)
    ```
2.  **执行筛选条件**：根据计算出的阈值，筛选出所有因子值小于或等于该阈值的股票。
    ```python
    selected_stocks = fundamental_preprocessed[fundamental_preprocessed[‘pb_ratio’] <= threshold].index.tolist()
    ```
3.  **更新股票池**：将筛选出的股票列表保存到上下文变量中，供后续调仓使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/070ac115151636f73819a75f08dcf327_35.png)

至此，我们得到了一个基于低市净率因子筛选出的目标股票池 `context.stock_list`。

![](img/070ac115151636f73819a75f08dcf327_37.png)

![](img/070ac115151636f73819a75f08dcf327_39.png)

## 执行调仓操作

股票池更新后，我们需要根据新的股票列表调整实际持仓。这涉及到卖出不再存在于新股票池中的股票，并买入新加入的股票。

![](img/070ac115151636f73819a75f08dcf327_41.png)

![](img/070ac115151636f73819a75f08dcf327_43.png)

以下是调仓逻辑的实现步骤：

![](img/070ac115151636f73819a75f08dcf327_45.png)

1.  **识别待卖出股票**：比较当前持仓的股票与新股票池，找出存在于持仓但不在新股票池中的股票，这些是需要卖出的。
    ```python
    # 假设 context.portfolio.positions 可以获取当前持仓的股票代码列表
    current_holdings = list(context.portfolio.positions.keys())
    stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
    ```
2.  **执行卖出操作**：遍历待卖出股票列表，下达卖出指令。
    ```python
    for stock in stocks_to_sell:
        order_target_percent(stock, 0)  # 将该股票持仓比例调整为0，即全部卖出
    ```
3.  **执行买入操作**：对于股票池中的股票，进行等权配置买入。
    ```python
    if len(context.stock_list) > 0:
        weight = 1.0 / len(context.stock_list)  # 计算每只股票的权重
        for stock in context.stock_list:
            order_target_percent(stock, weight)  # 调整该股票持仓至目标权重
    ```

通过以上步骤，我们完成了一个完整的从因子处理到股票筛选，再到执行调仓的交易逻辑闭环。

![](img/070ac115151636f73819a75f08dcf327_47.png)

本节课中我们一起学习了量化策略中股票池筛选与调仓的核心流程。我们首先整合了因子的预处理步骤，包括去极值、标准化和中性化。接着，我们基于处理后的因子值设定了筛选规则，构建了目标股票池。最后，我们实现了根据新股票池动态调整持仓的调仓逻辑，卖出现持仓中不符合条件的股票，并买入新入选的股票。这个过程是许多量化选股策略的基础框架。