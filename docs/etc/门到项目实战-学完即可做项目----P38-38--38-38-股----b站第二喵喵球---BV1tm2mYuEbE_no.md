# 量化交易实战：P38：股票池筛选

![](img/586523f549bd42fd01ec3fa0e0cb5efa_0.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_2.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_4.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_6.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_8.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_10.png)

## 概述
在本节课中，我们将学习如何基于处理好的因子数据，对股票池进行筛选，并确定需要调仓的股票列表。这是构建量化交易策略中执行交易决策的关键一步。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_12.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_14.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_16.png)

上一节我们完成了因子的预处理操作，包括去极值、标准化和中性化。本节中我们来看看如何利用处理后的因子值来筛选出我们想要交易的股票。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_18.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_20.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_22.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_24.png)

## 因子预处理结果回顾
首先，我们需要获取经过预处理后的因子数据。在代码中，我们通过一个函数得到了包含所需指标的 `DataFrame`。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_26.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_28.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_30.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_32.png)

```python
fundamental_df = get_fundamental_data()
```

![](img/586523f549bd42fd01ec3fa0e0cb5efa_34.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_36.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_38.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_40.png)

这个 `DataFrame` 包含了我们进行股票筛选所需的核心因子数据。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_42.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_44.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_46.png)

## 设定筛选阈值
接下来，我们需要确定一个筛选标准。例如，如果我们认为因子值较小的股票未来表现可能更好，我们可以选择因子值排名在前20%的股票。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_48.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_50.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_52.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_54.png)

以下是设定筛选阈值的步骤：
1.  计算因子的分位数。
2.  确定我们想要的百分位点（例如，20%）。
3.  根据这个阈值生成一个布尔条件，用于筛选股票。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_56.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_58.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_60.png)

```python
# 假设我们的因子列名为 ‘processed_factor’
factor_series = fundamental_df[‘processed_factor’]
# 计算20%分位点的值
threshold_value = factor_series.quantile(0.2)
# 生成筛选条件：选择因子值小于等于该阈值的股票
condition = factor_series <= threshold_value
```

![](img/586523f549bd42fd01ec3fa0e0cb5efa_62.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_64.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_66.png)

## 执行股票筛选
基于上一步生成的布尔条件，我们可以从所有股票中筛选出符合条件的股票代码。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_68.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_70.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_72.png)

```python
# 应用条件进行筛选，得到目标股票列表
selected_stocks = fundamental_df[condition].index.tolist()
```

![](img/586523f549bd42fd01ec3fa0e0cb5efa_74.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_76.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_78.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_80.png)

这样，`selected_stocks` 列表就包含了我们根据因子策略初步选出的股票。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_82.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_84.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_86.png)

## 确定调仓操作
仅仅选出新股票还不够，我们还需要与当前持仓进行比较，以决定具体的买卖操作。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_88.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_90.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_92.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_94.png)

首先，我们需要获取当前的持仓列表。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_96.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_98.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_100.png)

```python
# 从上下文对象中获取当前持仓的股票代码
current_holdings = list(context.portfolio.positions.keys())
```

![](img/586523f549bd42fd01ec3fa0e0cb5efa_102.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_104.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_106.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_108.png)

**注意**：具体的API（如 `context.portfolio.positions`）可能因使用的量化平台（如聚宽、RiceQuant等）而异，需要根据实际情况调整。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_110.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_112.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_114.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_116.png)

然后，比较当前持仓和新选出的股票池，以确定需要卖出和买入的股票。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_118.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_120.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_122.png)

以下是确定调仓操作的逻辑：
1.  **需要卖出的股票**：在当前持仓中，但不在新股票池里的股票。
2.  **需要买入的股票**：在新股票池中，但不在当前持仓里的股票。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_124.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_126.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_128.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_130.png)

```python
# 计算需要卖出的股票（有持仓但未被选中）
stocks_to_sell = list(set(current_holdings) - set(selected_stocks))

![](img/586523f549bd42fd01ec3fa0e0cb5efa_132.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_134.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_136.png)

# 计算需要买入的股票（被选中但尚无持仓）
stocks_to_buy = list(set(selected_stocks) - set(current_holdings))
```

![](img/586523f549bd42fd01ec3fa0e0cb5efa_138.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_140.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_142.png)

## 存储筛选结果
最后，为了方便后续的交易函数使用，我们将筛选出的股票列表存入上下文对象中。

![](img/586523f549bd42fd01ec3fa0e0cb5efa_144.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_146.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_148.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_150.png)

```python
# 将选出的股票池存入context
context.stock_list = selected_stocks
# 也可以存储需要调仓的列表
context.stocks_to_sell = stocks_to_sell
context.stocks_to_buy = stocks_to_buy
```

![](img/586523f549bd42fd01ec3fa0e0cb5efa_152.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_154.png)

![](img/586523f549bd42fd01ec3fa0e0cb5efa_156.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。我们从预处理后的因子数据出发，通过设定阈值筛选出目标股票，然后通过与当前持仓对比，明确了需要卖出和买入的股票列表。这个过程是连接因子分析与实际交易执行的核心桥梁，为下一节课实现具体的交易下单操作打下了基础。