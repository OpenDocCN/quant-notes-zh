# 量化投资入门：P38：股票池筛选 📊

![](img/61863faec83f0bc4d7619249c6595ab6_0.png)

![](img/61863faec83f0bc4d7619249c6595ab6_2.png)

![](img/61863faec83f0bc4d7619249c6595ab6_4.png)

![](img/61863faec83f0bc4d7619249c6595ab6_6.png)

在本节课中，我们将学习如何基于处理后的因子数据，构建一个用于投资的股票池。我们将完成从因子预处理到股票筛选，再到模拟调仓的完整流程。

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来筛选股票。

![](img/61863faec83f0bc4d7619249c6595ab6_8.png)

## 因子预处理流程回顾

![](img/61863faec83f0bc4d7619249c6595ab6_10.png)

![](img/61863faec83f0bc4d7619249c6595ab6_12.png)

首先，我们需要对原始因子数据进行一系列预处理操作，包括去除离群值、标准化和中性化。以下是完整的预处理步骤：

![](img/61863faec83f0bc4d7619249c6595ab6_14.png)

![](img/61863faec83f0bc4d7619249c6595ab6_16.png)

1.  **去除离群值**：使用 `winsorize` 方法处理因子数据中的极端值。
2.  **标准化**：使用 `standardize` 方法将因子数据转换为均值为0、标准差为1的标准分布。
3.  **中性化**：使用 `neutralize` 方法消除因子对行业或市值等风格的暴露。

![](img/61863faec83f0bc4d7619249c6595ab6_18.png)

![](img/61863faec83f0bc4d7619249c6595ab6_20.png)

![](img/61863faec83f0bc4d7619249c6595ab6_22.png)

我们将这些步骤串联起来，形成一个完整的预处理函数。

![](img/61863faec83f0bc4d7619249c6595ab6_24.png)

```python
# 假设 fundamental 是原始的因子DataFrame，sector 是行业分类数据
processed_factor = winsorize(fundamental)
processed_factor = standardize(processed_factor)
processed_factor = neutralize(processed_factor, sector)
```

![](img/61863faec83f0bc4d7619249c6595ab6_26.png)

## 基于因子进行股票筛选

预处理完成后，我们得到一个干净的因子值。接下来，我们需要根据这个因子值来筛选股票。通常，我们希望买入因子值较小（例如市净率较低）的股票。

以下是筛选股票池的具体步骤：

![](img/61863faec83f0bc4d7619249c6595ab6_28.png)

![](img/61863faec83f0bc4d7619249c6595ab6_30.png)

1.  **确定筛选阈值**：计算因子值的分位数，例如选择因子值最小的前20%的股票。
2.  **执行筛选**：根据阈值，从所有股票中筛选出符合条件的股票代码。

![](img/61863faec83f0bc4d7619249c6595ab6_32.png)

```python
# 计算20%分位点的值
threshold = processed_factor.quantile(0.2)
# 筛选出因子值小于等于该阈值的股票
selected_stocks = processed_factor[processed_factor <= threshold].index.tolist()
```

![](img/61863faec83f0bc4d7619249c6595ab6_34.png)

![](img/61863faec83f0bc4d7619249c6595ab6_36.png)

## 模拟交易调仓操作

筛选出目标股票池后，我们需要模拟交易操作，即卖出不在新股票池中的持仓，并买入新股票池中的股票。

![](img/61863faec83f0bc4d7619249c6595ab6_38.png)

![](img/61863faec83f0bc4d7619249c6595ab6_40.png)

以下是调仓逻辑的实现步骤：

![](img/61863faec83f0bc4d7619249c6595ab6_42.png)

1.  **获取当前持仓**：从交易上下文 `context` 中获取当前账户持有的所有股票。
2.  **计算需要卖出的股票**：找出当前持仓中，不在新股票池 `selected_stocks` 里的股票。
3.  **执行卖出操作**：对这部分股票下达卖出指令。
4.  **执行买入操作**：将资金分配到新股票池中的股票上（具体分配策略需另行定义）。

```python
# 获取当前持仓的股票列表
current_holdings = context.portfolio.positions.keys()
# 计算需要卖出的股票：当前有但新池子里没有的股票
stocks_to_sell = set(current_holdings).difference(set(selected_stocks))
# 执行卖出操作（此处为逻辑示意）
for stock in stocks_to_sell:
    order_target_percent(stock, 0)  # 将持仓比例调整为0，即卖出
```

![](img/61863faec83f0bc4d7619249c6595ab6_44.png)

本节课中我们一起学习了量化策略中股票池筛选的核心流程。我们首先回顾并串联了因子预处理的三个步骤，然后学习了如何根据处理后的因子值筛选出目标股票，最后模拟了根据新股票池进行调仓的交易逻辑。这个过程是构建自动化选股策略的基础。