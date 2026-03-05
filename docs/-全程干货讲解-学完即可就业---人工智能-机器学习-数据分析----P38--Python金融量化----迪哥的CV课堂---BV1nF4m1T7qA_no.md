# Python金融量化：P38：股票池筛选

![](img/2f3f55e0debec479bb185e7b2d59c730_0.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_2.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_4.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_6.png)

在本节课中，我们将学习如何基于预处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将从因子预处理开始，逐步构建一个完整的股票筛选与交易逻辑。

![](img/2f3f55e0debec479bb185e7b2d59c730_8.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_10.png)

## 概述

![](img/2f3f55e0debec479bb185e7b2d59c730_12.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_14.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_16.png)

上一节我们完成了因子的中性化操作。本节中，我们将利用预处理后的因子数据，对股票池进行筛选，并实现一个基础的调仓策略。核心步骤包括：基于因子值筛选股票、识别需要卖出的股票以及为后续交易做准备。

![](img/2f3f55e0debec479bb185e7b2d59c730_18.png)

## 因子预处理流程回顾与执行

![](img/2f3f55e0debec479bb185e7b2d59c730_20.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_22.png)

首先，我们需要执行之前编写好的因子预处理流程。该流程包括去极值、标准化和中性化三个步骤。

![](img/2f3f55e0debec479bb185e7b2d59c730_24.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_26.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_28.png)

以下是预处理操作的核心代码：

![](img/2f3f55e0debec479bb185e7b2d59c730_30.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_32.png)

```python
# 执行去极值操作，例如使用3倍标准差方法
factor_no_outliers = winsorize_med_3sigma(factor_data, 'pb')

![](img/2f3f55e0debec479bb185e7b2d59c730_34.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_36.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_38.png)

# 对去极值后的因子进行标准化
factor_standardized = standardize(factor_no_outliers)

![](img/2f3f55e0debec479bb185e7b2d59c730_40.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_42.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_44.png)

# 对标准化后的因子进行行业市值中性化
factor_neutralized = neutralize(factor_standardized, industry_market_cap_data)
```

![](img/2f3f55e0debec479bb185e7b2d59c730_46.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_48.png)

执行完上述操作后，我们得到了一个经过完整预处理的因子 `factor_neutralized`，它将作为我们筛选股票的依据。

![](img/2f3f55e0debec479bb185e7b2d59c730_50.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_52.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_54.png)

## 基于因子值筛选股票

![](img/2f3f55e0debec479bb185e7b2d59c730_56.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_58.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_60.png)

接下来，我们需要根据预处理后的因子值对股票进行筛选。我们的策略是选择因子值较小的股票，因为在本例中，较低的市净率（PB）可能预示着更高的预期收益。

![](img/2f3f55e0debec479bb185e7b2d59c730_62.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_64.png)

以下是筛选操作的具体步骤：

![](img/2f3f55e0debec479bb185e7b2d59c730_66.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_68.png)

首先，我们需要确定一个筛选阈值。例如，我们选择因子值从小到大排序，位于前20%的股票。

![](img/2f3f55e0debec479bb185e7b2d59c730_70.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_72.png)

```python
# 计算20%分位点的因子值
threshold = factor_neutralized.quantile(0.2)
```

![](img/2f3f55e0debec479bb185e7b2d59c730_74.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_76.png)

然后，我们根据这个阈值筛选出符合条件的股票代码。

![](img/2f3f55e0debec479bb185e7b2d59c730_78.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_80.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_82.png)

```python
# 筛选出因子值小于等于阈值的股票
selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
```

![](img/2f3f55e0debec479bb185e7b2d59c730_84.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_86.png)

筛选完成后，我们将选中的股票列表存入上下文变量 `context` 中，以便后续交易函数调用。

![](img/2f3f55e0debec479bb185e7b2d59c730_88.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_90.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_92.png)

```python
context.stock_list = selected_stocks
```

![](img/2f3f55e0debec479bb185e7b2d59c730_94.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_96.png)

## 识别并处理调仓股票

![](img/2f3f55e0debec479bb185e7b2d59c730_98.png)

在每次调仓时，我们需要对比新选出的股票池和当前已持有的股票，以决定需要卖出哪些股票。

![](img/2f3f55e0debec479bb185e7b2d59c730_100.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_102.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_104.png)

以下是识别待卖出股票的逻辑：

![](img/2f3f55e0debec479bb185e7b2d59c730_106.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_108.png)

我们需要获取当前投资组合中持有的所有股票。

![](img/2f3f55e0debec479bb185e7b2d59c730_110.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_112.png)

```python
# 获取当前持仓的股票代码列表
current_holdings = list(context.portfolio.positions.keys())
```

![](img/2f3f55e0debec479bb185e7b2d59c730_114.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_116.png)

接着，我们找出那些当前持有但不在新股票池中的股票，这些股票就是本次调仓需要卖出的对象。

![](img/2f3f55e0debec479bb185e7b2d59c730_118.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_120.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_122.png)

```python
# 计算需要卖出的股票：当前持有但不在新选股票池中
stocks_to_sell = set(current_holdings) - set(context.stock_list)
```

![](img/2f3f55e0debec479bb185e7b2d59c730_124.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_126.png)

至此，我们完成了股票池的筛选和待调整持仓的识别。`context.stock_list` 包含了计划买入或继续持有的股票，而 `stocks_to_sell` 则包含了需要清仓的股票。

![](img/2f3f55e0debec479bb185e7b2d59c730_128.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_130.png)

## 总结

![](img/2f3f55e0debec479bb185e7b2d59c730_132.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_134.png)

本节课中我们一起学习了股票池筛选的完整流程。

![](img/2f3f55e0debec479bb185e7b2d59c730_136.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_138.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_140.png)

1.  **回顾并执行了因子预处理**，包括去极值、标准化和中性化，为筛选提供了干净、可比的数据。
2.  **基于预处理因子进行股票筛选**，通过设定分位数阈值，选出了符合策略逻辑（如低市净率）的股票组合。
3.  **识别调仓操作目标**，通过对比新旧股票池，明确了需要卖出的股票列表，为下一节实现具体的买卖交易指令打下了基础。

![](img/2f3f55e0debec479bb185e7b2d59c730_142.png)

![](img/2f3f55e0debec479bb185e7b2d59c730_144.png)

通过以上步骤，我们构建了一个从数据预处理到投资决策的逻辑闭环，这是量化交易策略中核心的选股与组合管理环节。