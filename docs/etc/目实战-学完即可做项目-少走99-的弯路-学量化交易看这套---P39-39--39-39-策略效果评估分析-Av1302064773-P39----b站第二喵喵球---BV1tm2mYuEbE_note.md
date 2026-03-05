# 量化交易实战：P39：策略效果评估与调仓实现 📊

![](img/2c5e1de95e88241948267fb2fe4efc0e_1.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_3.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_5.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_7.png)

在本节课中，我们将学习如何评估一个量化策略的效果，并实现一个基于因子选股的月度调仓策略。我们将通过编写代码，完成从数据预处理、股票筛选到执行买卖指令的完整流程，并观察策略的回测表现。

![](img/2c5e1de95e88241948267fb2fe4efc0e_9.png)

---

![](img/2c5e1de95e88241948267fb2fe4efc0e_11.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_13.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_15.png)

上一节我们介绍了因子选股的基本逻辑，本节中我们来看看如何将理论转化为代码，并执行调仓操作。

![](img/2c5e1de95e88241948267fb2fe4efc0e_17.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_19.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_21.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_23.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_25.png)

首先，我们需要从当前持仓中移除那些不再符合选股条件的股票。以下代码实现了这一筛选过程：

![](img/2c5e1de95e88241948267fb2fe4efc0e_27.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_29.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_31.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_33.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_35.png)

```python
# 假设 stocks_to_delete 是计算出的需要卖出的股票列表
if len(stocks_to_delete) > 0:
    print(f"执行调仓操作，时间：{context.current_dt}")
    # 卖出不再符合条件的股票
    for stock in stocks_to_delete:
        order_target_percent(stock, 0)
```

![](img/2c5e1de95e88241948267fb2fe4efc0e_37.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_39.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_41.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_43.png)

完成卖出操作后，我们需要将资金重新分配到符合条件的新股票池中。以下是买入操作的实现：

![](img/2c5e1de95e88241948267fb2fe4efc0e_45.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_47.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_49.png)

```python
# 假设 stock_pool 是经过筛选后的目标股票池
for stock in stock_pool:
    # 将资金平均分配到每只股票
    order_target_percent(stock, 1.0 / len(stock_pool))
```

![](img/2c5e1de95e88241948267fb2fe4efc0e_51.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_53.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_55.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_57.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_59.png)

这样，我们就完成了一次完整的调仓操作。策略的核心是定期（例如每月初）执行上述的`rebalance`函数，检查持仓并调整至目标组合。

![](img/2c5e1de95e88241948267fb2fe4efc0e_61.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_63.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_65.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_67.png)

---

![](img/2c5e1de95e88241948267fb2fe4efc0e_69.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_71.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_73.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_75.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_77.png)

在编写和调试策略时，常常会遇到各种错误。以下是几个常见的错误及其解决方法：

![](img/2c5e1de95e88241948267fb2fe4efc0e_79.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_81.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_83.png)

1.  **函数参数错误**：例如，在中性化处理函数中漏传了必要的参数。需要检查函数定义并补全参数。
2.  **拼写错误**：变量或函数名拼写错误，例如将`quantile`误写为`qantile`。需要仔细核对代码。
3.  **未执行模型训练**：在使用统计模型（如OLS回归）前，必须先调用`fit()`方法进行训练，然后才能使用其结果。
4.  **对象属性错误**：尝试访问不存在的对象属性，例如在`Series`对象上调用错误的方法。需查阅文档确认正确的用法。

![](img/2c5e1de95e88241948267fb2fe4efc0e_85.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_87.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_89.png)

通过逐步修正这些错误，我们可以让策略顺利运行起来。

![](img/2c5e1de95e88241948267fb2fe4efc0e_91.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_93.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_95.png)

---

![](img/2c5e1de95e88241948267fb2fe4efc0e_97.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_99.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_101.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_103.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_105.png)

策略回测完成后，我们需要评估其表现。主要关注以下几个指标：

![](img/2c5e1de95e88241948267fb2fe4efc0e_107.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_109.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_111.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_113.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_115.png)

*   **累计收益率**：策略在整个回测期间的总收益。
*   **年化收益率**：将累计收益率折算为每年的平均收益。
*   **基准收益率**：同期市场基准（如沪深300指数）的收益，用于对比。
*   **最大回撤**：策略净值从高点下降到低点的最大幅度，衡量策略的风险。
*   **夏普比率**：衡量每承受一单位风险，所获得的超额收益。

![](img/2c5e1de95e88241948267fb2fe4efc0e_117.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_119.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_121.png)

在示例回测中，策略虽然年化收益率不高，但在市场基准为负的情况下，取得了正向收益，并且最大回撤控制在一定范围内，说明策略在一定程度上跑赢了市场并控制了风险。

![](img/2c5e1de95e88241948267fb2fe4efc0e_123.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_125.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_127.png)

---

![](img/2c5e1de95e88241948267fb2fe4efc0e_129.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_131.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_133.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_135.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_137.png)

本节课中我们一起学习了量化策略从编码到回测评估的全过程。我们实现了一个基于市值和市净率因子的月度调仓策略，其核心步骤包括：

![](img/2c5e1de95e88241948267fb2fe4efc0e_139.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_141.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_143.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_145.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_147.png)

1.  **定时触发**：在策略初始化时设置按月调仓的定时器。
2.  **股票筛选**：过滤掉不符合条件的股票（如ST股、停牌股），并查询所需因子数据。
3.  **数据处理与选股**：对因子进行预处理（如中性化），并根据规则（如因子值从小到大排序选取前N%）筛选出目标股票池。
4.  **执行调仓**：卖出持仓中不在目标池的股票，并将资金平均买入目标池中的所有股票。
5.  **效果评估**：通过回测结果分析策略的收益、风险和稳定性。

![](img/2c5e1de95e88241948267fb2fe4efc0e_149.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_151.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_153.png)

![](img/2c5e1de95e88241948267fb2fe4efc0e_155.png)

需要注意的是，本策略仅为教学示例，展示了量化研究的基本流程。实际应用中需要考虑更多因素，如交易成本、滑点、更复杂的因子模型和风险控制等。