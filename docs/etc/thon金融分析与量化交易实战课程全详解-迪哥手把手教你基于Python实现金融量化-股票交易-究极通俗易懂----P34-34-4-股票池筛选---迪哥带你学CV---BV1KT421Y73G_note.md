# Python金融分析与量化交易实战课程：P34：股票池筛选

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_0.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_2.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_4.png)

## 概述
在本节课中，我们将学习如何基于预处理后的因子数据，对股票池进行筛选。我们将完成从因子数据预处理到最终股票池构建的完整流程，包括离群值处理、标准化、中性化以及基于特定阈值（如前20%）的选股操作。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_6.png)

上一节我们介绍了因子的中性化操作，本节中我们来看看如何利用处理好的因子数据来筛选出我们想要的股票。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_8.png)

## 因子数据预处理
首先，我们需要对原始的因子数据进行一系列预处理操作，以确保数据的质量和可比性。以下是预处理的具体步骤：

### 1. 离群值处理
我们使用三西格玛（3σ）法则来处理因子数据中的离群值。这一步的目的是消除极端值对后续分析的影响。

```python
# 对因子数据应用三西格玛离群值处理
factor_data = remove_outliers_3sigma(factor_data)
```

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_10.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_12.png)

### 2. 数据标准化
处理完离群值后，我们需要对数据进行标准化。标准化可以将不同量纲的数据转换到同一尺度，便于比较和计算。

```python
# 对处理后的因子数据进行标准化
factor_data_standardized = standardize_data(factor_data)
```

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_14.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_16.png)

### 3. 因子中性化
最后，我们对标准化后的因子进行中性化处理，以消除市场或行业等系统性因素的影响。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_18.png)

```python
# 对标准化后的因子进行中性化处理
factor_neutralized = neutralize_factor(factor_data_standardized, market_cap_data)
```

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_20.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_22.png)

完成以上三步后，我们就得到了一个经过完整预处理的、干净的因子数据，可以用于后续的选股策略。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_24.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_26.png)

## 基于因子进行股票筛选
有了处理好的因子数据，接下来我们就可以基于它来筛选股票。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据策略假设，这些股票可能具有更高的预期收益。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_28.png)

以下是筛选股票的具体步骤：

### 1. 确定筛选阈值
我们首先需要确定一个筛选的阈值。例如，我们选择因子值从小到大排序的前20%作为我们的目标股票池。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_30.png)

```python
# 计算因子值的20%分位数，作为筛选阈值
threshold = factor_neutralized.quantile(0.2)
```

### 2. 执行筛选操作
根据计算出的阈值，我们从所有股票中筛选出因子值小于或等于该阈值的股票。

```python
# 筛选出因子值小于等于阈值的股票
selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
```

### 3. 更新股票池
将筛选出的股票列表存储到上下文（context）中，供后续的交易逻辑使用。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_32.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_34.png)

```python
# 将选出的股票列表存入context
context.stock_list = selected_stocks
```

至此，我们基于特定因子完成了股票池的初步筛选。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_36.png)

## 调仓与交易准备
在确定了目标股票池之后，我们需要为实际的交易操作做准备。这涉及到对比当前持仓与目标股票池，并计划需要卖出和买入的股票。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_38.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_40.png)

以下是准备调仓的步骤：

### 1. 识别需要卖出的股票
我们需要找出当前持仓中，但不在新目标股票池里的股票。这些股票将被卖出。

```python
# 获取当前持仓的股票列表
current_holdings = list(context.portfolio.positions.keys())

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_42.png)

# 找出当前持仓中不在目标股票池的股票（需要卖出）
stocks_to_sell = list(set(current_holdings).difference(set(context.stock_list)))
```

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_44.png)

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_46.png)

### 2. 识别需要买入的股票
同样，我们也需要找出在目标股票池中，但当前并未持有的股票。这些股票将被买入。

```python
# 找出在目标股票池中但当前未持有的股票（需要买入）
stocks_to_buy = list(set(context.stock_list).difference(set(current_holdings)))
```

通过以上两步，我们明确了本次调仓需要执行的具体卖出和买入操作列表，为编写交易执行逻辑打下了基础。

## 总结
本节课中我们一起学习了股票池筛选的完整流程。
1.  我们首先对原始因子数据进行了三步预处理：**离群值处理**、**标准化**和**中性化**，以获得干净、可比的因子数据。
2.  接着，我们基于处理后的因子设定了筛选阈值（如前20%），并**筛选出符合策略要求的股票**，构建了目标股票池。
3.  最后，我们通过对比当前持仓与目标股票池，**明确了需要调仓的股票列表**（包括卖出和买入），为下一节课实现具体的交易逻辑做好了准备。

![](img/6f95fd6584fc5f4c0c86203d2a4ddafd_48.png)

这个过程是量化交易策略中从因子分析到具体交易指令生成的核心环节。