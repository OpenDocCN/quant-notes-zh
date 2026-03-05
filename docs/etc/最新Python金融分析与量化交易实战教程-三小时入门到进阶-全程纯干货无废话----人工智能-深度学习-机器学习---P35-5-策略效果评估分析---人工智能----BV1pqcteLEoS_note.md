# Python金融量化实战：P35：5-策略效果评估分析 📊

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其进行回测评估。我们将编写代码来筛选股票、执行调仓操作，并分析策略的最终表现。

![](img/fae3669fec4b8c9360e570c269b17059_1.png)

![](img/fae3669fec4b8c9360e570c269b17059_3.png)

---

## 策略实现与调仓逻辑

![](img/fae3669fec4b8c9360e570c269b17059_5.png)

上一节我们介绍了如何筛选股票池，本节中我们来看看如何根据筛选结果执行具体的调仓操作。

![](img/fae3669fec4b8c9360e570c269b17059_7.png)

![](img/fae3669fec4b8c9360e570c269b17059_9.png)

首先，我们需要判断当前持仓中是否有股票不再满足筛选条件。如果存在这样的股票，我们就需要执行调仓操作。

![](img/fae3669fec4b8c9360e570c269b17059_11.png)

![](img/fae3669fec4b8c9360e570c269b17059_13.png)

![](img/fae3669fec4b8c9360e570c269b17059_15.png)

以下是调仓判断的逻辑：
```python
if len(stocks_to_delete) > 0:
    print(f"执行调仓操作，时间：{context.current_dt}")
    # 执行卖出和买入操作
```

![](img/fae3669fec4b8c9360e570c269b17059_17.png)

![](img/fae3669fec4b8c9360e570c269b17059_19.png)

![](img/fae3669fec4b8c9360e570c269b17059_21.png)

如果确实有股票需要被移除（即 `stocks_to_delete` 列表长度大于0），则打印提示信息并开始调仓。

![](img/fae3669fec4b8c9360e570c269b17059_23.png)

![](img/fae3669fec4b8c9360e570c269b17059_25.png)

![](img/fae3669fec4b8c9360e570c269b17059_27.png)

---

![](img/fae3669fec4b8c9360e570c269b17059_29.png)

![](img/fae3669fec4b8c9360e570c269b17059_31.png)

## 执行卖出操作

![](img/fae3669fec4b8c9360e570c269b17059_33.png)

![](img/fae3669fec4b8c9360e570c269b17059_35.png)

对于需要移除的每只股票，我们将其持仓比例调整为0，即全部卖出。

![](img/fae3669fec4b8c9360e570c269b17059_37.png)

![](img/fae3669fec4b8c9360e570c269b17059_39.png)

![](img/fae3669fec4b8c9360e570c269b17059_41.png)

以下是卖出操作的代码：
```python
for stock in stocks_to_delete:
    order_target_percent(stock, 0)
```
这段代码遍历 `stocks_to_delete` 列表中的每只股票，并使用 `order_target_percent` 函数将其目标持仓比例设置为0，完成卖出。

![](img/fae3669fec4b8c9360e570c269b17059_43.png)

---

![](img/fae3669fec4b8c9360e570c269b17059_45.png)

![](img/fae3669fec4b8c9360e570c269b17059_47.png)

![](img/fae3669fec4b8c9360e570c269b17059_49.png)

## 执行买入操作

![](img/fae3669fec4b8c9360e570c269b17059_51.png)

![](img/fae3669fec4b8c9360e570c269b17059_53.png)

卖出完成后，我们需要将资金重新分配到当前股票池的股票中。这里采用等权重分配的方式。

![](img/fae3669fec4b8c9360e570c269b17059_55.png)

![](img/fae3669fec4b8c9360e570c269b17059_57.png)

以下是买入操作的代码：
```python
for stock in current_pool:
    order_target_percent(stock, 1.0 / len(current_pool))
```
这段代码遍历当前股票池 `current_pool` 中的每只股票，计算平均权重（1/股票数量），并使用 `order_target_percent` 函数买入相应的比例。

![](img/fae3669fec4b8c9360e570c269b17059_59.png)

![](img/fae3669fec4b8c9360e570c269b17059_61.png)

![](img/fae3669fec4b8c9360e570c269b17059_63.png)

