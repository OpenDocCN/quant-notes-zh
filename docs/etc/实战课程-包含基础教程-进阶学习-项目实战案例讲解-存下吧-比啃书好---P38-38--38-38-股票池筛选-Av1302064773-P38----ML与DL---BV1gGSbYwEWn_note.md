# 量化交易教程：P38：股票池筛选

![](img/90651afca6bebc1944ec693c0d13df30_0.png)

![](img/90651afca6bebc1944ec693c0d13df30_2.png)

![](img/90651afca6bebc1944ec693c0d13df30_4.png)

![](img/90651afca6bebc1944ec693c0d13df30_6.png)

![](img/90651afca6bebc1944ec693c0d13df30_8.png)

![](img/90651afca6bebc1944ec693c0d13df30_0.png)

![](img/90651afca6bebc1944ec693c0d13df30_10.png)

![](img/90651afca6bebc1944ec693c0d13df30_12.png)

![](img/90651afca6bebc1944ec693c0d13df30_14.png)

在本节课中，我们将学习如何基于处理后的因子数据，构建并筛选出用于交易的股票池。我们将完成因子预处理后的最后一步，并实现一个简单的选股策略。

![](img/90651afca6bebc1944ec693c0d13df30_16.png)

![](img/90651afca6bebc1944ec693c0d13df30_18.png)

![](img/90651afca6bebc1944ec693c0d13df30_20.png)

上一节我们介绍了因子的中性化操作，本节中我们来看看如何利用处理好的因子进行股票池的筛选。

![](img/90651afca6bebc1944ec693c0d13df30_22.png)

![](img/90651afca6bebc1944ec693c0d13df30_24.png)

## 执行预处理操作

![](img/90651afca6bebc1944ec693c0d13df30_26.png)

![](img/90651afca6bebc1944ec693c0d13df30_28.png)

![](img/90651afca6bebc1944ec693c0d13df30_30.png)

![](img/90651afca6bebc1944ec693c0d13df30_32.png)

现在，我们可以执行之前编写的几个预处理操作了。

![](img/90651afca6bebc1944ec693c0d13df30_34.png)

![](img/90651afca6bebc1944ec693c0d13df30_36.png)

![](img/90651afca6bebc1944ec693c0d13df30_2.png)

![](img/90651afca6bebc1944ec693c0d13df30_38.png)

![](img/90651afca6bebc1944ec693c0d13df30_40.png)

![](img/90651afca6bebc1944ec693c0d13df30_42.png)

![](img/90651afca6bebc1944ec693c0d13df30_44.png)

以下是具体的执行步骤：

![](img/90651afca6bebc1944ec693c0d13df30_46.png)

![](img/90651afca6bebc1944ec693c0d13df30_48.png)

![](img/90651afca6bebc1944ec693c0d13df30_50.png)

1.  **获取因子数据**：首先，我们需要从数据源获取因子数据，并将其转换为DataFrame格式，以便后续操作。
    ```python
    fundamental = get_fundamental_data()  # 假设的函数，获取基本面数据
    fundamental_df = pd.DataFrame(fundamental)
    ```

![](img/90651afca6bebc1944ec693c0d13df30_52.png)

![](img/90651afca6bebc1944ec693c0d13df30_54.png)

![](img/90651afca6bebc1944ec693c0d13df30_56.png)

2.  **去除离群值**：对因子数据应用三西格玛法则，去除极端值。
    ```python
    factor_series = fundamental_df[‘target_factor’]  # 假设的目标因子列名
    factor_no_outliers = remove_outliers_3sigma(factor_series)
    ```

![](img/90651afca6bebc1944ec693c0d13df30_58.png)

![](img/90651afca6bebc1944ec693c0d13df30_60.png)

3.  **数据标准化**：将去除离群值后的因子数据进行标准化处理，使其均值为0，标准差为1。
    ```python
    factor_standardized = standardize(factor_no_outliers)
    ```

![](img/90651afca6bebc1944ec693c0d13df30_62.png)

![](img/90651afca6bebc1944ec693c0d13df30_64.png)

![](img/90651afca6bebc1944ec693c0d13df30_66.png)

4.  **因子中性化**：对标准化后的因子进行中性化处理，以消除市值等行业或风格因素的影响。
    ```python
    factor_neutralized = neutralize(factor_standardized, exposure=‘market_cap’)
    ```

![](img/90651afca6bebc1944ec693c0d13df30_68.png)

![](img/90651afca6bebc1944ec693c0d13df30_70.png)

![](img/90651afca6bebc1944ec693c0d13df30_72.png)

现在，我们就完成了因子所需的所有预处理操作。

![](img/90651afca6bebc1944ec693c0d13df30_74.png)

![](img/90651afca6bebc1944ec693c0d13df30_76.png)

![](img/90651afca6bebc1944ec693c0d13df30_78.png)

## 基于因子筛选股票

![](img/90651afca6bebc1944ec693c0d13df30_80.png)

![](img/90651afca6bebc1944ec693c0d13df30_82.png)

![](img/90651afca6bebc1944ec693c0d13df30_84.png)

