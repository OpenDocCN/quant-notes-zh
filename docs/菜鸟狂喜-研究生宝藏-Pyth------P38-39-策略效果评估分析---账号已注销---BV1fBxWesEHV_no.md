# Python金融量化分析：P38：策略效果评估分析 📊

![](img/462e5936bddb5846a95e6c032da2ac24_1.png)

![](img/462e5936bddb5846a95e6c032da2ac24_3.png)

![](img/462e5936bddb5846a95e6c032da2ac24_5.png)

在本节课中，我们将学习如何评估一个基于因子的选股策略的实际效果。我们将通过回测系统运行策略代码，观察其收益、风险等关键指标，并与基准进行比较，从而判断策略的有效性。

---

![](img/462e5936bddb5846a95e6c032da2ac24_7.png)

![](img/462e5936bddb5846a95e6c032da2ac24_9.png)

![](img/462e5936bddb5846a95e6c032da2ac24_11.png)

上一节我们完成了策略核心逻辑的编写，本节中我们来看看如何运行并评估这个策略。

![](img/462e5936bddb5846a95e6c032da2ac24_13.png)

![](img/462e5936bddb5846a95e6c032da2ac24_15.png)

![](img/462e5936bddb5846a95e6c032da2ac24_17.png)

![](img/462e5936bddb5846a95e6c032da2ac24_19.png)

首先，我们需要确保股票池的更新。在每月调仓时，我们会将不再符合选股条件的股票从当前持仓中移除。

![](img/462e5936bddb5846a95e6c032da2ac24_21.png)

![](img/462e5936bddb5846a95e6c032da2ac24_23.png)

![](img/462e5936bddb5846a95e6c032da2ac24_25.png)

![](img/462e5936bddb5846a95e6c032da2ac24_27.png)

```python
# 判断是否需要调仓
if len(stocks_to_delete) > 0:
    print(f"执行调仓操作，时间：{context.current_dt}")
```

![](img/462e5936bddb5846a95e6c032da2ac24_29.png)

![](img/462e5936bddb5846a95e6c032da2ac24_31.png)

![](img/462e5936bddb5846a95e6c032da2ac24_33.png)

![](img/462e5936bddb5846a95e6c032da2ac24_35.png)

如果确实有股票需要被剔除（即 `stocks_to_delete` 列表长度大于0），则执行调仓操作。以下是调仓的具体步骤：

![](img/462e5936bddb5846a95e6c032da2ac24_37.png)

![](img/462e5936bddb5846a95e6c032da2ac24_39.png)

![](img/462e5936bddb5846a95e6c032da2ac24_41.png)

![](img/462e5936bddb5846a95e6c032da2ac24_43.png)

首先，卖出所有需要剔除的股票。我们使用 `order_target_percent` 函数，将目标股票的持仓比例设置为0，即全部卖出。

![](img/462e5936bddb5846a95e6c032da2ac24_45.png)

![](img/462e5936bddb5846a95e6c032da2ac24_47.png)

```python
for stock in stocks_to_delete:
    order_target_percent(stock, 0)
```

![](img/462e5936bddb5846a95e6c032da2ac24_49.png)

![](img/462e5936bddb5846a95e6c032da2ac24_51.png)

![](img/462e5936bddb5846a95e6c032da2ac24_53.png)

![](img/462e5936bddb5846a95e6c032da2ac24_55.png)

卖出完成后，我们需要买入新的、符合选股条件的股票。我们采用等权重配置的方式，将资金平均分配给股票池中的每一只股票。

![](img/462e5936bddb5846a95e6c032da2ac24_57.png)

![](img/462e5936bddb5846a95e6c032da2ac24_59.png)

![](img/462e5936bddb5846a95e6c032da2ac24_61.png)

![](img/462e5936bddb5846a95e6c032da2ac24_63.png)

```python
# 计算每只股票应占的权重
weight = 1.0 / len(filtered_stocks)
for stock in filtered_stocks:
    order_target_percent(stock, weight)
```

![](img/462e5936bddb5846a95e6c032da2ac24_65.png)

![](img/462e5936bddb5846a95e6c032da2ac24_67.png)

![](img/462e5936bddb5846a95e6c032da2ac24_69.png)

![](img/462e5936bddb5846a95e6c032da2ac24_71.png)

这样，我们就完成了一次完整的调仓操作。策略会每月自动执行此流程，检查并更新持仓。

![](img/462e5936bddb5846a95e6c032da2ac24_73.png)

![](img/462e5936bddb5846a95e6c032da2ac24_75.png)

![](img/462e5936bddb5846a95e6c032da2ac24_77.png)

![](img/462e5936bddb5846a95e6c032da2ac24_79.png)

---

![](img/462e5936bddb5846a95e6c032da2ac24_81.png)

![](img/462e5936bddb5846a95e6c032da2ac24_83.png)

在编写和运行策略代码时，常常会遇到各种错误。以下是我们在调试过程中遇到并解决的一些典型问题：

![](img/462e5936bddb5846a95e6c032da2ac24_85.png)

