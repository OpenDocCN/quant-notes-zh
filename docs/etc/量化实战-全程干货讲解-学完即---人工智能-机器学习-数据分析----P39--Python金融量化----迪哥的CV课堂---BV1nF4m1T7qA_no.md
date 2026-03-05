# Python金融量化：P39：策略效果评估分析 📊

![](img/a2cff44e856ecc2252d7d08b1861855d_1.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_3.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_5.png)

在本节课中，我们将学习如何对一个已构建的量化交易策略进行效果评估分析。我们将通过回测程序，观察策略的执行过程、调仓操作以及最终的收益表现，从而判断策略的有效性。

---

![](img/a2cff44e856ecc2252d7d08b1861855d_7.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_9.png)

上一节我们介绍了策略的构建逻辑，本节中我们来看看如何运行并评估这个策略。

![](img/a2cff44e856ecc2252d7d08b1861855d_11.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_13.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_15.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_17.png)

首先，我们需要确保当前持仓中不再包含不符合筛选条件的股票。以下是执行此操作的代码片段：

![](img/a2cff44e856ecc2252d7d08b1861855d_19.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_21.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_23.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_25.png)

```python
# 假设 stocks_to_remove 是需要从持仓中移除的股票列表
if len(stocks_to_remove) > 0:
    print("开始执行调仓操作...")
    # 卖出不在股票池中的股票
    for stock in stocks_to_remove:
        order_target_percent(stock, 0)
```

![](img/a2cff44e856ecc2252d7d08b1861855d_27.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_29.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_31.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_33.png)

接下来，我们需要买入符合筛选条件的新股票。以下是买入操作的代码：

![](img/a2cff44e856ecc2252d7d08b1861855d_35.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_37.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_39.png)

```python
# 假设 stock_pool 是经过筛选后的目标股票池
for stock in stock_pool:
    order_target_percent(stock, 1.0 / len(stock_pool))
```

![](img/a2cff44e856ecc2252d7d08b1861855d_41.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_43.png)

这样，我们就完成了一次完整的调仓操作。策略会定期（例如每月初）执行上述逻辑，检查并调整持仓。

![](img/a2cff44e856ecc2252d7d08b1861855d_45.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_47.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_49.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_51.png)

---

![](img/a2cff44e856ecc2252d7d08b1861855d_53.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_55.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_57.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_59.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_61.png)

在运行回测程序时，可能会遇到一些错误。以下是常见的调试步骤：

![](img/a2cff44e856ecc2252d7d08b1861855d_63.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_65.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_66.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_67.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_69.png)

1.  **检查函数参数**：确保调用函数时传递了所有必需的参数。
2.  **检查变量名拼写**：仔细核对代码中所有变量和函数名的拼写。
3.  **检查数据处理流程**：确保数据预处理（如中性化）步骤完整，例如模型训练必须在计算残差之前完成。

![](img/a2cff44e856ecc2252d7d08b1861855d_71.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_73.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_75.png)

通过逐步修正这些错误，程序最终可以成功编译并运行。

![](img/a2cff44e856ecc2252d7d08b1861855d_77.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_79.png)

---

![](img/a2cff44e856ecc2252d7d08b1861855d_81.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_83.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_85.png)

程序运行后，我们可以通过输出日志观察策略的执行情况。例如，可以看到在每月的调仓日，程序会打印“开始执行调仓操作...”的提示。

![](img/a2cff44e856ecc2252d7d08b1861855d_87.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_89.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_91.png)

同时，回测引擎会生成策略的绩效报告。我们需要关注以下几个核心指标：

![](img/a2cff44e856ecc2252d7d08b1861855d_93.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_95.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_97.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_99.png)

*   **累计收益率**：策略在整个回测期间的总收益。
*   **年化收益率**：将累计收益率折算成年化的收益水平。
*   **基准收益率**：同期市场基准（如沪深300指数）的收益，用于对比。
*   **夏普比率**：衡量策略承担单位风险所获得的超额回报。公式为：`(策略年化收益率 - 无风险利率) / 策略年化波动率`。
*   **最大回撤**：策略在任一历史时点从峰值到谷底的最大亏损幅度。

![](img/a2cff44e856ecc2252d7d08b1861855d_101.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_103.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_105.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_107.png)

在示例回测中，策略的收益曲线可能在某些时段优于基准指数，这表明因子选股逻辑起到了一定的作用。

![](img/a2cff44e856ecc2252d7d08b1861855d_109.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_111.png)

---

![](img/a2cff44e856ecc2252d7d08b1861855d_113.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_115.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_117.png)

以下是回测完成后，查看详细结果的步骤：

![](img/a2cff44e856ecc2252d7d08b1861855d_119.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_121.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_123.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_125.png)

1.  观察**收益对比图**，直观比较策略收益与基准收益的走势。
2.  查看**绩效摘要**，获取年化收益、夏普比率、最大回撤等关键数据。
3.  分析**交易详情**，了解每一次调仓的具体买卖记录。
4.  查看**持仓历史**，跟踪策略在不同时间点的资产配置变化。

![](img/a2cff44e856ecc2252d7d08b1861855d_127.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_129.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_131.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_133.png)

这些详细信息有助于我们深入理解策略的行为模式，并为后续的优化提供依据。

![](img/a2cff44e856ecc2252d7d08b1861855d_135.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_137.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_139.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_141.png)

---

![](img/a2cff44e856ecc2252d7d08b1861855d_143.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_145.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_147.png)

![](img/a2cff44e856ecc2252d7d08b1861855d_149.png)

本节课中我们一起学习了量化策略的回测与评估流程。我们首先编写了策略的调仓逻辑，然后通过回测运行并调试程序，最后分析了策略的绩效报告和各项关键指标。这个过程涵盖了从策略实现到效果验证的完整步骤，是量化交易实践中至关重要的一环。