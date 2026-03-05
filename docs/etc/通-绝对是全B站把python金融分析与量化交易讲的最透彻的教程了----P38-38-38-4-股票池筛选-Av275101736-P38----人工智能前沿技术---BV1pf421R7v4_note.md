# Python金融量化与股票交易：P38：股票池筛选

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_0.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_2.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_4.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_6.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_8.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_10.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_12.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选。我们将完成因子预处理后的最后一步，即根据因子值选择目标股票，并准备进行后续的调仓操作。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_14.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_16.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_18.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_20.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来筛选股票。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_22.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_24.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_26.png)

## 因子预处理流程回顾与执行
首先，我们需要执行之前编写好的因子预处理流程。该流程包括三个步骤：去除离群值、标准化和中性化。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_28.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_30.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_32.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_34.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_36.png)

以下是具体的代码执行步骤：

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_38.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_40.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_42.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_44.png)

1.  **获取基础数据**：首先，我们从`fundamental`数据中获取所需的因子指标（例如市净率），并将其转换为DataFrame格式。
    ```python
    fundamental = get_fundamentals(...).T
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_46.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_48.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_50.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_52.png)

2.  **去除离群值**：对因子数据应用三西格玛法则，以消除极端值的影响。
    ```python
    factor_no_outliers = winsorize(fundamental['pb_ratio'])
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_54.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_56.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_58.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_60.png)

3.  **标准化**：将去除离群值后的数据进行标准化处理，使其均值为0，标准差为1。
    ```python
    factor_standardized = standardize(factor_no_outliers)
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_62.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_64.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_66.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_68.png)

4.  **中性化**：最后，对标准化后的因子进行中性化处理，以消除市值等风格因子的影响。
    ```python
    factor_neutralized = neutralization(factor_standardized, fundamental['market_cap'])
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_70.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_72.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_74.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_76.png)

至此，我们完成了因子`pb_ratio`的所有预处理操作，得到了一个可用于选股的、经过清洗和调整的因子值序列`factor_neutralized`。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_78.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_80.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_82.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_84.png)

## 基于因子进行股票筛选
获得处理后的因子后，下一步是基于它来筛选股票。我们的目标是选择因子值较小（例如，市净率较低）的股票，因为根据之前的分析，这类股票可能具有更高的预期收益。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_86.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_88.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_90.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_92.png)

以下是筛选股票的具体步骤：

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_94.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_96.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_98.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_100.png)

1.  **设定筛选阈值**：我们需要确定一个分位点，例如选择因子值最小的前20%的股票。
    ```python
    threshold = factor_neutralized.quantile(0.2)
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_102.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_104.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_106.png)

2.  **执行筛选**：根据设定的阈值，筛选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
    ```
    这行代码会返回一个股票代码列表`selected_stocks`，即我们本轮希望持有的股票池。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_108.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_110.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_112.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_114.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_116.png)

3.  **存储筛选结果**：将筛选出的股票列表存入上下文变量`context`中，供后续调仓函数使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_118.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_120.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_122.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_124.png)

## 确定调仓操作：识别需卖出的股票
在量化交易中，调仓不仅涉及买入新股票，还包括卖出不再符合持仓条件的股票。因此，我们需要比较当前持仓与新的目标股票池。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_126.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_128.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_130.png)

以下是识别需要卖出股票的方法：

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_132.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_134.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_136.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_138.png)

1.  **获取当前持仓**：首先，从账户信息中获取当前持有的所有股票列表。
    ```python
    current_holdings = context.portfolio.positions.keys()
    ```

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_140.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_142.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_144.png)

2.  **计算差异**：通过集合运算，找出当前持有但不在新目标股票池`context.stock_list`中的股票。这些股票就是本次调仓需要卖出的对象。
    ```python
    stocks_to_sell = set(current_holdings).difference(context.stock_list)
    ```
    这里使用了集合的`difference`方法，它返回在第一个集合（当前持仓）中但不在第二个集合（目标股票池）中的元素。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_146.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_148.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_150.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_152.png)

至此，我们成功完成了股票池的筛选工作，并明确了需要买入（`context.stock_list`中的股票）和需要卖出（`stocks_to_sell`中的股票）的对象，为下一节课编写具体的交易下单逻辑做好了准备。

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_154.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_156.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_158.png)

![](img/2e376ae5c60ca7b22822e54a2ad6ef51_160.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。我们首先回顾并执行了因子的预处理操作，包括去极值、标准化和中性化。接着，我们基于处理后的因子值，通过设定分位数阈值筛选出了目标股票组合。最后，我们通过比较当前持仓与目标组合，识别出了需要卖出的股票列表。这个过程是量化策略中选股与调仓的核心环节。