![](img/462e5936bddb5846a95e6c032da2ac24_87.png)

![](img/462e5936bddb5846a95e6c032da2ac24_89.png)

1.  **函数参数缺失**：在调用中性化处理函数时，漏传了必要的参数（如因子列名）。需要检查函数定义并补全参数。
2.  **模型未训练**：在使用线性回归模型进行中性化前，必须先调用 `fit()` 方法训练模型，然后才能计算残差。
3.  **拼写错误**：代码中的变量名或函数名拼写错误（例如 `quantile` 拼成了 `qant`）。需要仔细检查并修正。
4.  **变量未定义**：在循环或判断中使用了未提前定义的列表变量（如 `stock_list`）。需要确保所有变量在使用前都已正确初始化。

![](img/462e5936bddb5846a95e6c032da2ac24_91.png)

![](img/462e5936bddb5846a95e6c032da2ac24_93.png)

![](img/462e5936bddb5846a95e6c032da2ac24_95.png)

![](img/462e5936bddb5846a95e6c032da2ac24_97.png)

通过逐一解决这些错误，最终代码得以成功编译并运行。

![](img/462e5936bddb5846a95e6c032da2ac24_99.png)

![](img/462e5936bddb5846a95e6c032da2ac24_101.png)

![](img/462e5936bddb5846a95e6c032da2ac24_103.png)

![](img/462e5936bddb5846a95e6c032da2ac24_105.png)

---

![](img/462e5936bddb5846a95e6c032da2ac24_107.png)

![](img/462e5936bddb5846a95e6c032da2ac24_109.png)

![](img/462e5936bddb5846a95e6c032da2ac24_111.png)

程序开始回测后，我们可以在输出日志中观察调仓记录，并在进度条完成后查看策略的绩效分析报告。

![](img/462e5936bddb5846a95e6c032da2ac24_113.png)

![](img/462e5936bddb5846a95e6c032da2ac24_115.png)

![](img/462e5936bddb5846a95e6c032da2ac24_117.png)

关键指标解读：
*   **年化收益率**：策略在回测期间获得的复合年化收益。公式为 `(最终价值 / 初始价值) ^ (1 / 年数) - 1`。
*   **基准年化收益**：作为比较基准的市场指数（如沪深300）在同期的年化收益。
*   **夏普比率**：衡量每承受一单位总风险，能产生多少超额回报。数值越高，风险调整后收益越好。公式为 `(投资组合预期收益率 - 无风险利率) / 投资组合标准差`。
*   **最大回撤**：在选定周期内，资产净值从最高点到最低点的最大跌幅。这是衡量策略下行风险的重要指标。

![](img/462e5936bddb5846a95e6c032da2ac24_119.png)

![](img/462e5936bddb5846a95e6c032da2ac24_121.png)

![](img/462e5936bddb5846a95e6c032da2ac24_123.png)

在我们的示例回测中，策略的年化收益虽然不高，但在市场基准收益为负的情况下，取得了正收益，表明策略在一定程度上有抗跌或选股能力。夏普比率较低，说明收益波动相对较大。最大回撤控制在10%以内，属于可接受范围。

![](img/462e5936bddb5846a95e6c032da2ac24_125.png)

![](img/462e5936bddb5846a95e6c032da2ac24_127.png)

![](img/462e5936bddb5846a95e6c032da2ac24_129.png)

![](img/462e5936bddb5846a95e6c032da2ac24_131.png)

![](img/462e5936bddb5846a95e6c032da2ac24_133.png)

---

![](img/462e5936bddb5846a95e6c032da2ac24_135.png)

![](img/462e5936bddb5846a95e6c032da2ac24_137.png)

![](img/462e5936bddb5846a95e6c032da2ac24_139.png)

![](img/462e5936bddb5846a95e6c032da2ac24_141.png)

策略的核心流程可以总结为以下几步：
1.  **初始化与定时**：在 `initialize` 函数中设置定时器（如每月调仓）。
2.  **股票池过滤**：在 `before_trading_start` 中，过滤掉ST股、停牌股等不符合基本条件的股票。
3.  **因子查询与处理**：获取剩余股票的因子数据（如市值、市净率），并进行标准化、中性化等预处理。
4.  **因子选股**：根据处理后的因子值设定规则（如市净率低于某个分位数）进行筛选，得到目标股票池。
5.  **调仓执行**：比较当前持仓与目标股票池，卖出不在池中的股票，并等权重买入池中新股票。

![](img/462e5936bddb5846a95e6c032da2ac24_143.png)

![](img/462e5936bddb5846a95e6c032da2ac24_145.png)

![](img/462e5936bddb5846a95e6c032da2ac24_147.png)

![](img/462e5936bddb5846a95e6c032da2ac24_149.png)

本节课中我们一起学习了如何运行并评估一个量化选股策略。我们实践了从代码编写、调试到回测分析的全过程，理解了如何通过关键绩效指标来评判策略的优劣。量化交易是一个不断迭代和优化的过程，本课提供的框架和思路是后续更复杂策略开发的坚实基础。