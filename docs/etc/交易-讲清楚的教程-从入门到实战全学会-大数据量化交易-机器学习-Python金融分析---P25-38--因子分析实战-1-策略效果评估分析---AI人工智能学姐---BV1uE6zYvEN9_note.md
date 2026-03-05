# Python金融量化+股票交易：P25：因子分析实战1-策略效果评估分析 📊

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_1.png)

在本节课中，我们将学习如何构建一个基于因子的选股策略，并对其进行回测和效果评估。我们将通过编写代码，实现每月调仓、因子筛选、数据预处理以及最终的交易执行逻辑。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_3.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_5.png)

---

## 策略构建与调仓逻辑

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_7.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_9.png)

上一节我们介绍了因子选股的基本概念，本节中我们来看看如何将理论转化为实际的代码逻辑。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_11.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_13.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_15.png)

首先，我们需要从当前股票池中删除不再符合筛选条件的股票。如果确实有股票被删除，即需要执行调仓操作，我们将打印一条信息以便观察计时器是否正常工作。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_17.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_19.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_21.png)

以下是执行调仓操作的核心步骤：

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_23.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_25.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_27.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_29.png)

1.  **卖出不再持有的股票**：遍历需要删除的股票列表，对每只股票执行卖出操作，将其持仓比例调整为0。
    ```python
    for stock in stocks_to_delete:
        order_target_percent(stock, 0)
    ```

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_31.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_33.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_35.png)

2.  **买入新入选的股票**：遍历当前筛选后的股票池，对每只股票执行买入操作。为了简化，我们采用等权重分配资金的方式。
    ```python
    for stock in current_pool:
        order_target_percent(stock, 1.0 / len(current_pool))
    ```

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_37.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_39.png)

这样，我们就完成了一次完整的调仓操作。该逻辑会每月执行一次，以检查并更新我们的持仓组合。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_41.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_43.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_45.png)

---

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_47.png)

## 代码调试与错误修正

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_49.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_51.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_53.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_55.png)

在编写和运行策略代码时，难免会遇到错误。本节我们将一起查看并修正几个常见的编码错误，确保策略能够顺利运行。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_57.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_59.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_61.png)

首先，在数据预处理（如中性化处理）的函数调用中，我们遗漏了必要的参数。需要将因子名称作为参数正确传入。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_63.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_65.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_67.png)

```python
# 修正前（缺少因子名称参数）
neutralized_factor = neutralize(factor_data)
# 修正后
neutralized_factor = neutralize(factor_data, ‘factor_name‘)
```

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_69.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_71.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_73.png)

其次，在使用线性回归模型进行中性化处理时，我们忘记了调用 `fit` 方法进行模型训练。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_75.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_77.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_79.png)

```python
# 修正前
model = OLS(y, X)
# 修正后
model = OLS(y, X).fit()
```

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_81.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_83.png)

此外，还有一些拼写错误，例如将 `quantile` 误写为 `qantile`，以及变量名 `stock_list` 漏写了 `list`。逐一修正这些错误后，代码便能通过编译并开始执行回测。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_85.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_87.png)

---

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_89.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_91.png)

## 回测执行与结果分析

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_93.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_95.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_97.png)

代码修正完毕后，我们启动回测。策略会按月执行调仓，并在每次调仓时打印日志。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_99.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_101.png)

在回测过程中，我们可以观察策略收益曲线与基准收益曲线（如沪深300指数）的对比。在示例回测中，尽管基准年化收益为负，但我们的策略收益在部分时间段内实现了正值，表现相对优于基准。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_103.png)

关键绩效指标分析：
*   **年化收益率**：策略收益与基准收益的对比。
*   **夏普比率**：衡量策略风险调整后的收益，虽然示例中该值较低。
*   **最大回撤**：策略资产净值从峰值到谷底的最大跌幅，示例中约为9%。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_105.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_107.png)

这些指标共同帮助我们评估策略的有效性和风险水平。需要注意的是，本例仅为演示，因子选择（如市值、市净率）和参数设置都较为简单。实际应用中需要更严谨的因子研究和参数优化。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_109.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_111.png)

---

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_113.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_115.png)

## 策略流程总结

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_117.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_119.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_121.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_123.png)

本节课中我们一起学习了构建一个完整因子选股策略的流程：

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_125.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_127.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_129.png)

1.  **初始化与定时**：在策略初始化函数中设定调仓频率（如每月）。
2.  **股票池过滤**：剔除不符合基本条件的股票（如ST股、停牌股）。
3.  **因子获取与处理**：获取所需因子数据，并进行必要的预处理（如中性化处理）。
4.  **因子筛选**：根据因子逻辑（如市净率越低越好）筛选出目标股票池。
5.  **调仓执行**：
    *   卖出持仓中但不在新股票池的股票。
    *   买入新股票池中的股票，可按等权重或其他方式分配资金。

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_131.png)

![](img/cf006c2f4b8c3fe61481c0c4ec37d395_133.png)

最终，我们通过回测平台得到了策略的交易详情、净值曲线和各项绩效指标。这为我们验证投资想法、优化策略逻辑提供了重要的实践工具。记住，回测结果不代表未来表现，实际投资需谨慎。