# Python金融分析与量化交易实战课程：P34：股票池筛选 📊

![](img/f835137f2b2aee710001db5b5259755b_1.png)

![](img/f835137f2b2aee710001db5b5259755b_3.png)

![](img/f835137f2b2aee710001db5b5259755b_5.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选和调仓操作。我们将完成从因子预处理到最终股票选择的完整流程。

![](img/f835137f2b2aee710001db5b5259755b_7.png)

---

![](img/f835137f2b2aee710001db5b5259755b_9.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子进行股票筛选。

首先，我们需要执行之前编写好的预处理操作。预处理操作包括三个步骤：去除离群值、标准化和中性化。

![](img/f835137f2b2aee710001db5b5259755b_11.png)

以下是预处理操作的具体步骤：

![](img/f835137f2b2aee710001db5b5259755b_13.png)

1.  **去除离群值**：对因子数据应用三西格玛法则，以消除极端值的影响。
    ```python
    fundamental = fundamental_dataframe
    fundamental_no_outliers = fundamental[‘pb_ratio’].apply(three_sigma)
    ```

![](img/f835137f2b2aee710001db5b5259755b_15.png)

![](img/f835137f2b2aee710001db5b5259755b_17.png)

2.  **标准化**：将去除离群值后的数据进行标准化处理，使其均值为0，标准差为1。
    ```python
    fundamental_standardized = standardize(fundamental_no_outliers)
    ```

![](img/f835137f2b2aee710001db5b5259755b_19.png)

![](img/f835137f2b2aee710001db5b5259755b_21.png)

3.  **中性化**：对标准化后的因子进行中性化处理，以消除市场风格（如市值）的影响。
    ```python
    fundamental_neutralized = neutralize(fundamental_standardized, style_factor=‘market_cap’)
    ```

![](img/f835137f2b2aee710001db5b5259755b_23.png)

![](img/f835137f2b2aee710001db5b5259755b_25.png)

完成所有预处理操作后，我们得到了一个经过清洗和调整的因子值。

![](img/f835137f2b2aee710001db5b5259755b_27.png)

---

![](img/f835137f2b2aee710001db5b5259755b_29.png)

接下来，我们将基于这个处理后的因子对股票池进行筛选。我们的目标是选出因子值较小（例如，市净率较低）的股票，因为根据策略假设，这些股票可能具有更高的预期收益。

![](img/f835137f2b2aee710001db5b5259755b_31.png)

以下是筛选股票的具体步骤：

1.  **确定筛选阈值**：我们需要确定一个分位数点，例如选择因子值最小的前20%的股票。
    ```python
    threshold = fundamental_neutralized.quantile(0.2)
    ```

2.  **执行筛选**：根据阈值，筛选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = fundamental_neutralized[fundamental_neutralized <= threshold].index.tolist()
    ```

![](img/f835137f2b2aee710001db5b5259755b_33.png)

3.  **存储筛选结果**：将筛选出的股票列表存入上下文变量中，供后续调仓使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/f835137f2b2aee710001db5b5259755b_35.png)

现在，我们得到了一个基于因子策略筛选出的目标股票池。

![](img/f835137f2b2aee710001db5b5259755b_37.png)

---

![](img/f835137f2b2aee710001db5b5259755b_39.png)

最后一步是执行调仓操作。我们需要比较当前持仓的股票与新的目标股票池，卖出不在新池子中的股票，并买入新池子中尚未持有的股票。

![](img/f835137f2b2aee710001db5b5259755b_41.png)

以下是调仓操作的具体步骤：

1.  **识别待卖出股票**：找出当前持仓中，但不在新目标股票池中的股票。
    ```python
    current_holdings = context.portfolio.positions
    stocks_to_sell = set(current_holdings).difference(context.stock_list)
    ```

![](img/f835137f2b2aee710001db5b5259755b_43.png)

![](img/f835137f2b2aee710001db5b5259755b_45.png)

2.  **执行卖出操作**：遍历待卖出股票列表，执行卖出指令。
    ```python
    for stock in stocks_to_sell:
        order_target_percent(stock, 0)
    ```

![](img/f835137f2b2aee710001db5b5259755b_47.png)

3.  **执行买入操作**：将资金平均分配给目标股票池中的所有股票（或根据其他权重分配逻辑）。
    ```python
    if len(context.stock_list) > 0:
        weight = 1.0 / len(context.stock_list)
        for stock in context.stock_list:
            order_target_percent(stock, weight)
    ```

通过以上步骤，我们就完成了一次基于因子的股票池筛选和动态调仓。

---

![](img/f835137f2b2aee710001db5b5259755b_49.png)

本节课中我们一起学习了量化策略中股票池筛选的完整流程。我们从预处理后的因子数据出发，通过设定阈值筛选出目标股票，最后通过比较持仓与目标池的差异，执行了卖出和买入操作，实现了投资组合的更新。这个过程是许多量化策略的核心组成部分。