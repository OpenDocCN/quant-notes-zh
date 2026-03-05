# 量化交易完全可自学教程：P85：33. 股票池筛选与调仓操作

![](img/544e445f34beac25c1891c503b1fa744_0.png)

![](img/544e445f34beac25c1891c503b1fa744_2.png)

![](img/544e445f34beac25c1891c503b1fa744_4.png)

![](img/544e445f34beac25c1891c503b1fa744_6.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子预处理到生成最终交易股票列表的完整流程。

![](img/544e445f34beac25c1891c503b1fa744_8.png)

![](img/544e445f34beac25c1891c503b1fa744_10.png)

---

上一节我们完成了因子的中性化处理，得到了一个相对“干净”的因子数据。本节中，我们来看看如何利用这个因子来筛选股票，并准备进行交易。

![](img/544e445f34beac25c1891c503b1fa744_12.png)

![](img/544e445f34beac25c1891c503b1fa744_14.png)

### 执行预处理操作
现在，我们可以执行之前编写好的预处理操作了。以下是完整的预处理步骤：

![](img/544e445f34beac25c1891c503b1fa744_16.png)

![](img/544e445f34beac25c1891c503b1fa744_18.png)

1.  **离群值处理**：首先，我们对因子数据进行离群值处理，以消除极端值的影响。
    ```python
    # 对市净率因子进行离群值处理
    processed_factor = winsorize(fundamental_df['PB'])
    ```

![](img/544e445f34beac25c1891c503b1fa744_20.png)

![](img/544e445f34beac25c1891c503b1fa744_22.png)

![](img/544e445f34beac25c1891c503b1fa744_24.png)

2.  **标准化操作**：接着，我们对处理后的数据进行标准化，使其符合标准正态分布。
    ```python
    # 对处理后的因子进行标准化
    standardized_factor = standardize(processed_factor)
    ```

![](img/544e445f34beac25c1891c503b1fa744_26.png)

![](img/544e445f34beac25c1891c503b1fa744_28.png)

3.  **中性化操作**：最后，我们对标准化后的因子进行中性化处理，以剔除市场风格等因素的影响。
    ```python
    # 对标准化因子进行中性化处理
    neutralized_factor = neutralize(standardized_factor)
    ```

![](img/544e445f34beac25c1891c503b1fa744_30.png)

至此，我们完成了因子所需的所有预处理操作，得到了一个可用于选股的“中性化因子”。

![](img/544e445f34beac25c1891c503b1fa744_32.png)

![](img/544e445f34beac25c1891c503b1fa744_34.png)

### 基于因子筛选股票
接下来，我们需要基于这个处理好的因子对股票池进行筛选。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据之前的分析，这类股票可能具有更高的预期收益。

![](img/544e445f34beac25c1891c503b1fa744_36.png)

以下是筛选股票的具体步骤：

![](img/544e445f34beac25c1891c503b1fa744_38.png)

1.  **设定筛选阈值**：我们设定一个分位数阈值，例如选择因子值最小的前20%的股票。
    ```python
    # 计算20%分位点的值
    threshold = neutralized_factor.quantile(0.2)
    ```

![](img/544e445f34beac25c1891c503b1fa744_40.png)

![](img/544e445f34beac25c1891c503b1fa744_42.png)

2.  **执行筛选判断**：根据阈值，判断哪些股票的因子值满足我们的要求（小于等于阈值）。
    ```python
    # 筛选出因子值小于等于阈值的股票
    selected_stocks = neutralized_factor[neutralized_factor <= threshold].index.tolist()
    ```

![](img/544e445f34beac25c1891c503b1fa744_44.png)

![](img/544e445f34beac25c1891c503b1fa744_46.png)

3.  **存储筛选结果**：将筛选出的股票列表存储到上下文变量中，供后续调仓使用。
    ```python
    # 将选出的股票列表存入context
    context.stock_list = selected_stocks
    ```

![](img/544e445f34beac25c1891c503b1fa744_48.png)

![](img/544e445f34beac25c1891c503b1fa744_50.png)

![](img/544e445f34beac25c1891c503b1fa744_52.png)

现在，我们得到了一个基于因子策略筛选出的目标股票池 `stock_list`。

![](img/544e445f34beac25c1891c503b1fa744_54.png)

![](img/544e445f34beac25c1891c503b1fa744_56.png)

![](img/544e445f34beac25c1891c503b1fa744_58.png)

### 准备调仓操作
有了目标股票池后，下一步是规划如何调整现有持仓，使其与目标池保持一致。这涉及到卖出不在新池子中的股票。

![](img/544e445f34beac25c1891c503b1fa744_60.png)

![](img/544e445f34beac25c1891c503b1fa744_62.png)

以下是准备调仓操作的步骤：

![](img/544e445f34beac25c1891c503b1fa744_64.png)

![](img/544e445f34beac25c1891c503b1fa744_66.png)

![](img/544e445f34beac25c1891c503b1fa744_68.png)

1.  **获取当前持仓**：首先，我们需要获取当前账户中持有的所有股票。
    ```python
    # 获取当前持仓的股票代码列表
    current_holdings = list(context.portfolio.positions.keys())
    ```

![](img/544e445f34beac25c1891c503b1fa744_70.png)

![](img/544e445f34beac25c1891c503b1fa744_72.png)

2.  **计算需要卖出的股票**：通过对比当前持仓 `current_holdings` 和目标股票池 `stock_list`，找出那些我们持有但不在新目标池中的股票。这些股票就是我们需要卖出的。
    ```python
    # 找出需要卖出的股票（在当前持仓中但不在目标股票池中）
    stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
    ```

![](img/544e445f34beac25c1891c503b1fa744_74.png)

![](img/544e445f34beac25c1891c503b1fa744_76.png)

3.  **存储卖出列表**：将需要卖出的股票列表也存储起来，以便在交易函数中执行卖出指令。
    ```python
    # 将待卖出股票列表存入context
    context.stocks_to_sell = stocks_to_sell
    ```

![](img/544e445f34beac25c1891c503b1fa744_78.png)

![](img/544e445f34beac25c1891c503b1fa744_80.png)

这样，我们就明确了本次调仓的两个核心列表：**要买入的目标股票列表 (`stock_list`)** 和 **要卖出的现有股票列表 (`stocks_to_sell`)**。在下一节的交易执行函数中，我们将使用这两个列表来完成具体的买卖操作。

![](img/544e445f34beac25c1891c503b1fa744_82.png)

![](img/544e445f34beac25c1891c503b1fa744_84.png)

![](img/544e445f34beac25c1891c503b1fa744_86.png)

---

![](img/544e445f34beac25c1891c503b1fa744_88.png)

![](img/544e445f34beac25c1891c503b1fa744_90.png)

## 总结
本节课中我们一起学习了股票池筛选与调仓准备的完整过程。我们首先回顾并执行了因子的预处理流程（离群值处理、标准化、中性化）。然后，我们基于处理后的因子设定了筛选条件，选出了符合策略逻辑的目标股票。最后，我们通过对比当前持仓与目标股票池，确定了需要卖出的股票列表，为下一课的实际交易操作做好了准备。