接下来，我们需要基于处理后的因子对股票池进行筛选。

![](img/90651afca6bebc1944ec693c0d13df30_86.png)

![](img/90651afca6bebc1944ec693c0d13df30_88.png)

![](img/90651afca6bebc1944ec693c0d13df30_90.png)

![](img/90651afca6bebc1944ec693c0d13df30_56.png)

![](img/90651afca6bebc1944ec693c0d13df30_92.png)

![](img/90651afca6bebc1944ec693c0d13df30_94.png)

![](img/90651afca6bebc1944ec693c0d13df30_96.png)

首先，需要确定筛选逻辑。例如，对于市净率（P/B）因子，通常认为值较小的股票未来收益可能更高。因此，我们可以选择因子值最小的前20%的股票。

![](img/90651afca6bebc1944ec693c0d13df30_98.png)

![](img/90651afca6bebc1944ec693c0d13df30_100.png)

![](img/90651afca6bebc1944ec693c0d13df30_102.png)

以下是筛选股票池的步骤：

![](img/90651afca6bebc1944ec693c0d13df30_104.png)

![](img/90651afca6bebc1944ec693c0d13df30_106.png)

1.  **设定筛选阈值**：计算因子的分位数，确定前20%的临界值。
    ```python
    threshold = factor_neutralized.quantile(0.2)
    ```

![](img/90651afca6bebc1944ec693c0d13df30_108.png)

![](img/90651afca6bebc1944ec693c0d13df30_110.png)

![](img/90651afca6bebc1944ec693c0d13df30_112.png)

2.  **执行筛选**：选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
    ```

![](img/90651afca6bebc1944ec693c0d13df30_114.png)

![](img/90651afca6bebc1944ec693c0d13df30_116.png)

![](img/90651afca6bebc1944ec693c0d13df30_118.png)

![](img/90651afca6bebc1944ec693c0d13df30_120.png)

3.  **存储筛选结果**：将筛选出的股票列表存入上下文变量中，供后续交易逻辑使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/90651afca6bebc1944ec693c0d13df30_122.png)

![](img/90651afca6bebc1944ec693c0d13df30_124.png)

## 制定交易调仓逻辑

![](img/90651afca6bebc1944ec693c0d13df30_126.png)

![](img/90651afca6bebc1944ec693c0d13df30_128.png)

![](img/90651afca6bebc1944ec693c0d13df30_130.png)

获得目标股票池后，需要制定具体的交易指令，包括买入新标的和卖出已持有但不在新池中的标的。

![](img/90651afca6bebc1944ec693c0d13df30_132.png)

![](img/90651afca6bebc1944ec693c0d13df30_134.png)

![](img/90651afca6bebc1944ec693c0d13df30_136.png)

![](img/90651afca6bebc1944ec693c0d13df30_108.png)

![](img/90651afca6bebc1944ec693c0d13df30_138.png)

![](img/90651afca6bebc1944ec693c0d13df30_140.png)

![](img/90651afca6bebc1944ec693c0d13df30_142.png)

以下是调仓逻辑的实现步骤：

![](img/90651afca6bebc1944ec693c0d13df30_144.png)

![](img/90651afca6bebc1944ec693c0d13df30_146.png)

![](img/90651afca6bebc1944ec693c0d13df30_148.png)

1.  **获取当前持仓**：从交易上下文中获取当前账户持有的所有股票。
    ```python
    current_holdings = list(context.portfolio.positions.keys())
    ```

![](img/90651afca6bebc1944ec693c0d13df30_150.png)

![](img/90651afca6bebc1944ec693c0d13df30_152.png)

2.  **确定待卖出股票**：找出当前持有但不在最新股票池中的股票，这些是需要卖出的。
    ```python
    stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
    ```

![](img/90651afca6bebc1944ec693c0d13df30_154.png)

![](img/90651afca6bebc1944ec693c0d13df30_156.png)

![](img/90651afca6bebc1944ec693c0d13df30_158.png)

3.  **确定待买入股票**：找出在最新股票池中但当前并未持有的股票，这些是需要买入的。
    ```python
    stocks_to_buy = list(set(context.stock_list) - set(current_holdings))
    ```

![](img/90651afca6bebc1944ec693c0d13df30_160.png)

![](img/90651afca6bebc1944ec693c0d13df30_162.png)

![](img/90651afca6bebc1944ec693c0d13df30_164.png)

4.  **执行交易订单**：根据资金情况，对`stocks_to_sell`执行卖出操作，对`stocks_to_buy`执行买入操作。这部分逻辑需要根据具体的交易平台API实现。

![](img/90651afca6bebc1944ec693c0d13df30_166.png)

![](img/90651afca6bebc1944ec693c0d13df30_168.png)

![](img/90651afca6bebc1944ec693c0d13df30_170.png)

本节课中我们一起学习了如何将处理好的因子应用于实战，完成股票池的筛选和交易调仓的逻辑构建。我们首先回顾并执行了完整的因子预处理流程，然后基于因子值筛选出目标股票，最后设计了对比当前持仓与目标股票池以产生交易信号的逻辑。这是构建一个完整量化交易策略的关键一步。