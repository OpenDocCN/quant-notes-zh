# Python金融分析与量化交易实战：P38：股票池筛选

![](img/bf330b6b6a36ad81d46741d67994cc30_0.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_2.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_4.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_6.png)

在本节课中，我们将学习如何基于预处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子处理到股票选择的完整流程。

![](img/bf330b6b6a36ad81d46741d67994cc30_8.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_10.png)

## 概述

![](img/bf330b6b6a36ad81d46741d67994cc30_12.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_14.png)

上一节我们完成了因子的中性化处理。本节中，我们将利用处理好的因子数据，设定筛选条件，构建目标股票池，并规划如何根据新旧股票池的差异进行买卖操作。

![](img/bf330b6b6a36ad81d46741d67994cc30_16.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_18.png)

## 执行预处理操作

![](img/bf330b6b6a36ad81d46741d67994cc30_20.png)

首先，我们需要执行之前编写好的预处理函数，对原始因子数据进行处理。

![](img/bf330b6b6a36ad81d46741d67994cc30_22.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_24.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_26.png)

以下是完整的预处理步骤，我们将逐步执行：

![](img/bf330b6b6a36ad81d46741d67994cc30_28.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_30.png)

1.  **去除离群值**：使用三西格玛法则处理因子数据中的极端值。
2.  **标准化**：将因子数据转换为均值为0、标准差为1的标准分布。
3.  **中性化**：消除因子与市值之间的相关性。

![](img/bf330b6b6a36ad81d46741d67994cc30_32.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_34.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_36.png)

我们将从获取基本面数据开始，并依次应用上述操作。

![](img/bf330b6b6a36ad81d46741d67994cc30_38.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_40.png)

```python
# 获取基本面数据并转换为DataFrame
fundamental = get_fundamentals(...).T
fundamental_df = pd.DataFrame(fundamental)

![](img/bf330b6b6a36ad81d46741d67994cc30_42.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_44.png)

# 1. 对‘市净率’因子进行去离群值处理
factor_no_outliers = three_sigma(fundamental_df, ‘市净率‘)

![](img/bf330b6b6a36ad81d46741d67994cc30_46.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_48.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_50.png)

# 2. 对去离群值后的结果进行标准化
factor_standardized = standardize(factor_no_outliers)

![](img/bf330b6b6a36ad81d46741d67994cc30_52.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_54.png)

# 3. 对标准化后的因子进行市值中性化处理
factor_neutralized = neutralize(factor_standardized, ‘市值‘)

![](img/bf330b6b6a36ad81d46741d67994cc30_56.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_58.png)

# 将最终处理好的因子数据赋值给变量
processed_factor = factor_neutralized
```

![](img/bf330b6b6a36ad81d46741d67994cc30_60.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_62.png)

现在，我们得到了一个经过完整预处理的因子序列 `processed_factor`。

![](img/bf330b6b6a36ad81d46741d67994cc30_64.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_66.png)

## 基于因子筛选股票

![](img/bf330b6b6a36ad81d46741d67994cc30_68.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_70.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_72.png)

得到处理后的因子后，下一步是基于它来筛选股票。我们的策略是选择因子值较小（例如市净率较低）的股票，因为理论上它们可能具有更高的预期收益。

![](img/bf330b6b6a36ad81d46741d67994cc30_74.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_76.png)

以下是筛选股票的具体步骤：

![](img/bf330b6b6a36ad81d46741d67994cc30_78.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_80.png)

1.  **设定筛选阈值**：我们计划选择因子值最小的前20%的股票。
2.  **计算分位点**：通过计算因子的20%分位数来确定阈值。
3.  **执行筛选**：选出所有因子值小于等于该阈值的股票。

![](img/bf330b6b6a36ad81d46741d67994cc30_82.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_84.png)

```python
# 计算20%分位数作为阈值
threshold = processed_factor.quantile(0.2)

![](img/bf330b6b6a36ad81d46741d67994cc30_86.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_88.png)

# 筛选出因子值小于等于阈值的所有股票
selected_stocks = processed_factor[processed_factor <= threshold].index.tolist()

![](img/bf330b6b6a36ad81d46741d67994cc30_90.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_92.png)

# 将选出的股票列表存入上下文对象中，供后续调仓使用
context.stock_list = selected_stocks
```

![](img/bf330b6b6a36ad81d46741d67994cc30_94.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_96.png)

至此，我们得到了目标股票池 `context.stock_list`。

![](img/bf330b6b6a36ad81d46741d67994cc30_98.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_100.png)

## 规划调仓操作

![](img/bf330b6b6a36ad81d46741d67994cc30_102.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_104.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_106.png)

有了新的目标股票池后，我们需要将其与当前持有的股票进行比较，以决定需要卖出和买入哪些股票。

![](img/bf330b6b6a36ad81d46741d67994cc30_108.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_110.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_112.png)

以下是规划调仓操作的逻辑：

![](img/bf330b6b6a36ad81d46741d67994cc30_114.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_116.png)

1.  **获取当前持仓**：从交易账户中获取当前持有的所有股票。
2.  **计算需要卖出的股票**：找出在当前持仓中但不在新股票池里的股票，这些是需要卖出的。
3.  **计算需要买入的股票**：找出在新股票池里但不在当前持仓中的股票，这些是需要买入的。

![](img/bf330b6b6a36ad81d46741d67994cc30_118.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_120.png)

首先，我们实现卖出逻辑。

![](img/bf330b6b6a36ad81d46741d67994cc30_122.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_124.png)

```python
# 获取当前账户所有持仓的股票代码
current_holdings = list(context.portfolio.positions.keys())

![](img/bf330b6b6a36ad81d46741d67994cc30_126.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_128.png)

# 计算需要卖出的股票：当前持有但不在新股票池中
stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
```

![](img/bf330b6b6a36ad81d46741d67994cc30_130.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_132.png)

接下来，买入逻辑相对简单，就是新股票池中我们尚未持有的部分。

![](img/bf330b6b6a36ad81d46741d67994cc30_134.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_136.png)

```python
# 计算需要买入的股票：在新股票池中但当前未持有
stocks_to_buy = list(set(context.stock_list) - set(current_holdings))
```

![](img/bf330b6b6a36ad81d46741d67994cc30_138.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_140.png)

通过以上步骤，我们明确了 `stocks_to_sell` 和 `stocks_to_buy` 两个列表，为下一步执行具体的交易指令做好了准备。

![](img/bf330b6b6a36ad81d46741d67994cc30_142.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_144.png)

## 总结

![](img/bf330b6b6a36ad81d46741d67994cc30_146.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_148.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_150.png)

本节课中我们一起学习了股票池筛选与调仓规划的核心流程。

![](img/bf330b6b6a36ad81d46741d67994cc30_152.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_154.png)

我们首先回顾并执行了因子的预处理流程，包括去离群值、标准化和中性化。接着，我们基于处理后的因子设定了筛选条件（选择前20%），得到了目标股票池。最后，我们通过比较目标股票池与当前持仓，清晰地定义出了需要卖出和买入的股票列表，为策略的执行奠定了坚实基础。

![](img/bf330b6b6a36ad81d46741d67994cc30_156.png)

![](img/bf330b6b6a36ad81d46741d67994cc30_158.png)

在接下来的课程中，我们将利用本节得到的买卖列表，在回测框架中实现具体的交易下单逻辑。