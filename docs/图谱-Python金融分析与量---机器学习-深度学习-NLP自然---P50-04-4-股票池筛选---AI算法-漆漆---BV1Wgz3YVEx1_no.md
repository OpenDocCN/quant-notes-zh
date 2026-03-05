# 人工智能金融量化交易：P50：04-4-股票池筛选

![](img/56cd285df1fc81204c10d427675084fc_1.png)

![](img/56cd285df1fc81204c10d427675084fc_3.png)

![](img/56cd285df1fc81204c10d427675084fc_5.png)

在本节课中，我们将学习如何基于预处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子处理到生成最终交易股票列表的完整流程。

![](img/56cd285df1fc81204c10d427675084fc_7.png)

## 概述

![](img/56cd285df1fc81204c10d427675084fc_9.png)

上一节我们完成了因子的中性化操作。本节中，我们将利用处理好的因子数据，执行股票池的筛选逻辑，并准备后续的买入卖出操作。

## 执行预处理操作

现在，我们可以执行之前编写好的预处理操作了。我们将按顺序对因子数据进行离群值处理、标准化和中性化。

![](img/56cd285df1fc81204c10d427675084fc_11.png)

以下是具体的执行步骤：

![](img/56cd285df1fc81204c10d427675084fc_13.png)

![](img/56cd285df1fc81204c10d427675084fc_15.png)

1.  **离群值处理**：首先，对因子数据执行三西格玛法以去除极端值。
    ```python
    fundamental = pd.DataFrame(factor_data)  # 假设factor_data是原始的因子数据
    factor_no_outliers = three_sigma(fundamental['pb_ratio'])
    ```

![](img/56cd285df1fc81204c10d427675084fc_17.png)

![](img/56cd285df1fc81204c10d427675084fc_19.png)

2.  **标准化操作**：接着，对去除离群值后的数据进行标准化处理。
    ```python
    factor_standardized = standardize(factor_no_outliers)
    ```

![](img/56cd285df1fc81204c10d427675084fc_21.png)

![](img/56cd285df1fc81204c10d427675084fc_23.png)

3.  **中性化操作**：最后，对标准化后的因子进行中性化处理，以消除行业或市值等风格因子的影响。
    ```python
    factor_neutralized = neutralize(factor_standardized)
    ```

![](img/56cd285df1fc81204c10d427675084fc_25.png)

至此，我们完成了因子所需的所有预处理操作。

![](img/56cd285df1fc81204c10d427675084fc_27.png)

## 基于因子筛选股票

接下来，我们需要基于处理后的因子值对股票池进行筛选。我们的目标是选出因子值较小（例如市净率较低）的股票，因为理论上它们可能带来更高的预期收益。

![](img/56cd285df1fc81204c10d427675084fc_29.png)

以下是筛选逻辑：

1.  **确定筛选阈值**：我们计划选择因子值最小的前20%的股票。首先需要计算这个分位点。
    ```python
    threshold = factor_neutralized.quantile(0.2)
    ```

2.  **执行筛选**：根据阈值，筛选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
    ```

![](img/56cd285df1fc81204c10d427675084fc_31.png)

![](img/56cd285df1fc81204c10d427675084fc_33.png)

3.  **存储结果**：将筛选出的股票列表存入上下文变量中，供后续调仓使用。
    ```python
    context.stock_list = selected_stocks
    ```

我们现在得到了一个基于因子策略选出的目标股票池。

![](img/56cd285df1fc81204c10d427675084fc_35.png)

## 准备调仓操作

![](img/56cd285df1fc81204c10d427675084fc_37.png)

![](img/56cd285df1fc81204c10d427675084fc_39.png)

有了目标股票池后，我们需要规划具体的交易指令。这包括卖出不再池中的股票，以及买入新入选的股票。

首先，我们需要识别并删除当前持仓中不在目标股票池里的股票。

以下是识别待删除股票的逻辑：

![](img/56cd285df1fc81204c10d427675084fc_41.png)

![](img/56cd285df1fc81204c10d427675084fc_43.png)

1.  **获取当前持仓**：从交易上下文中获取当前账户持有的所有股票。
    ```python
    current_holdings = context.portfolio.positions.keys()  # 假设API如此
    ```

![](img/56cd285df1fc81204c10d427675084fc_45.png)

2.  **计算差异**：找出当前持仓股票与目标股票池的差集，这些就是需要卖出的股票。
    ```python
    stocks_to_sell = list(set(current_holdings).difference(set(context.stock_list)))
    ```

这样，我们就明确了需要从持仓中移除的股票列表。

## 总结

![](img/56cd285df1fc81204c10d427675084fc_47.png)

本节课中我们一起学习了股票池筛选的完整流程。我们首先对预处理后的因子数据执行了筛选，选出了符合策略逻辑的目标股票。接着，我们通过比较当前持仓与目标股票池，识别出了需要卖出的股票，为下一节执行具体的买卖交易指令做好了准备。整个流程的核心在于将因子信号转化为可执行的股票列表。