---

![](img/fae3669fec4b8c9360e570c269b17059_65.png)

![](img/fae3669fec4b8c9360e570c269b17059_67.png)

![](img/fae3669fec4b8c9360e570c269b17059_69.png)

## 代码调试与错误修正

![](img/fae3669fec4b8c9360e570c269b17059_71.png)

在编写和运行策略时，常常会遇到错误。我们需要根据错误提示逐一修正。

![](img/fae3669fec4b8c9360e570c269b17059_73.png)

![](img/fae3669fec4b8c9360e570c269b17059_75.png)

例如，在中心化（中性化）处理时，可能缺少必要的参数或未执行模型训练。

![](img/fae3669fec4b8c9360e570c269b17059_77.png)

![](img/fae3669fec4b8c9360e570c269b17059_79.png)

修正中心化操作的代码可能如下：
```python
# 假设之前缺少 fit 步骤
model = LinearRegression()
model.fit(X, y)  # 添加训练步骤
residuals = y - model.predict(X)
```

![](img/fae3669fec4b8c9360e570c269b17059_81.png)

另一个常见错误是函数名或变量名拼写错误，例如将 `quantile` 误写为 `qantile`，需要仔细检查并更正。

![](img/fae3669fec4b8c9360e570c269b17059_83.png)

---

## 回测结果分析

![](img/fae3669fec4b8c9360e570c269b17059_85.png)

![](img/fae3669fec4b8c9360e570c269b17059_87.png)

策略执行完毕后，我们可以查看回测结果，评估策略性能。

![](img/fae3669fec4b8c9360e570c269b17059_89.png)

![](img/fae3669fec4b8c9360e570c269b17059_91.png)

关键指标包括：
*   **年化收益率**：策略在一年内的平均收益率。
*   **基准收益率**：比较基准（如沪深300指数）的年化收益率。
*   **夏普比率**：衡量风险调整后的收益。
*   **最大回撤**：策略从峰值到谷底的最大亏损幅度。

![](img/fae3669fec4b8c9360e570c269b17059_93.png)

![](img/fae3669fec4b8c9360e570c269b17059_95.png)

在示例回测中，虽然基准收益为负，但我们的策略收益可能为正或负值更小，这表明因子选股策略在一定条件下能产生超额收益或减少亏损。

![](img/fae3669fec4b8c9360e570c269b17059_97.png)

![](img/fae3669fec4b8c9360e570c269b17059_99.png)

![](img/fae3669fec4b8c9360e570c269b17059_101.png)

---

![](img/fae3669fec4b8c9360e570c269b17059_103.png)

![](img/fae3669fec4b8c9360e570c269b17059_105.png)

![](img/fae3669fec4b8c9360e570c269b17059_107.png)

## 策略流程总结

![](img/fae3669fec4b8c9360e570c269b17059_109.png)

![](img/fae3669fec4b8c9360e570c269b17059_111.png)

![](img/fae3669fec4b8c9360e570c269b17059_113.png)

本节课中我们一起学习了构建并回测一个因子选股策略的完整流程。

![](img/fae3669fec4b8c9360e570c269b17059_115.png)

![](img/fae3669fec4b8c9360e570c269b17059_117.png)

![](img/fae3669fec4b8c9360e570c269b17059_119.png)

1.  **初始化与定时器**：在 `initialize` 函数中设置调仓频率（如每月）。
2.  **股票池筛选**：过滤掉不符合条件的股票（如ST股、停牌股）。
3.  **因子查询与处理**：获取所需的因子数据（如市值、市净率），并进行预处理（如中心化）。
4.  **因子选股**：根据因子逻辑（如市净率越低越好）筛选出目标股票池。
5.  **调仓执行**：
    *   卖出持仓中不在新股票池的股票。
    *   买入新股票池中的股票，并进行资金分配（如等权重分配）。
6.  **回测评估**：运行回测，分析策略的收益、风险等关键指标。

![](img/fae3669fec4b8c9360e570c269b17059_121.png)

![](img/fae3669fec4b8c9360e570c269b17059_123.png)

这个流程展示了从想法到代码实现，再到结果评估的完整量化策略开发路径。需要注意的是，回测结果仅代表历史表现，实际市场应用需谨慎。