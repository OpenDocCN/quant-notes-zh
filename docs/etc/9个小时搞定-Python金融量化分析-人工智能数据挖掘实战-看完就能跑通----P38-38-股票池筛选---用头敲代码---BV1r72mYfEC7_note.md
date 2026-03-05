# Python金融量化分析：P38：股票池筛选

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_0.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_2.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_4.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并构建一个基础的调仓逻辑。我们将完成因子预处理后的应用步骤，包括确定选股标准、筛选目标股票以及为后续交易操作准备数据。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_6.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来构建我们的投资组合。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_8.png)

---

## 执行预处理操作

现在，我们已经完成了中性化操作。接下来，我们将按顺序执行之前编写的几个预处理函数。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_10.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_12.png)

首先，我们需要获取因子数据并为其指定一个返回值。

```python
fundamental = fundamental.to_frame()
```

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_14.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_16.png)

在这个DataFrame中，包含了我们所需的指标数据。我们将对这个指标（例如市净率）进行操作。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_18.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_20.png)

以下是完整的预处理步骤：

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_22.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_24.png)

1.  **去除离群值**：首先，我们对因子数据应用三西格玛法去除极端值。
    ```python
    factor_no_outliers = three_sigma(fundamental['pb_ratio'])
    ```
    经过这一步，我们的指标数据中就不再包含极值了。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_26.png)

2.  **标准化处理**：去除离群值后，我们还需要对数据进行标准化，使其符合标准正态分布。
    ```python
    factor_standardized = standardize(factor_no_outliers)
    ```
    这一步完成了一个标准化的操作。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_28.png)

3.  **中性化处理**：最后，我们对标准化后的因子进行中性化处理，以消除市值等风格因子的影响。
    ```python
    factor_neutralized = neutralize(factor_standardized)
    ```
    至此，我们完成了因子所需的所有预处理操作。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_30.png)

---

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_32.png)

## 基于因子筛选股票

接下来，我们需要基于处理后的因子对股票池进行筛选。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据之前的分析，这类股票可能带来更高的预期收益。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_34.png)

首先，我们需要确定一个筛选的阈值。例如，我们希望选择因子值最小的前20%的股票。

```python
threshold = factor_neutralized.quantile(0.2)
```

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_36.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_38.png)

这里，我们计算了因子值的20%分位数。接下来，我们进行选股操作。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_40.png)

以下是筛选逻辑：
我们需要判断哪些股票的因子值满足我们的要求（即小于或等于阈值）。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_42.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_44.png)

```python
selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
```

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_46.png)

这段代码基于因子值进行判断，并返回所有满足条件（因子值小于等于20%分位数）的股票代码列表。这些就是我们想要选入投资组合的股票。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_48.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_50.png)

我们将筛选出的股票列表存入上下文变量中，以备后续交易使用。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_52.png)

```python
context.stock_list = selected_stocks
```

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_54.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_56.png)

---

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_58.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_60.png)

## 准备调仓：识别需卖出的股票

有了目标股票池后，我们需要执行调仓操作，这涉及买入新股票和卖出不再持有的股票。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_62.png)

首先，我们需要找出哪些当前持有的股票不在新的目标股票池中，这些股票需要被卖出。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_64.png)

以下是识别待卖出股票的步骤：
我们需要获取当前账户的持仓信息。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_66.png)

```python
current_holdings = context.portfolio.positions
```

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_68.png)

然后，我们将当前持仓与新的目标股票池进行比较，找出差异。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_70.png)

```python
stocks_to_sell = set(current_holdings).difference(set(context.stock_list))
```

`difference`操作的含义是：找出存在于当前持仓集合但不存在于目标股票池集合中的股票。这些股票就是我们计划要卖出的。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_72.png)

这样，我们就完成了调仓前的准备工作，明确了需要买入的股票（`context.stock_list`）和需要卖出的股票（`stocks_to_sell`）。

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_74.png)

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_76.png)

---

![](img/b72e1b138127e9db484d4cb2d3aa0cdb_78.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。我们首先回顾并执行了因子的预处理步骤（去极值、标准化、中性化）。接着，我们设定了选股标准（选择因子值最小的前20%），并基于此筛选出了目标股票组合。最后，我们通过比较当前持仓与目标组合，识别出了需要调出的股票，为下一节课实现具体的买卖交易指令打下了基础。