# 人工智能数据挖掘实战：P38：股票池筛选 📊

![](img/e9caec68906d2540dab896771fb00e47_0.png)

![](img/e9caec68906d2540dab896771fb00e47_2.png)

![](img/e9caec68906d2540dab896771fb00e47_3.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并构建一个简单的选股策略。我们将完成从因子预处理到生成最终股票列表的完整流程。

![](img/e9caec68906d2540dab896771fb00e47_5.png)

![](img/e9caec68906d2540dab896771fb00e47_6.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子进行股票筛选。

## 因子预处理流程回顾 🔄

![](img/e9caec68906d2540dab896771fb00e47_8.png)

![](img/e9caec68906d2540dab896771fb00e47_9.png)

![](img/e9caec68906d2540dab896771fb00e47_11.png)

首先，我们需要对原始因子数据执行一系列预处理操作，包括去除离群值、标准化和中性化。以下是完整的预处理步骤：

![](img/e9caec68906d2540dab896771fb00e47_13.png)

![](img/e9caec68906d2540dab896771fb00e47_14.png)

![](img/e9caec68906d2540dab896771fb00e47_16.png)

1.  **去除离群值**：使用3σ原则处理因子数据中的极端值。
    ```python
    fundamental = factor_data.winsorize(factor=‘pb’)
    ```
2.  **标准化**：将因子数据转换为均值为0、标准差为1的标准正态分布。
    ```python
    standardized = fundamental.standardize()
    ```
3.  **中性化**：消除因子与市值、行业等风格因子的相关性。
    ```python
    preprocessed_factor = standardized.neutralize()
    ```

![](img/e9caec68906d2540dab896771fb00e47_17.png)

![](img/e9caec68906d2540dab896771fb00e47_19.png)

![](img/e9caec68906d2540dab896771fb00e47_20.png)

完成上述步骤后，我们得到了一个经过清洗和调整的、可用于选股的因子 `preprocessed_factor`。

![](img/e9caec68906d2540dab896771fb00e47_21.png)

![](img/e9caec68906d2540dab896771fb00e47_23.png)

## 基于因子进行股票筛选 📈

接下来，我们将利用处理后的因子对股票池进行筛选。我们的目标是选出因子值最小的前20%的股票，因为在本例中，我们假设较低的市净率（PB）可能预示着更高的未来收益。

![](img/e9caec68906d2540dab896771fb00e47_25.png)

以下是筛选股票池的具体步骤：

![](img/e9caec68906d2540dab896771fb00e47_27.png)

1.  **确定分位数**：计算因子的20%分位点，作为筛选阈值。
    ```python
    threshold = preprocessed_factor.quantile(0.2)
    ```
2.  **执行筛选**：选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = preprocessed_factor[preprocessed_factor <= threshold].index
    ```
3.  **存储结果**：将筛选出的股票列表存入上下文变量中，供后续调仓使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/e9caec68906d2540dab896771fb00e47_28.png)

![](img/e9caec68906d2540dab896771fb00e47_29.png)

![](img/e9caec68906d2540dab896771fb00e47_30.png)

![](img/e9caec68906d2540dab896771fb00e47_31.png)

![](img/e9caec68906d2540dab896771fb00e47_32.png)

至此，我们得到了一个基于因子策略筛选出的目标股票池 `context.stock_list`。

![](img/e9caec68906d2540dab896771fb00e47_33.png)

![](img/e9caec68906d2540dab896771fb00e47_34.png)

## 准备调仓：识别需卖出的股票 💸

![](img/e9caec68906d2540dab896771fb00e47_36.png)

![](img/e9caec68906d2540dab896771fb00e47_37.png)

![](img/e9caec68906d2540dab896771fb00e47_38.png)

在实施买入操作前，我们需要先处理当前持仓中不在新股票池里的股票，即需要卖出的股票。

![](img/e9caec68906d2540dab896771fb00e47_40.png)

![](img/e9caec68906d2540dab896771fb00e47_41.png)

![](img/e9caec68906d2540dab896771fb00e47_42.png)

以下是识别待卖出股票的逻辑：

![](img/e9caec68906d2540dab896771fb00e47_43.png)

![](img/e9caec68906d2540dab896771fb00e47_45.png)

![](img/e9caec68906d2540dab896771fb00e47_46.png)

我们需要找出当前持仓股票与目标股票池之间的差异。具体来说，是找出那些“当前持有但不在新股票池中”的股票。

![](img/e9caec68906d2540dab896771fb00e47_48.png)

```python
# 获取当前账户的所有持仓股票
current_holdings = set(context.portfolio.positions.keys())
# 获取目标股票池
target_pool = set(context.stock_list)
# 计算需要卖出的股票（在持仓中但不在目标池中）
stocks_to_sell = current_holdings.difference(target_pool)
```

![](img/e9caec68906d2540dab896771fb00e47_50.png)

![](img/e9caec68906d2540dab896771fb00e47_51.png)

变量 `stocks_to_sell` 现在包含了所有需要清仓卖出的股票代码集合。

![](img/e9caec68906d2540dab896771fb00e47_53.png)

![](img/e9caec68906d2540dab896771fb00e47_55.png)

## 总结 📝

![](img/e9caec68906d2540dab896771fb00e47_57.png)

本节课中我们一起学习了股票池筛选的完整流程。

![](img/e9caec68906d2540dab896771fb00e47_59.png)

![](img/e9caec68906d2540dab896771fb00e47_61.png)

我们首先回顾并执行了因子的预处理流程，包括去极值、标准化和中性化。接着，我们基于处理后的因子值，通过设定分位数阈值筛选出了目标股票组合。最后，我们通过集合运算，识别出了当前持仓中需要卖出的股票，为下一步执行具体的买卖交易指令做好了准备。

![](img/e9caec68906d2540dab896771fb00e47_62.png)

![](img/e9caec68906d2540dab896771fb00e47_63.png)

![](img/e9caec68906d2540dab896771fb00e47_65.png)

通过这一系列操作，我们构建了一个从因子分析到投资组合构建的基本框架。