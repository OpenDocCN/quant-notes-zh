# Python金融量化交易：P38：4-股票池筛选

![](img/7d638fe78cb8082964fe3061fc8cd8c0_0.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_2.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_4.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_6.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_8.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_10.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选。我们将完成因子预处理流程，并实现一个根据因子值（如市净率）筛选特定股票的逻辑，为后续的调仓交易操作做好准备。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_12.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_14.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_16.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_18.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子进行股票筛选。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_20.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_22.png)

## 因子预处理流程回顾与执行
现在，我们已经完成了中性化操作的代码编写。接下来，我们将执行之前写好的整个预处理流程。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_24.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_26.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_28.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_30.png)

首先，我们需要对因子数据进行离群值处理。以下是具体的操作步骤：

![](img/7d638fe78cb8082964fe3061fc8cd8c0_32.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_34.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_36.png)

1.  **离群值处理**：使用三西格玛方法对因子数据进行处理，以去除极端值。
    ```python
    fundamental = get_fundamentals(...).T
    factor_no_outliers = three_sigma(fundamental['pb_ratio'])
    ```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_38.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_40.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_42.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_44.png)

2.  **数据标准化**：对去除离群值后的因子数据进行标准化，使其均值为0，标准差为1。
    ```python
    factor_standardized = standardize(factor_no_outliers)
    ```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_46.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_48.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_50.png)

3.  **因子中性化**：对标准化后的因子进行中性化处理，以消除市值等风格因子的影响。
    ```python
    factor_neutralized = neutralize(factor_standardized, fundamental['market_cap'])
    ```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_52.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_54.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_56.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_58.png)

经过以上三步，我们完成了因子所需的所有预处理操作，得到了一个相对“纯净”的因子值。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_60.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_62.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_64.png)

## 基于因子进行股票筛选
接下来，我们需要基于处理后的因子值对股票池进行筛选。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据历史经验，这类股票可能具有更高的预期收益。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_66.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_68.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_70.png)

以下是筛选股票的核心步骤：

![](img/7d638fe78cb8082964fe3061fc8cd8c0_72.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_74.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_76.png)

1.  **确定筛选阈值**：我们需要确定一个分位点，例如选择因子值最小的前20%的股票。
    ```python
    threshold = factor_neutralized.quantile(0.2)
    ```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_78.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_80.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_82.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_84.png)

2.  **执行筛选逻辑**：根据阈值，筛选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
    ```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_86.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_88.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_90.png)

3.  **存储筛选结果**：将筛选出的股票列表存入上下文变量中，供后续调仓函数使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_92.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_94.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_96.png)

这样，我们就得到了一个基于当前因子表现优选出的股票池。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_98.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_100.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_102.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_104.png)

## 为调仓操作做准备：识别需卖出的股票
在真正的交易调仓前，我们还需要比较新选出的股票池与当前持仓的差异，以确定需要卖出哪些股票。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_106.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_108.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_110.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_112.png)

具体思路是：找出当前持仓中，但不在新股票池`stock_list`中的股票。这些股票就是我们需要卖出的标的。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_114.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_116.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_118.png)

以下是实现的代码逻辑：
```python
# 获取当前账户的所有持仓股票
current_holdings = list(context.portfolio.positions.keys())

![](img/7d638fe78cb8082964fe3061fc8cd8c0_120.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_122.png)

# 计算当前持仓与新股票池的差集，即需要卖出的股票列表
stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
```

![](img/7d638fe78cb8082964fe3061fc8cd8c0_124.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_126.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_128.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_130.png)

这段代码执行后，`stocks_to_sell`列表中就包含了所有需要从当前持仓中移除的股票代码。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_132.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_134.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_136.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_138.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_140.png)

首先，我们回顾并执行了因子的预处理流程，包括离群值处理、标准化和中性化。接着，我们基于处理后的因子值，通过设定分位数阈值，筛选出了目标股票池。最后，我们通过计算持仓与目标池的差异，识别出了需要卖出的股票列表，为下一节课实现具体的买卖交易指令打下了基础。

![](img/7d638fe78cb8082964fe3061fc8cd8c0_142.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_144.png)

![](img/7d638fe78cb8082964fe3061fc8cd8c0_146.png)

至此，股票筛选的核心逻辑已构建完成。