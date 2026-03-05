# Python金融分析与量化交易实战：P39：5-策略效果评估分析 📊

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_1.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_3.png)

在本节课中，我们将学习如何对一个已构建的量化交易策略进行效果评估分析。我们将通过回测运行策略，观察其表现，并理解策略代码的执行流程与逻辑。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_5.png)

## 策略执行与调仓逻辑

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_7.png)

上一节我们介绍了策略的构建框架，本节中我们来看看策略的具体执行和调仓操作。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_9.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_11.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_13.png)

首先，策略需要定期检查当前持有的股票是否符合筛选条件。如果某些股票不再满足要求，就需要将其从投资组合中移除。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_15.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_17.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_19.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_21.png)

```python
# 判断是否需要执行调仓操作
if len(stocks_to_delete) > 0:
    print(f"执行调仓操作")
```

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_23.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_25.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_27.png)

如果确实有股票需要调整，则执行调仓。调仓分为两步：卖出不再符合要求的股票，并买入新进入股票池的股票。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_29.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_31.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_33.png)

以下是调仓操作的具体步骤：

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_35.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_37.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_39.png)

1.  **卖出操作**：遍历需要删除的股票列表，使用 `order_target_percent` 函数将其持仓比例调整为0，即全部卖出。
    ```python
    for stock in stocks_to_delete:
        order_target_percent(stock, 0)
    ```

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_41.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_43.png)

2.  **买入操作**：遍历当前筛选出的股票池，使用 `order_target_percent` 函数进行买入。为了简化，我们采用等权重配置，即每只股票分配相同的资金比例。
    ```python
    for stock in current_pool:
        order_target_percent(stock, 1.0 / len(current_pool))
    ```

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_45.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_47.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_49.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_51.png)

这样，就完成了一次基于规则的月度调仓操作。策略会每月检查股票池，并及时执行买卖指令以维持投资组合符合既定标准。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_53.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_55.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_57.png)

## 代码调试与运行

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_59.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_61.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_63.png)

在编写和运行策略时，难免会遇到各种错误。我们需要根据错误提示，逐步检查和修正代码。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_65.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_67.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_69.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_71.png)

例如，在运行初期，我们遇到了几个典型错误：

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_73.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_75.png)

*   **参数缺失错误**：在中性化处理函数中，缺少了必要的参数。需要将指标名称正确传入函数。
*   **方法调用错误**：在使用统计模型进行回归分析前，忘记了调用 `fit()` 方法进行训练。
*   **拼写错误**：在函数或变量名中存在拼写错误，例如 `quantile` 拼写成了 `qant`。
*   **变量名错误**：在删除股票的逻辑中，使用了未定义的变量名 `stock_list`，需要修正为正确的变量名。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_77.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_79.png)

经过一系列调试，代码最终成功通过编译并开始执行回测。在回测过程中，控制台会打印出每次调仓的日志，方便我们观察策略的运行节奏。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_81.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_83.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_85.png)

## 回测结果分析

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_87.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_89.png)

策略回测完成后，我们可以从多个维度评估其效果。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_91.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_93.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_95.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_97.png)

**核心评估指标通常包括：**

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_99.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_101.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_103.png)

*   **策略收益率**：策略在整个回测期间的总收益和年化收益。
*   **基准收益率**：作为比较基准的市场指数（如沪深300）在同期的收益。
*   **夏普比率**：衡量策略风险调整后收益的指标，数值越高越好。
*   **最大回撤**：策略净值从峰值到谷底的最大跌幅，反映策略的风险控制能力。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_105.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_107.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_109.png)

在本次示例回测中，我们观察到：
*   策略收益率在大部分时间内为负，但在后期转为正值。
*   策略的最终表现优于基准指数（基准年化收益为负）。
*   夏普比率较低，表明策略的风险调整后收益并不突出。
*   最大回撤约为9%，处于可接受范围。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_111.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_113.png)

这些结果表明，我们构建的基于市值和市净率因子的简单选股策略，在测试区间内表现略优于市场基准，但仍有很大的优化空间。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_115.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_117.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_119.png)

## 策略流程总结

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_121.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_123.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_125.png)

本节课中我们一起学习了量化策略从执行到评估的完整流程。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_127.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_129.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_131.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_133.png)

整个策略的核心流程可以概括为以下几步：

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_135.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_137.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_139.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_141.png)

1.  **初始化与定时**：在策略的 `__init__` 构造函数中设置定时器，决定调仓频率（如每月、每周）。
2.  **股票池过滤**：首先过滤掉不符合基本条件的股票（如ST股、停牌股）。
3.  **因子查询与处理**：对剩余股票查询所需的因子数据（如市值、市净率），并进行必要的预处理操作（如中性化处理）。
4.  **股票筛选**：根据处理后的因子值，应用选股规则（例如，选择市净率最低的20%股票）构建最终的投资组合。
5.  **调仓执行**：比较当前持仓与目标股票池的差异。卖出持仓中不在目标池的股票，买入目标池中尚未持有的股票，并调整至目标权重（如等权重）。
6.  **回测与评估**：运行策略回测，通过收益率、夏普比率、最大回撤等关键指标全面评估策略的历史表现。

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_143.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_145.png)

![](img/e8b0ba031b8e40d55bd49f46cc4a883d_147.png)

通过本节课的学习，我们掌握了量化策略效果评估的基本方法，并理解了策略代码从构建、调试到运行分析的完整链路。这为后续开发更复杂、更有效的交易策略奠定了坚实的基础。