# Python金融量化交易：P38：股票池筛选

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_0.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_2.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_4.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_6.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_8.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_10.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_12.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并准备执行调仓操作。我们将完成因子预处理后的应用，包括筛选出符合特定条件的股票，并识别出需要从当前持仓中移除的股票。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_14.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_16.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_18.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_20.png)

上一节我们完成了因子的中性化处理，本节中我们来看看如何利用处理好的因子进行股票筛选。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_22.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_24.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_26.png)

## 因子预处理流程回顾与执行
现在，我们已经完成了中性化操作。接下来，我们可以执行之前编写好的几个预处理步骤。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_28.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_30.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_32.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_34.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_36.png)

以下是具体的执行步骤：

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_38.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_40.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_42.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_44.png)

1.  **去除离群值**：首先，对因子数据应用三西格玛法则，以去除极端值。
    ```python
    fundamental = fundamental_dataframe  # 假设 fundamental_dataframe 是包含所需指标（如市净率）的DataFrame
    factor_series = fundamental['target_factor']  # 例如，市净率因子
    factor_no_outliers = three_sigma(factor_series)  # 应用三西格玛去极值函数
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_46.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_48.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_50.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_52.png)

2.  **数据标准化**：对去除离群值后的因子数据进行标准化处理，使其符合标准正态分布。
    ```python
    factor_standardized = standardize(factor_no_outliers)  # 应用标准化函数
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_54.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_56.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_58.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_60.png)

3.  **因子中性化**：最后，对标准化后的因子进行中性化处理，以消除市值等风格因子的影响。
    ```python
    factor_neutralized = neutralize(factor_standardized, market_cap_series)  # 应用中性化函数，传入市值序列
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_62.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_64.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_66.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_68.png)

现在，我们就完成了因子所需的所有预处理操作。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_70.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_72.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_74.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_76.png)

## 基于因子进行股票筛选
接下来，我们需要基于处理好的因子对股票池进行筛选。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_78.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_80.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_82.png)

首先，需要确定筛选逻辑。例如，对于市净率因子，通常认为值较小的股票未来收益期望可能更高。因此，我们可以选择因子值最小的前20%的股票。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_84.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_86.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_88.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_90.png)

以下是筛选操作：

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_92.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_94.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_96.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_98.png)

1.  **设定筛选阈值**：我们选择因子值最小的20%作为股票池。
    ```python
    threshold = factor_neutralized.quantile(0.2)  # 计算20%分位点的值
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_100.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_102.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_104.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_106.png)

2.  **执行筛选**：根据阈值筛选出符合条件的股票代码。
    ```python
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_108.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_110.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_112.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_114.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_116.png)

这样，我们就得到了一个股票列表 `selected_stocks`，它包含了我们想要交易的股票。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_118.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_120.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_122.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_124.png)

## 准备调仓：识别需卖出的股票
在调仓之前，我们需要比较新选出的股票池和当前的持仓，找出需要卖出的股票。

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_126.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_128.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_130.png)

以下是识别需卖出股票的步骤：

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_132.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_134.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_136.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_138.png)

1.  **获取当前持仓**：首先，从上下文信息中获取当前账户持有的所有股票。
    ```python
    current_holdings = context.portfolio.positions.keys()  # 获取当前持仓股票代码列表
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_140.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_142.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_144.png)

2.  **计算差异**：找出在当前持仓中，但不在新股票池 `selected_stocks` 中的股票。这些就是需要卖出的股票。
    ```python
    stocks_to_sell = set(current_holdings).difference(set(selected_stocks))
    ```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_146.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_148.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_150.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_152.png)

我们将需要卖出的股票列表 `stocks_to_sell` 保存下来，以便在后续的交易函数中执行卖出操作。同时，将新选出的股票池 `selected_stocks` 存入上下文 `context` 中，供后续使用。
```python
context.stock_list = selected_stocks
```

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_154.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_156.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_158.png)

![](img/a2e0e86fac7779c3d2b15ee7a303c3ae_160.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。我们首先回顾并执行了因子的预处理步骤，包括去极值、标准化和中性化。然后，基于处理后的因子设定了筛选条件，选出了目标股票池。最后，通过比较新股票池与当前持仓，识别出了需要卖出的股票，为下一步执行具体的买卖交易指令做好了准备。