# 量化交易教程：P38：股票池筛选

![](img/59d16f2acc8eb89f97002a9588ef7d9d_0.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_2.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_3.png)

在本节课中，我们将学习如何基于处理后的因子数据，构建一个用于量化交易的股票池。我们将完成从因子预处理到最终股票筛选的完整流程，并实现一个简单的调仓逻辑。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_5.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来筛选股票。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_6.png)

## 因子预处理流程回顾

![](img/59d16f2acc8eb89f97002a9588ef7d9d_8.png)

首先，我们需要对原始因子数据进行一系列预处理操作，以确保数据的质量和可比性。以下是完整的预处理步骤：

![](img/59d16f2acc8eb89f97002a9588ef7d9d_10.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_11.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_12.png)

1.  **离群值处理**：使用3σ原则剔除极端值。
2.  **标准化**：将数据转换为均值为0、标准差为1的标准正态分布。
3.  **中性化**：消除因子与市值、行业等风格因子的相关性。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_14.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_15.png)

我们将按顺序执行这些步骤。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_16.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_17.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_19.png)

```python
# 假设 fundamental 是包含原始因子数据的 DataFrame
# 假设 three_sigma, standardize, neutralize 是已定义好的预处理函数

![](img/59d16f2acc8eb89f97002a9588ef7d9d_20.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_22.png)

# 1. 离群值处理
factor_no_outlier = three_sigma(fundamental, ‘市净率‘)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_23.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_25.png)

# 2. 标准化
factor_standardized = standardize(factor_no_outlier)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_26.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_28.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_29.png)

# 3. 中性化
factor_neutralized = neutralize(factor_standardized)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_30.png)

# 最终处理后的因子
processed_factor = factor_neutralized
```

![](img/59d16f2acc8eb89f97002a9588ef7d9d_32.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_34.png)

## 基于因子进行股票筛选

获得处理后的因子数据后，下一步是根据特定的规则筛选股票。例如，我们可能希望选择市净率因子值最小的前20%的股票，因为较低的市净率可能预示着更高的未来收益。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_36.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_38.png)

以下是筛选逻辑的实现步骤：

首先，我们需要确定筛选的分位点。这里我们选择因子值最小的20%作为股票池。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_40.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_41.png)

```python
# 计算20%分位点的值
threshold = processed_factor.quantile(0.2)
```

![](img/59d16f2acc8eb89f97002a9588ef7d9d_42.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_43.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_44.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_45.png)

接下来，我们根据这个阈值筛选出符合条件的股票代码。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_46.png)

```python
# 筛选出因子值小于等于阈值的股票
selected_stocks = processed_factor[processed_factor <= threshold].index.tolist()

![](img/59d16f2acc8eb89f97002a9588ef7d9d_48.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_49.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_50.png)

# 将筛选结果存入上下文变量中，供后续交易逻辑使用
context.stock_list = selected_stocks
```

![](img/59d16f2acc8eb89f97002a9588ef7d9d_51.png)

## 实现调仓逻辑

![](img/59d16f2acc8eb89f97002a9588ef7d9d_53.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_54.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_55.png)

在量化策略中，我们需要根据最新的股票池来调整持仓。这意味着需要卖出不在新股票池中的股票，并买入新股票池中我们尚未持有的股票。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_56.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_58.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_59.png)

以下是实现调仓的关键步骤：

![](img/59d16f2acc8eb89f97002a9588ef7d9d_60.png)

首先，我们需要找出当前持仓中哪些股票不在新的目标股票池里，这些股票需要被卖出。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_62.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_63.png)

```python
# 获取当前账户的所有持仓股票代码
current_holdings = list(context.portfolio.positions.keys())

![](img/59d16f2acc8eb89f97002a9588ef7d9d_64.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_65.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_66.png)

# 计算需要卖出的股票：当前持有但不在新股票池中的股票
stocks_to_sell = set(current_holdings).difference(set(context.stock_list))
```

![](img/59d16f2acc8eb89f97002a9588ef7d9d_68.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_70.png)

然后，对于需要卖出的股票，执行卖出操作。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_71.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_72.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_74.png)

```python
# 遍历需要卖出的股票列表
for stock in stocks_to_sell:
    # 获取该股票的当前持仓数量
    current_position = context.portfolio.positions[stock].amount
    # 如果持仓数量大于0，则执行市价卖出
    if current_position > 0:
        order_target_value(stock, 0)  # 将目标持仓调整为0，即全部卖出
```

![](img/59d16f2acc8eb89f97002a9588ef7d9d_76.png)

最后，对于新股票池中的股票，如果我们尚未持有或持仓不足，则执行买入操作。一个简单的等权重分配方法是将可用资金平均分配到每只目标股票上。

![](img/59d16f2acc8eb89f97002a9588ef7d9d_78.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_79.png)

```python
# 计算可用于购买新股票的总资金
available_cash = context.portfolio.cash

![](img/59d16f2acc8eb89f97002a9588ef7d9d_81.png)

# 计算每只股票应分配的资金（等权重）
cash_per_stock = available_cash / len(context.stock_list)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_83.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_84.png)

# 遍历目标股票池
for stock in context.stock_list:
    # 执行买入操作，目标价值为计算出的每份资金
    order_target_value(stock, cash_per_stock)
```

![](img/59d16f2acc8eb89f97002a9588ef7d9d_85.png)

![](img/59d16f2acc8eb89f97002a9588ef7d9d_87.png)

本节课中我们一起学习了量化交易中股票池筛选与调仓的完整流程。我们从因子预处理出发，通过去极值、标准化和中性化确保了因子的有效性。接着，我们基于处理后的因子设定了筛选规则（如前20%），构建了目标股票池。最后，我们实现了核心的调仓逻辑，通过比较当前持仓与目标股票池，自动执行卖出和买入操作，使持仓结构与策略信号保持一致。这个过程是构建自动化量化交易策略的基础环节。