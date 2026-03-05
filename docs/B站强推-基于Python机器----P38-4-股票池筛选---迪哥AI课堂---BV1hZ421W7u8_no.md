# 机器学习量化交易实战：P38：4-股票池筛选

![](img/ef0cd8ecee4737853df4a032399bd3e2_0.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_2.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_4.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_6.png)

## 概述

![](img/ef0cd8ecee4737853df4a032399bd3e2_8.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_10.png)

在本节课程中，我们将学习如何基于预处理后的因子数据，对股票池进行筛选。我们将完成从因子处理到最终选股、调仓的完整流程，包括去除离群值、标准化、中性化，以及根据因子值筛选特定股票，并识别需要从持仓中移除的股票。

![](img/ef0cd8ecee4737853df4a032399bd3e2_12.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_14.png)

## 因子预处理流程回顾

![](img/ef0cd8ecee4737853df4a032399bd3e2_16.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_18.png)

上一节我们介绍了因子的中性化操作。本节中，我们将把预处理流程串联起来，并基于处理后的因子进行股票筛选。

首先，我们需要获取基础因子数据，并将其转换为DataFrame格式，以便后续操作。

![](img/ef0cd8ecee4737853df4a032399bd3e2_20.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_22.png)

```python
fundamental = get_fundamentals(...).to_df()
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_24.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_26.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_28.png)

以下是完整的预处理步骤，我们将依次执行：

![](img/ef0cd8ecee4737853df4a032399bd3e2_30.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_32.png)

### 1. 去除离群值

![](img/ef0cd8ecee4737853df4a032399bd3e2_34.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_36.png)

第一步是对因子数据进行去极值处理，我们使用之前定义好的三西格玛方法。

![](img/ef0cd8ecee4737853df4a032399bd3e2_38.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_40.png)

```python
factor_no_outlier = winsorize_3sigma(fundamental['pb_ratio'])
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_42.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_44.png)

经过此步骤，我们的因子指标中不再包含极端值。

![](img/ef0cd8ecee4737853df4a032399bd3e2_46.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_48.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_50.png)

### 2. 标准化处理

![](img/ef0cd8ecee4737853df4a032399bd3e2_52.png)

去除离群值之后，我们需要对数据进行标准化，使其符合均值为0、标准差为1的分布。

![](img/ef0cd8ecee4737853df4a032399bd3e2_54.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_56.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_58.png)

```python
factor_standardized = standardize(factor_no_outlier)
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_60.png)

这相当于执行了一个标准化的操作。

![](img/ef0cd8ecee4737853df4a032399bd3e2_62.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_64.png)

### 3. 行业市值中性化

![](img/ef0cd8ecee4737853df4a032399bd3e2_66.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_68.png)

做完标准化操作之后，接下来还需要进行中性化处理，以消除行业和市值对因子的影响。

![](img/ef0cd8ecee4737853df4a032399bd3e2_70.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_72.png)

```python
factor_neutralized = neutralize(factor_standardized, fundamental['market_cap'])
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_74.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_76.png)

现在，我们就完成了因子所需的所有预处理操作。

![](img/ef0cd8ecee4737853df4a032399bd3e2_78.png)

## 基于因子进行股票筛选

![](img/ef0cd8ecee4737853df4a032399bd3e2_80.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_82.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_84.png)

接下来，我们将基于处理好的因子对股票池进行筛选。我们的目标是选出因子值较小（例如市净率较低）的股票，因为通常认为这类股票可能有更高的预期收益。

![](img/ef0cd8ecee4737853df4a032399bd3e2_86.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_88.png)

首先，我们需要确定一个筛选阈值。例如，我们希望选出因子值最小的前20%的股票。

![](img/ef0cd8ecee4737853df4a032399bd3e2_90.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_92.png)

```python
# 计算20%分位点的值
threshold = factor_neutralized.quantile(0.2)
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_94.png)

然后，我们根据这个阈值对股票进行筛选。判断逻辑是：因子值小于等于该阈值的股票被选中。

![](img/ef0cd8ecee4737853df4a032399bd3e2_96.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_98.png)

```python
selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_100.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_102.png)

这样，我们就得到了一个股票列表 `selected_stocks`，它包含了所有满足我们筛选条件的股票代码。我们将这个列表存入上下文 `context` 中，供后续调仓使用。

![](img/ef0cd8ecee4737853df4a032399bd3e2_104.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_106.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_108.png)

```python
context.stock_list = selected_stocks
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_110.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_112.png)

## 调仓操作：识别需卖出的股票

![](img/ef0cd8ecee4737853df4a032399bd3e2_114.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_116.png)

有了新的目标股票池后，我们需要执行调仓操作。这包括买入新选中的股票，并卖出不再存在于目标池中的原有持仓。

![](img/ef0cd8ecee4737853df4a032399bd3e2_118.png)

因此，首先要识别出哪些当前持有的股票需要被卖出。这些股票是那些存在于我们原有持仓中，但不在新 `stock_list` 里的股票。

![](img/ef0cd8ecee4737853df4a032399bd3e2_120.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_122.png)

我们假设可以通过 `context.portfolio.positions` 获取当前持仓的股票列表。

![](img/ef0cd8ecee4737853df4a032399bd3e2_124.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_126.png)

```python
# 获取当前持仓的股票列表
current_holdings = list(context.portfolio.positions.keys())
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_128.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_130.png)

然后，我们计算当前持仓与新股票池的差集。差集的结果就是那些需要被卖出的股票。

![](img/ef0cd8ecee4737853df4a032399bd3e2_132.png)

```python
# 找出需要卖出的股票：在当前持仓中但不在新股票池里的股票
stocks_to_sell = set(current_holdings).difference(set(context.stock_list))
```

![](img/ef0cd8ecee4737853df4a032399bd3e2_134.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_136.png)

至此，我们完成了股票筛选的核心逻辑，得到了目标买入列表 `context.stock_list` 和需要卖出的列表 `stocks_to_sell`。在后续的交易函数中，就可以基于这两个列表执行具体的买入和卖出操作。

![](img/ef0cd8ecee4737853df4a032399bd3e2_138.png)

## 总结

![](img/ef0cd8ecee4737853df4a032399bd3e2_140.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_142.png)

本节课中我们一起学习了股票池筛选的完整流程。
1.  **回顾了因子预处理**：包括去极值、标准化和中性化。
2.  **实现了基于因子的筛选**：通过设定分位数阈值，筛选出因子值较小的股票构成目标股票池。
3.  **准备了调仓数据**：通过计算持仓与目标池的差集，识别出需要卖出的股票列表。

![](img/ef0cd8ecee4737853df4a032399bd3e2_144.png)

![](img/ef0cd8ecee4737853df4a032399bd3e2_146.png)

这些步骤为构建一个完整的量化交易策略奠定了关键基础，下一步我们将在此基础上实现具体的交易下单逻辑。