# Python金融量化交易实战教程：P23：3-策略效果演示与指标分析 📊

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_1.png)

在本节课中，我们将学习如何对量化交易策略进行效果演示与指标分析。我们将通过调整数据结构、实现交易逻辑，并最终运行回测来观察策略的表现。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_3.png)

---

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_5.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_7.png)

上一节我们介绍了策略的初步框架，本节中我们来看看如何具体实现交易逻辑并分析其效果。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_9.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_11.png)

## 数据结构的调整

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_13.png)

我们最初的想法是将数据横向排列，即每个股票名称作为一列。但通常我们更喜欢纵向排列数据，即每一行代表一只股票及其财务指标。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_15.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_17.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_18.png)

以下是调整数据结构的步骤：

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_20.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_22.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_24.png)

1.  首先，我们需要对原始的 `DataFrame` 进行转置操作。
2.  转置后，数据将变为纵向排列，每一行对应一只股票。
3.  我们的目标是获取转置后数据的索引，即股票名称列表。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_26.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_28.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_30.png)

核心操作是使用 `DataFrame` 的转置方法：
```python
transposed_df = original_df.T
```

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_32.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_34.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_36.png)

转置完成后，数据呈现为纵向结构，索引列即为股票名称，后续列为其对应的财务指标。此时，我们只需提取索引即可获得股票列表。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_38.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_40.png)

## 实现交易逻辑

在 `handle_bar` 函数中，我们需要实现具体的买卖逻辑。首先，需要判断当前账户的持仓情况。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_42.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_44.png)

以下是实现交易逻辑的步骤：

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_46.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_48.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_50.png)

1.  通过 `context.portfolio.positions` 获取当前所有持仓，这是一个字典，键为股票代码。
2.  判断持仓字典是否为空。如果为空，说明是首次运行，需要执行买入操作。
3.  如果持仓不为空，则需要遍历当前持仓，卖出那些不在最新股票池中的股票。
4.  接着，遍历最新的股票池，买入那些当前未持仓或需要调整仓位的股票。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_52.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_54.png)

以下是关键的API调用示例：
*   卖出不在股票池中的股票：`order_target_percent(stock, 0)`。参数 `0` 表示将该股票仓位调整至0%，即全部卖出。
*   买入股票池中的股票：`order_target_percent(stock, weight)`。参数 `weight` 表示目标仓位占总资产的比例，这里我们可以选择平均分配。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_56.png)

## 策略回测与结果分析

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_58.png)

完成代码编写后，我们运行回测来观察策略表现。回测结果会提供一系列关键指标。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_60.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_62.png)

以下是主要的分析指标及其含义：

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_64.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_66.png)

*   **累计收益率**：策略在整个回测期间的总收益。
*   **年化收益率**：将累计收益率折算为年化数据，便于比较。
*   **基准收益率**：同期市场指数（如沪深300）的收益率，用于对比。
*   **阿尔法（Alpha）与贝塔（Beta）**：衡量策略相对于市场的超额收益和系统性风险。
*   **夏普比率（Sharpe Ratio）**：衡量每承受一单位风险所产生的超额回报。负值表示收益不如无风险利率。
*   **最大回撤（Max Drawdown）**：策略净值从高点回落至最低点的最大幅度，反映极端风险。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_68.png)

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_70.png)

初次回测结果可能不理想（例如出现亏损），这可能是回测时间段选择或策略逻辑本身所致。我们需要对比策略收益曲线与基准收益曲线，即使两者都下跌，策略若能减少亏损或控制回撤，也体现了其价值。此外，应关注交易详情，检查买卖点是否合理。

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_72.png)

---

![](img/b9a4f0b7b1c0a2ae71c27075aa9beb5b_74.png)

本节课中我们一起学习了量化策略的完整实现流程：从数据预处理、交易逻辑编码到回测分析与指标解读。关键在于理解每个步骤的目的，并能根据回测结果反思和优化策略逻辑。