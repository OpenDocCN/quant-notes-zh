# Python金融分析与量化交易实战教程：P36：04-4-股票池筛选

![](img/c8723bf7da6bdeeebdeb31066b2be534_1.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_3.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_5.png)

## 概述
在本节课程中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子预处理到生成目标股票列表，再到识别需要卖出和买入的股票的全过程。

![](img/c8723bf7da6bdeeebdeb31066b2be534_7.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来构建我们的投资组合。

![](img/c8723bf7da6bdeeebdeb31066b2be534_9.png)

---

## 执行预处理操作

![](img/c8723bf7da6bdeeebdeb31066b2be534_11.png)

现在，我们已经完成了中性化操作的代码编写。接下来，我们将在此处执行之前写好的几个预处理步骤。

![](img/c8723bf7da6bdeeebdeb31066b2be534_13.png)

以下是具体的执行步骤：

![](img/c8723bf7da6bdeeebdeb31066b2be534_15.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_17.png)

1.  **离群值处理**：首先，我们对因子数据进行离群值处理，以消除极端值的影响。这里我们使用三西格玛方法。
    ```python
    fundamental = fundamental_df
    fundamental = three_sigma(fundamental, factor_column)
    ```
    经过这一步，我们的指标数据中就不再包含极值了。

![](img/c8723bf7da6bdeeebdeb31066b2be534_19.png)

2.  **标准化操作**：完成离群值处理后，我们还需要进行标准化操作，使数据符合标准正态分布。
    ```python
    fundamental_standardized = standardize(fundamental)
    ```
    这一步相当于对数据做了一个标准化的转换。

![](img/c8723bf7da6bdeeebdeb31066b2be534_21.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_23.png)

3.  **中性化操作**：最后，我们对标准化后的因子进行中性化处理，以消除市值等风格因子的影响。
    ```python
    fundamental_neutralized = neutralize(fundamental_standardized)
    ```
    至此，我们完成了因子所需的所有预处理操作。

![](img/c8723bf7da6bdeeebdeb31066b2be534_25.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_27.png)

---

## 基于因子筛选股票

![](img/c8723bf7da6bdeeebdeb31066b2be534_29.png)

接下来，我们将基于处理后的因子，对股票池进行筛选。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据历史经验，这类股票可能带来更高的预期收益。

以下是筛选股票的具体过程：

1.  **设定筛选阈值**：我们需要指定一个筛选的指标和阈值。例如，我们希望选取因子值最小的前20%的股票。
    ```python
    threshold = fundamental_neutralized.quantile(0.2)
    ```
    这里，`quantile(0.2)` 计算了因子值的20%分位数。

![](img/c8723bf7da6bdeeebdeb31066b2be534_31.png)

2.  **生成目标股票列表**：根据设定的阈值，我们筛选出所有因子值小于或等于该阈值的股票。
    ```python
    selected_stocks = fundamental_neutralized[fundamental_neutralized <= threshold].index.tolist()
    ```
    这样，我们就得到了一个包含目标股票的列表 `selected_stocks`。

![](img/c8723bf7da6bdeeebdeb31066b2be534_33.png)

3.  **存储目标股票列表**：我们将筛选出的股票列表存储到上下文变量中，以便后续调仓使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/c8723bf7da6bdeeebdeb31066b2be534_35.png)

---

![](img/c8723bf7da6bdeeebdeb31066b2be534_37.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_39.png)

## 执行调仓操作

有了目标股票列表后，我们现在需要执行具体的买卖操作。这涉及到识别哪些现有持仓需要卖出，以及哪些目标股票需要买入。

以下是调仓逻辑的实现步骤：

![](img/c8723bf7da6bdeeebdeb31066b2be534_41.png)

![](img/c8723bf7da6bdeeebdeb31066b2be534_43.png)

1.  **识别需要卖出的股票**：我们需要找出当前持仓中，但不在最新目标股票池里的股票。这些股票将被卖出。
    ```python
    # 获取当前账户的持仓股票列表
    current_holdings = context.portfolio.positions.keys()
    # 找出当前持仓中不在目标股票池的股票
    stocks_to_sell = set(current_holdings).difference(context.stock_list)
    ```
    这里，`difference()` 方法用于找出两个集合的差集。

![](img/c8723bf7da6bdeeebdeb31066b2be534_45.png)

2.  **执行卖出操作**：对于识别出的需要卖出的股票，我们可以遍历它们并执行卖出指令。
    ```python
    for stock in stocks_to_sell:
        order_target_percent(stock, 0)  # 将该股票持仓比例调整为0，即全部卖出
    ```

3.  **执行买入操作**：对于目标股票池中的股票，如果当前没有持仓或持仓不足，则执行买入操作。通常我们会将资金等权分配到每只目标股票上。
    ```python
    # 计算每只目标股票应分配的资金权重
    weight = 1.0 / len(context.stock_list) if context.stock_list else 0
    for stock in context.stock_list:
        order_target_percent(stock, weight)  # 调整该股票持仓至目标权重
    ```
    通过以上步骤，我们就完成了一个完整的基于因子筛选的调仓周期。

---

![](img/c8723bf7da6bdeeebdeb31066b2be534_47.png)

## 总结
本节课中我们一起学习了量化策略中股票池筛选与调仓的核心流程。我们首先回顾并执行了因子的预处理步骤，包括离群值处理、标准化和中性化。接着，我们基于处理后的因子设定了筛选条件，生成了目标股票列表。最后，我们详细讲解了如何通过比较当前持仓与目标股票列表来识别需要卖出和买入的股票，并执行相应的交易指令，从而完成整个投资组合的调整。