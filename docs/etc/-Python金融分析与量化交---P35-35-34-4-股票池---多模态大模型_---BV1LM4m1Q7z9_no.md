# Python金融分析与量化交易实战：P35：股票池筛选与调仓操作

![](img/59af62aa21ea617e48810a296dcfcb79_0.png)

![](img/59af62aa21ea617e48810a296dcfcb79_2.png)

![](img/59af62aa21ea617e48810a296dcfcb79_4.png)

![](img/59af62aa21ea617e48810a296dcfcb79_6.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并执行相应的调仓操作。我们将完成从因子预处理到生成最终交易股票列表的完整流程。

![](img/59af62aa21ea617e48810a296dcfcb79_8.png)

## 概述

![](img/59af62aa21ea617e48810a296dcfcb79_10.png)

上一节我们完成了因子的中性化处理。本节中，我们将利用处理好的因子数据，通过设定筛选条件来构建目标股票池，并学习如何根据新旧股票池的差异，制定买入和卖出的交易逻辑。

![](img/59af62aa21ea617e48810a296dcfcb79_12.png)

![](img/59af62aa21ea617e48810a296dcfcb79_14.png)

![](img/59af62aa21ea617e48810a296dcfcb79_16.png)

## 因子预处理结果获取

![](img/59af62aa21ea617e48810a296dcfcb79_18.png)

![](img/59af62aa21ea617e48810a296dcfcb79_20.png)

首先，我们需要获取经过完整预处理流程（包括离群值处理、标准化、中性化）后的因子数据。

![](img/59af62aa21ea617e48810a296dcfcb79_22.png)

![](img/59af62aa21ea617e48810a296dcfcb79_24.png)

![](img/59af62aa21ea617e48810a296dcfcb79_26.png)

```python
# 获取预处理后的因子数据（例如市净率PB）
fundamental_df = get_fundamental_data()  # 假设此函数返回包含所需指标的DataFrame
processed_factor = preprocess_operation(fundamental_df['PB'])  # 对‘PB’列进行预处理
```

![](img/59af62aa21ea617e48810a296dcfcb79_28.png)

![](img/59af62aa21ea617e48810a296dcfcb79_30.png)

这里，`preprocess_operation` 函数封装了之前课程中编写的三步操作：`3sigma` 去极值、`standardize` 标准化和 `neutralize` 行业中性化。`processed_factor` 即为我们最终用于选股的因子序列。

![](img/59af62aa21ea617e48810a296dcfcb79_32.png)

## 基于因子进行股票筛选

![](img/59af62aa21ea617e48810a296dcfcb79_34.png)

获得处理后的因子后，下一步是根据因子值对股票进行筛选。我们通常认为市净率较低的股票可能更具投资价值。

![](img/59af62aa21ea617e48810a296dcfcb79_36.png)

以下是筛选操作步骤：

1.  **确定分位点**：首先计算因子的特定分位数，作为筛选阈值。例如，我们选取因子值最小的前20%的股票。
    ```python
    threshold = processed_factor.quantile(0.2)
    ```
    这行代码计算了 `processed_factor` 的20%分位数，`threshold` 即为筛选的临界值。

![](img/59af62aa21ea617e48810a296dcfcb79_38.png)

![](img/59af62aa21ea617e48810a296dcfcb79_40.png)

2.  **执行筛选**：根据阈值，筛选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = processed_factor[processed_factor <= threshold].index.tolist()
    ```
    这行代码通过布尔索引，找出所有因子值小于等于 `threshold` 的股票，并将其代码列表存入 `selected_stocks`。这个列表就是我们的目标股票池。

![](img/59af62aa21ea617e48810a296dcfcb79_42.png)

![](img/59af62aa21ea617e48810a296dcfcb79_44.png)

## 调仓逻辑：确定待卖出股票

![](img/59af62aa21ea617e48810a296dcfcb79_46.png)

![](img/59af62aa21ea617e48810a296dcfcb79_48.png)

![](img/59af62aa21ea617e48810a296dcfcb79_50.png)

在实盘交易中，我们通常需要根据新的股票池调整现有持仓。这意味着需要卖出不再存在于新股票池中的股票，并买入新加入的股票。

![](img/59af62aa21ea617e48810a296dcfcb79_52.png)

以下是确定待卖出股票的逻辑：

![](img/59af62aa21ea617e48810a296dcfcb79_54.png)

![](img/59af62aa21ea617e48810a296dcfcb79_56.png)

![](img/59af62aa21ea617e48810a296dcfcb79_58.png)

1.  **获取当前持仓**：首先，需要获取当前账户中持有的所有股票。
    ```python
    current_holdings = list(context.portfolio.positions.keys())
    ```
    这里假设 `context.portfolio.positions` 是一个字典，包含了当前所有持仓信息，其键（keys）为股票代码。

![](img/59af62aa21ea617e48810a296dcfcb79_60.png)

2.  **计算差异集合**：对比当前持仓 `current_holdings` 和目标股票池 `selected_stocks`，找出存在于当前持仓但不在目标池中的股票，这些就是需要卖出的股票。
    ```python
    stocks_to_sell = set(current_holdings).difference(set(selected_stocks))
    ```
    使用集合的 `difference` 方法可以高效地计算出这个差异。`stocks_to_sell` 集合包含了所有需要清仓的股票代码。

## 总结

![](img/59af62aa21ea617e48810a296dcfcb79_62.png)

本节课中我们一起学习了量化策略中股票池筛选与调仓的关键步骤。我们首先回顾并应用了完整的因子预处理流程，然后基于处理后的因子设定了筛选条件（如前20%分位数），从而得到了目标股票池 `selected_stocks`。最后，我们通过对比当前持仓与目标股票池，逻辑清晰地推导出了需要卖出的股票列表 `stocks_to_sell`，为下一节课执行具体的交易订单打下了基础。