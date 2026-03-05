# Python金融分析与量化交易实战教程：P36：04-4-股票池筛选

![](img/2f4a6028ffd280e59c2b0654d493818d_1.png)

![](img/2f4a6028ffd280e59c2b0654d493818d_3.png)

## 概述
在本节课程中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子预处理到最终股票筛选的完整流程。

![](img/2f4a6028ffd280e59c2b0654d493818d_5.png)

![](img/2f4a6028ffd280e59c2b0654d493818d_7.png)

## 因子预处理操作回顾
上一节我们介绍了因子的中性化操作。现在，我们将整合所有预处理步骤，包括离群值处理、标准化和中性化。

![](img/2f4a6028ffd280e59c2b0654d493818d_9.png)

以下是完整的预处理操作流程：

1.  **离群值处理**：使用三西格玛方法处理因子数据中的极端值。
2.  **标准化**：将处理后的因子数据转换为均值为0、标准差为1的标准分布。
3.  **中性化**：消除因子数据中与市值相关的系统性影响。

## 执行预处理流程
现在，我们来执行上述预处理操作。首先，我们需要获取基础因子数据。

![](img/2f4a6028ffd280e59c2b0654d493818d_11.png)

```python
# 获取基础因子数据（例如市净率）
fundamental = get_fundamentals(...).to_dataframe()
```

![](img/2f4a6028ffd280e59c2b0654d493818d_13.png)

![](img/2f4a6028ffd280e59c2b0654d493818d_15.png)

接下来，我们按顺序应用预处理函数。假设我们已经定义了 `remove_outliers_3sigma`、`standardize` 和 `neutralize` 这三个函数。

![](img/2f4a6028ffd280e59c2b0654d493818d_17.png)

```python
# 1. 离群值处理
factor_no_outliers = remove_outliers_3sigma(fundamental[‘pb_ratio’])

![](img/2f4a6028ffd280e59c2b0654d493818d_19.png)

![](img/2f4a6028ffd280e59c2b0654d493818d_21.png)

# 2. 标准化
factor_standardized = standardize(factor_no_outliers)

![](img/2f4a6028ffd280e59c2b0654d493818d_23.png)

![](img/2f4a6028ffd280e59c2b0654d493818d_25.png)

# 3. 中性化（以市值为中性化基准）
factor_neutralized = neutralize(factor_standardized, fundamental[‘market_cap’])

![](img/2f4a6028ffd280e59c2b0654d493818d_27.png)

# 将最终处理后的因子保存起来
processed_factor = factor_neutralized
```

至此，我们完成了因子的所有预处理操作，得到了一个经过清洗和调整的、可用于选股的因子序列。

![](img/2f4a6028ffd280e59c2b0654d493818d_29.png)

## 基于因子进行股票筛选
上一节我们完成了因子的预处理，本节中我们来看看如何利用处理后的因子对股票池进行筛选。

我们的目标是：根据因子值，筛选出排名靠前（例如，因子值最小的前20%）的股票，构建我们的目标股票池。

以下是筛选股票池的具体步骤：

1.  **确定筛选分位数**：计算因子的特定分位点，作为筛选阈值。
2.  **应用筛选条件**：根据阈值，选出符合条件的股票代码。
3.  **存储目标股票池**：将筛选出的股票列表保存到上下文变量中，供后续调仓使用。

![](img/2f4a6028ffd280e59c2b0654d493818d_31.png)

![](img/2f4a6028ffd280e59c2b0654d493818d_33.png)

```python
# 确定筛选阈值，例如选择因子值最小的20%的股票
threshold = processed_factor.quantile(0.2)

![](img/2f4a6028ffd280e59c2b0654d493818d_35.png)

# 应用筛选条件，选出因子值小于等于阈值的股票
target_stocks = processed_factor[processed_factor <= threshold].index.tolist()

![](img/2f4a6028ffd280e59c2b0654d493818d_37.png)

# 将目标股票池存储到 context 中
context.stock_list = target_stocks
```

![](img/2f4a6028ffd280e59c2b0654d493818d_39.png)

现在，`context.stock_list` 中包含了我们根据因子策略筛选出的目标股票。

## 执行调仓操作
在确定了目标股票池之后，接下来我们需要执行具体的买卖操作，使实际持仓与目标股票池保持一致。

![](img/2f4a6028ffd280e59c2b0654d493818d_41.png)

调仓逻辑的核心是：卖出当前持有但不在目标股票池中的股票，买入在目标股票池中但当前未持有的股票。

![](img/2f4a6028ffd280e59c2b0654d493818d_43.png)

首先，我们需要识别出需要卖出的股票。以下是识别卖出股票列表的方法：

![](img/2f4a6028ffd280e59c2b0654d493818d_45.png)

```python
# 获取当前账户的所有持仓股票
current_holdings = list(context.portfolio.positions.keys())

# 计算需要卖出的股票：当前持有但不在目标股票池中的股票
stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
```

变量 `stocks_to_sell` 现在包含了所有需要清仓的股票代码。在后续的交易函数中，我们将对这些股票执行卖出操作。关于具体的买卖下单操作，将在接下来的章节中详细展开。

![](img/2f4a6028ffd280e59c2b0654d493818d_47.png)

## 总结
本节课中我们一起学习了股票池筛选与调仓准备的全过程。我们首先回顾并执行了因子的完整预处理流程，包括离群值处理、标准化和中性化。接着，我们基于处理后的因子值，通过设定分位数阈值筛选出了目标股票池。最后，我们通过比较当前持仓与目标股票池，找出了需要卖出的股票列表，为下一步执行具体的交易指令做好了准备。这个过程是量化策略从信号生成到交易执行的关键桥梁。