# 人生苦短，我学量化！：P33：股票池筛选P34

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_0.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_2.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_4.png)

## 概述
在本节课中，我们将学习如何基于处理好的因子数据，对股票池进行筛选，并准备执行后续的调仓操作。我们将完成因子预处理后的关键步骤，包括设定筛选条件、获取目标股票列表，以及识别需要卖出的持仓股票。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_6.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_8.png)

上一节我们完成了因子的中性化处理，本节中我们来看看如何利用处理好的因子来筛选股票。

## 因子预处理流程回顾与执行
现在，我们已经完成了中性化操作的代码编写。让我们回顾并执行整个预处理流程。

首先，我们需要在预处理函数中调用我们编写好的方法。预处理的第一步是对因子进行去极值处理。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_10.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_12.png)

以下是预处理操作的具体步骤：

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_14.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_16.png)

1.  **去极值处理**：调用 `sigma` 方法，对因子数据（例如市净率）进行处理，以消除极端值的影响。
    ```python
    # 对因子数据（例如‘pb’市净率）进行去极值处理
    factor_no_outliers = sigma(fundamental_data['pb'])
    ```

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_18.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_20.png)

2.  **标准化处理**：将去极值后的因子数据进行标准化，使其均值为0，标准差为1。
    ```python
    # 对去极值后的数据进行标准化
    factor_standardized = standardize(factor_no_outliers)
    ```

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_22.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_24.png)

3.  **中性化处理**：最后，对标准化后的因子进行中性化处理，以消除行业、市值等其他因素的影响。
    ```python
    # 对标准化后的因子进行中性化处理
    factor_neutralized = neutralize(factor_standardized)
    ```

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_26.png)

执行完以上三步后，我们就得到了完全预处理后的因子数据。接下来，我们将基于这个处理好的因子进行股票筛选。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_28.png)

## 基于因子值筛选股票
预处理完成后，下一步是基于因子值对股票池进行筛选操作。

我们以市净率（PB）因子为例，通常认为市净率较低的股票可能更具投资价值。因此，我们的筛选目标是选择因子值最小的前20%的股票。

以下是筛选操作的步骤：

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_30.png)

1.  **设定分位数阈值**：我们使用分位数函数来确定前20%的临界值。
    ```python
    # 计算因子值从小到大排序的前20%分位点
    threshold = factor_neutralized.quantile(0.2)
    ```

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_32.png)

2.  **执行条件筛选**：筛选出因子值小于或等于该阈值的所有股票。
    ```python
    # 筛选出因子值小于等于阈值的股票
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index
    ```

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_34.png)

3.  **存储筛选结果**：将筛选出的股票代码列表存储起来，供后续调仓使用。
    ```python
    # 将选中的股票列表存入上下文变量中
    context.stock_list = selected_stocks
    ```

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_36.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_38.png)

现在，我们得到了一个目标股票列表 `context.stock_list`。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_40.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_42.png)

## 准备调仓：识别需卖出的股票
基于新的股票列表，我们需要执行调仓操作。这包括买入新列表中的股票，并卖出已持有但不在新列表中的股票。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_44.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_46.png)

因此，首先要识别出哪些当前持有的股票需要被卖出。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_48.png)

以下是识别待卖出股票的步骤：

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_50.png)

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_52.png)

1.  **获取当前持仓**：首先，需要获取当前投资组合中持有的所有股票。
    ```python
    # 获取当前账户持有的所有股票代码
    current_holdings = context.portfolio.positions
    ```

2.  **计算差异集合**：然后，找出当前持仓中不在最新目标股票列表里的股票。这可以通过集合的差集操作来实现。
    ```python
    # 计算需要卖出的股票：当前持有但不在目标列表中的股票
    stocks_to_sell = set(current_holdings) - set(context.stock_list)
    ```

这样，我们就得到了一个需要清仓的股票列表 `stocks_to_sell`，为下一步执行具体的买卖指令做好了准备。

![](img/c3ed8fd0b9ef01176e6513cf6878ed82_54.png)

## 总结
本节课中我们一起学习了股票池筛选的关键步骤。我们首先回顾并执行了因子的完整预处理流程（去极值、标准化、中性化）。接着，我们基于处理后的因子值设定了筛选条件，选出了目标股票组合。最后，我们通过比较当前持仓与目标组合，识别出了需要卖出的股票，为下一节课执行交易指令打下了基础。