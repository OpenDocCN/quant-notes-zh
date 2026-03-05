# Python金融分析与量化交易实战教程：P35：4-股票池筛选

![](img/6b6c3e93a6f0278c53a6fd2de61da775_1.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_3.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_5.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_7.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选。我们将完成从因子预处理到最终股票筛选的完整流程，并准备进行后续的调仓操作。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_9.png)

上一节我们介绍了因子的中性化操作，本节中我们来看看如何利用处理好的因子来构建我们的投资组合。

## 因子预处理流程
现在，我们已经完成了中性化操作的编写。接下来，我们将在此处执行之前编写的几个预处理步骤。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_11.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_13.png)

以下是具体的预处理操作步骤：

![](img/6b6c3e93a6f0278c53a6fd2de61da775_15.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_17.png)

1.  **离群值处理**：首先，对因子数据进行三西格玛（3σ）处理，以去除极端值。这里我们需要指定对哪个数据进行操作，即对我们的因子数据（如市净率）进行处理。
    ```python
    fundamental = fundamental_df
    fundamental = three_sigma(fundamental, factor_column)
    ```
    经过此步骤，我们的指标数据中将不再包含极值。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_19.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_21.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_23.png)

2.  **标准化操作**：完成离群值处理后，还需要进行标准化操作，使数据符合标准正态分布。
    ```python
    fundamental = standardize(fundamental)
    ```
    这相当于执行了一个标准化转换。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_25.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_27.png)

3.  **中性化操作**：最后，对标准化后的因子进行中性化处理，以消除市值等风格因子的影响。
    ```python
    preprocessed_factor = neutralize(fundamental)
    ```
    至此，我们完成了所有必需的因子预处理操作。

## 基于因子进行股票筛选
接下来，我们需要基于处理好的因子对股票池进行筛选或调仓。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_29.png)

首先，需要指定一个筛选指标。例如，在量化策略中，我们通常认为市净率较小的股票未来收益可能更高。因此，我们希望筛选出因子值最小的前20%的股票。

以下是筛选操作的步骤：

![](img/6b6c3e93a6f0278c53a6fd2de61da775_31.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_33.png)

1.  **设定筛选阈值**：我们选择因子值从小到大排序的前20%作为候选股票。
    ```python
    threshold = preprocessed_factor.quantile(0.2)
    ```
2.  **执行筛选判断**：对所有股票的因子值进行判断，选出小于等于该阈值的股票。
    ```python
    selected_stocks = preprocessed_factor[preprocessed_factor <= threshold].index.tolist()
    ```
    这个判断基于因子值进行，最终我们得到了一个包含目标股票代码的列表。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_35.png)

3.  **存储筛选结果**：将筛选出的股票列表存入上下文（context）中，以备后续交易使用。
    ```python
    context.stock_list = selected_stocks
    ```
    现在，我们有了一个准备进行交易的股票列表（`stock_list`）。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_37.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_39.png)

## 准备调仓操作
在执行买卖操作前，我们需要明确要买入哪些股票，以及将哪些现有持仓卖出。

首先，需要找出那些不在当前新股票池中的现有持仓。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_41.png)

![](img/6b6c3e93a6f0278c53a6fd2de61da775_43.png)

以下是识别待删除股票的逻辑：

![](img/6b6c3e93a6f0278c53a6fd2de61da775_45.png)

1.  **获取当前持仓**：从账户信息中获取当前持有的所有股票。
    ```python
    current_holdings = context.portfolio.positions
    ```
    （注：实际API可能为`context.portfolio.positions`或`context.positions`，需根据平台确认）

2.  **计算差异集合**：对比当前持仓股票列表和新股票池列表，找出存在于持仓但不在新池子中的股票。这可以通过集合的差集（difference）操作实现。
    ```python
    stocks_to_sell = set(current_holdings).difference(set(context.stock_list))
    ```
    这样，我们就得到了所有需要卖出的股票列表。

![](img/6b6c3e93a6f0278c53a6fd2de61da775_47.png)

## 总结
本节课中我们一起学习了股票池筛选的完整过程。我们从预处理后的因子出发，通过设定阈值筛选出目标股票，并计算了需要调整的持仓。这个过程是量化交易策略中选股与调仓的核心环节，为下一节执行具体的交易订单打下了基础。