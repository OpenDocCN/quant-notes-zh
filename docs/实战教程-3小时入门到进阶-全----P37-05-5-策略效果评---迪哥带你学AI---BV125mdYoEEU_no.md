# Python金融分析与量化交易实战：P37：05-5-策略效果评估分析 📊

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其效果进行评估分析。我们将编写代码来执行月度调仓操作，处理数据，并最终观察策略的回测表现。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_1.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_3.png)

---

上一节我们介绍了策略的基本框架和定时器设置。本节中，我们来看看如何实现具体的调仓逻辑和策略评估。

## 执行调仓操作

![](img/d213033f0c1b61da4c2dde4cb292ce1d_5.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_7.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_9.png)

现在，我们已经从股票池中删除了不符合条件的股票。接下来，需要执行调仓操作。以下是具体步骤：

![](img/d213033f0c1b61da4c2dde4cb292ce1d_11.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_13.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_15.png)

首先，判断是否需要调仓。如果被删除的股票数量大于零，说明需要执行调仓。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_17.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_19.png)

```python
if len(stocks_to_delete) > 0:
    print("执行调仓操作")
```

![](img/d213033f0c1b61da4c2dde4cb292ce1d_21.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_23.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_25.png)

接下来，执行卖出操作。遍历所有需要删除的股票，将其持仓比例调整为0。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_27.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_29.png)

```python
for stock in stocks_to_delete:
    order_target_percent(stock, 0)
```

![](img/d213033f0c1b61da4c2dde4cb292ce1d_31.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_33.png)

卖出完成后，需要买入新的股票。遍历当前股票池中的所有股票，并平均分配资金买入。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_35.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_37.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_39.png)

```python
for stock in current_pool:
    order_target_percent(stock, 1.0 / len(current_pool))
```

![](img/d213033f0c1b61da4c2dde4cb292ce1d_41.png)

这样，我们就完成了一次完整的调仓操作。每个月定时器触发时，都会执行此流程，检查股票池并调整持仓。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_43.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_45.png)

---

![](img/d213033f0c1b61da4c2dde4cb292ce1d_47.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_49.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_51.png)

## 代码调试与运行

![](img/d213033f0c1b61da4c2dde4cb292ce1d_53.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_55.png)

在编写完代码后，直接运行可能会遇到各种错误。以下是调试过程中遇到的一些典型问题及其解决方法：

![](img/d213033f0c1b61da4c2dde4cb292ce1d_57.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_59.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_61.png)

**问题1：中性化函数缺少参数。**
在调用中性化函数时，需要传入正确的参数名。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_63.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_65.png)

```python
# 错误示例
neutralized_data = neutralize(factor_data, 'market_cap')
# 正确示例
neutralized_data = neutralize(factor_data, 'market_cap', 'factor_name')
```

![](img/d213033f0c1b61da4c2dde4cb292ce1d_67.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_69.png)

**问题2：未训练模型就进行预测。**
在使用线性回归等模型进行中性化处理前，必须先调用 `fit` 方法进行训练。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_71.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_73.png)

```python
model = LinearRegression()
model.fit(X, y)  # 必须先训练
residuals = y - model.predict(X)
```

![](img/d213033f0c1b61da4c2dde4cb292ce1d_75.png)

**问题3：变量名拼写错误。**
仔细检查代码中的变量名，确保与定义时一致。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_77.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_79.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_81.png)

```python
# 错误示例
stok_list = [...]
# 正确示例
stock_list = [...]
```

通过逐步修正这些错误，最终代码能够成功编译并运行。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_83.png)

---

![](img/d213033f0c1b61da4c2dde4cb292ce1d_85.png)

## 策略回测结果分析

![](img/d213033f0c1b61da4c2dde4cb292ce1d_87.png)

程序运行后，我们可以观察策略的回测表现。控制台会打印每次调仓的日志，并最终输出策略的绩效指标。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_89.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_91.png)

关键绩效指标包括：
*   **年化收益率**：策略在一年内的平均收益率。
*   **基准收益率**：对照基准（如沪深300指数）的年化收益率。
*   **夏普比率**：衡量策略风险调整后收益的指标。
*   **最大回撤**：策略净值从峰值到谷底的最大跌幅。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_93.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_95.png)

在我们的示例回测中，策略的年化收益率虽然不高，但在所选测试期内，其表现优于基准指数，且最大回撤控制在可接受范围内。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_97.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_99.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_101.png)

---

![](img/d213033f0c1b61da4c2dde4cb292ce1d_103.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_105.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_107.png)

## 策略流程总结

![](img/d213033f0c1b61da4c2dde4cb292ce1d_109.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_111.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_113.png)

本节课中我们一起学习了构建一个因子选股策略的完整流程：

![](img/d213033f0c1b61da4c2dde4cb292ce1d_115.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_117.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_119.png)

1.  **初始化与定时**：在 `initialize` 函数中设置按月执行的定时器。
2.  **股票过滤**：在定时函数中，首先过滤掉不满足基本条件的股票（如ST股、停牌股）。
3.  **因子计算与处理**：对剩余股票计算选股因子（如市值、市净率），并进行必要的数据预处理（如中性化处理）。
4.  **股票筛选**：根据处理后的因子值，筛选出符合策略逻辑的股票，形成当期股票池。
5.  **调仓执行**：
    *   卖出当前持有但不在新股票池中的股票（设置持仓比例为0）。
    *   买入新股票池中的所有股票，并平均分配资金。
6.  **效果评估**：运行回测，通过年化收益率、夏普比率、最大回撤等指标评估策略效果。

![](img/d213033f0c1b61da4c2dde4cb292ce1d_121.png)

![](img/d213033f0c1b61da4c2dde4cb292ce1d_123.png)

这个流程清晰地展示了从想法到代码实现，再到结果评估的量化策略开发路径。需要注意的是，本教程内容仅用于学习和回测演示，实际市场应用需考虑更多复杂因素。