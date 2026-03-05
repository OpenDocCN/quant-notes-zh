# 机器学习与量化交易：P38：股票池筛选 📊

![](img/12111e9f8a5b1721de48de2a0fa11374_0.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_2.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_4.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_6.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并准备执行调仓操作。我们将完成因子预处理后的步骤，包括离群值处理、标准化、中性化，并最终根据因子值筛选出目标股票。

![](img/12111e9f8a5b1721de48de2a0fa11374_8.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_10.png)

---

![](img/12111e9f8a5b1721de48de2a0fa11374_12.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_14.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_16.png)

上一节我们完成了因子的中性化处理。本节中，我们来看看如何基于处理好的因子数据，对股票池进行筛选。

![](img/12111e9f8a5b1721de48de2a0fa11374_18.png)

首先，我们需要执行之前写好的预处理操作。以下是具体的步骤：

![](img/12111e9f8a5b1721de48de2a0fa11374_20.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_22.png)

**第一步：去除离群值**
我们使用三西格玛方法对因子数据进行处理，以去除极端值。

![](img/12111e9f8a5b1721de48de2a0fa11374_24.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_26.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_28.png)

```python
# 对因子数据执行三西格玛去离群值操作
factor_processed = three_sigma(fundamental_df, factor_column)
```

![](img/12111e9f8a5b1721de48de2a0fa11374_30.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_32.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_34.png)

**第二步：数据标准化**
去除离群值后，我们需要对数据进行标准化处理，使其符合标准正态分布。

![](img/12111e9f8a5b1721de48de2a0fa11374_36.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_38.png)

```python
# 对处理后的因子数据进行标准化
factor_standardized = standardize(factor_processed)
```

![](img/12111e9f8a5b1721de48de2a0fa11374_40.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_42.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_44.png)

**第三步：因子中性化**
最后，我们对标准化后的因子进行中性化处理，以消除市场风格等因素的影响。

![](img/12111e9f8a5b1721de48de2a0fa11374_46.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_48.png)

```python
# 对标准化后的因子进行中性化处理
factor_neutralized = neutralize(factor_standardized, style_factor)
```

![](img/12111e9f8a5b1721de48de2a0fa11374_50.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_52.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_54.png)

现在，我们已经完成了所有必需的预处理操作，得到了一个干净、可用于筛选的因子数据。

![](img/12111e9f8a5b1721de48de2a0fa11374_56.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_58.png)

---

![](img/12111e9f8a5b1721de48de2a0fa11374_60.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_62.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_64.png)

接下来，我们需要基于这个处理好的因子来筛选股票。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据策略假设，这些股票可能具有更高的预期收益。

![](img/12111e9f8a5b1721de48de2a0fa11374_66.png)

以下是筛选股票的具体步骤：

![](img/12111e9f8a5b1721de48de2a0fa11374_68.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_70.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_72.png)

**第一步：确定筛选阈值**
我们计划选择因子值排名在前20%的股票，即因子值最小的那部分股票。

![](img/12111e9f8a5b1721de48de2a0fa11374_74.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_76.png)

```python
# 计算20%分位数的阈值
threshold = factor_neutralized.quantile(0.2)
```

![](img/12111e9f8a5b1721de48de2a0fa11374_78.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_80.png)

**第二步：执行筛选**
根据计算出的阈值，筛选出因子值小于或等于该阈值的所有股票。

![](img/12111e9f8a5b1721de48de2a0fa11374_82.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_84.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_86.png)

```python
# 筛选出因子值小于等于阈值的股票
selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
```

![](img/12111e9f8a5b1721de48de2a0fa11374_88.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_90.png)

**第三步：存储筛选结果**
将筛选出的股票列表存储到上下文变量中，以便后续的调仓操作使用。

![](img/12111e9f8a5b1721de48de2a0fa11374_92.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_94.png)

```python
# 将选出的股票列表存入context
context.stock_list = selected_stocks
```

![](img/12111e9f8a5b1721de48de2a0fa11374_96.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_98.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_100.png)

至此，我们得到了基于因子策略筛选出的目标股票池。

![](img/12111e9f8a5b1721de48de2a0fa11374_102.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_104.png)

---

![](img/12111e9f8a5b1721de48de2a0fa11374_106.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_108.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_110.png)

在准备执行买卖操作前，我们还需要处理持仓调整。具体来说，需要卖出那些不在新股票池中的现有持仓。

![](img/12111e9f8a5b1721de48de2a0fa11374_112.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_114.png)

以下是确定需要卖出的股票步骤：

![](img/12111e9f8a5b1721de48de2a0fa11374_116.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_118.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_120.png)

**第一步：获取当前持仓**
首先，我们需要获取当前账户中持有的所有股票。

![](img/12111e9f8a5b1721de48de2a0fa11374_122.png)

```python
# 获取当前持仓的股票列表
current_holdings = context.portfolio.positions.keys()
```

![](img/12111e9f8a5b1721de48de2a0fa11374_124.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_126.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_128.png)

**第二步：计算差异股票**
接着，找出当前持仓中不在新股票池（`selected_stocks`）里的股票。这些股票就是需要卖出的。

![](img/12111e9f8a5b1721de48de2a0fa11374_130.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_132.png)

```python
# 找出需要卖出的股票（在当前持仓但不在新股票池中）
stocks_to_sell = set(current_holdings).difference(set(selected_stocks))
```

![](img/12111e9f8a5b1721de48de2a0fa11374_134.png)

**第三步：准备执行卖出操作**
最后，我们将需要卖出的股票列表也存储起来，为下一步的调仓逻辑做好准备。

![](img/12111e9f8a5b1721de48de2a0fa11374_136.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_138.png)

```python
# 将待卖出股票列表存入context
context.stocks_to_sell = list(stocks_to_sell)
```

![](img/12111e9f8a5b1721de48de2a0fa11374_140.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_142.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_144.png)

---

![](img/12111e9f8a5b1721de48de2a0fa11374_146.png)

![](img/12111e9f8a5b1721de48de2a0fa11374_148.png)

本节课中我们一起学习了股票池筛选的完整流程。我们从预处理后的因子数据出发，通过设定阈值筛选出目标股票，并计算了需要从现有持仓中移除的股票。这为下一节课实现具体的买卖和调仓逻辑奠定了坚实的基础。