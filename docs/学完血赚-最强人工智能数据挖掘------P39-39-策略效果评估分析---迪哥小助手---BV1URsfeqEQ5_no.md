# Python金融量化分析：P39：策略效果评估分析 📊

![](img/6782095d3740028bf23e34b75fd2fcbc_1.png)

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其进行回测和效果评估。我们将编写代码来执行月度调仓操作，处理数据，并最终分析策略的收益表现。

![](img/6782095d3740028bf23e34b75fd2fcbc_3.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_5.png)

---

## 策略逻辑与代码实现

![](img/6782095d3740028bf23e34b75fd2fcbc_7.png)

上一节我们介绍了策略的基本框架，本节中我们来看看具体的代码实现和调仓逻辑。

![](img/6782095d3740028bf23e34b75fd2fcbc_9.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_10.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_11.png)

首先，我们需要判断当前持有的股票中，哪些不再满足我们的选股条件，并将其卖出。

![](img/6782095d3740028bf23e34b75fd2fcbc_12.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_14.png)

```python
if len(stocks_to_remove) > 0:
    print("执行调仓操作")
    for stock in stocks_to_remove:
        order_target_percent(stock, 0)
```

![](img/6782095d3740028bf23e34b75fd2fcbc_15.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_16.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_18.png)

以上代码判断需要移除的股票列表是否不为空。如果不为空，则遍历列表，并使用 `order_target_percent` 函数将每只股票的持仓比例调整为0，即全部卖出。

![](img/6782095d3740028bf23e34b75fd2fcbc_20.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_21.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_23.png)

接下来，我们需要买入符合当前选股条件的股票。我们将资金平均分配到股票池中的每一只股票上。

![](img/6782095d3740028bf23e34b75fd2fcbc_25.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_26.png)

```python
for stock in current_pool:
    order_target_percent(stock, 1.0 / len(current_pool))
```

![](img/6782095d3740028bf23e34b75fd2fcbc_28.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_29.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_31.png)

这段代码遍历当前的股票池，并使用 `order_target_percent` 函数买入每只股票，持仓比例是总资金的平均分配（1/股票数量）。

这样，我们就完成了一次完整的调仓操作：卖出不符合条件的股票，并买入符合条件的股票。

![](img/6782095d3740028bf23e34b75fd2fcbc_33.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_34.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_35.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_37.png)

---

![](img/6782095d3740028bf23e34b75fd2fcbc_38.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_40.png)

## 代码调试与错误修正

![](img/6782095d3740028bf23e34b75fd2fcbc_42.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_43.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_45.png)

在编写和运行策略代码时，常常会遇到错误。以下是调试过程中发现并修正的几个关键问题。

![](img/6782095d3740028bf23e34b75fd2fcbc_47.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_48.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_50.png)

以下是调试过程中遇到的主要问题及解决方法：

![](img/6782095d3740028bf23e34b75fd2fcbc_51.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_53.png)

1.  **中性化函数参数缺失**：在调用中性化函数时，缺少了必要的参数。需要将指标名称作为参数传入。
    ```python
    # 修正前（可能缺少参数）
    neutralized_data = neutralize(factor_data)
    # 修正后
    neutralized_data = neutralize(factor_data, ‘factor_name‘)
    ```

![](img/6782095d3740028bf23e34b75fd2fcbc_55.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_56.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_57.png)

2.  **未训练模型**：在进行回归分析以获取残差（中性化结果）前，必须先对模型进行拟合（训练）。
    ```python
    # 在计算残差前，必须执行 fit 方法
    model.fit(X, y)
    residuals = y - model.predict(X)
    ```

![](img/6782095d3740028bf23e34b75fd2fcbc_59.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_60.png)

3.  **变量名拼写错误**：代码中存在单词拼写错误，例如将 `quantile` 误写为 `qnt`。
    ```python
    # 修正拼写错误
    selected_stocks = factor_data[factor_data[‘factor‘] <= factor_data[‘factor‘].quantile(0.2)].index.tolist()
    ```

![](img/6782095d3740028bf23e34b75fd2fcbc_62.png)

4.  **未定义的变量**：在循环或判断中使用了未事先定义的列表变量。
    ```python
    # 确保 stocks_to_remove 已被正确定义
    stocks_to_remove = [s for s in current_holdings if s not in current_pool]
    ```

![](img/6782095d3740028bf23e34b75fd2fcbc_64.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_65.png)

通过逐步修正这些错误，代码最终得以成功编译并运行。

![](img/6782095d3740028bf23e34b75fd2fcbc_67.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_68.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_70.png)

---

## 回测执行与结果分析

![](img/6782095d3740028bf23e34b75fd2fcbc_72.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_73.png)

代码调试通过后，我们启动回测。策略设定为每月初执行一次调仓。

![](img/6782095d3740028bf23e34b75fd2fcbc_74.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_75.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_76.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_77.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_78.png)

程序运行后，日志显示在2月1日、3月1日等月初日期成功执行了调仓打印语句，说明定时器工作正常。

![](img/6782095d3740028bf23e34b75fd2fcbc_79.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_81.png)

在回测过程中，我们可以观察策略收益与基准收益（如沪深300指数）的对比。

![](img/6782095d3740028bf23e34b75fd2fcbc_83.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_84.png)

*   在回测初期，策略收益和基准收益可能均为负值。
*   随着时间推移，策略收益逐渐改善，可能转为正值，而基准收益仍为负值。这表明策略在一定时期内跑赢了市场基准。
*   关键指标观察：
    *   **年化收益率**：策略收益与基准收益的对比。
    *   **夏普比率**：衡量策略风险调整后的收益。虽然可能绝对值不高，但相较于基准有改善。
    *   **最大回撤**：策略资产从峰值到谷底的最大跌幅。需要关注其是否在可接受范围内。

最终的回测结果展示了策略在整个回测周期内的表现。通过分析“账户信息”和“收益曲线”，可以直观看到市值的变化情况。通常，策略可能在前期承受亏损，在后期获得正收益，整体实现相对于基准的超额收益。

![](img/6782095d3740028bf23e34b75fd2fcbc_86.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_87.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_88.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_89.png)

---

![](img/6782095d3740028bf23e34b75fd2fcbc_90.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_91.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_92.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_94.png)

## 课程总结

![](img/6782095d3740028bf23e34b75fd2fcbc_95.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_97.png)

本节课中我们一起学习了如何构建并评估一个因子选股策略。

![](img/6782095d3740028bf23e34b75fd2fcbc_99.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_100.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_101.png)

我们首先实现了策略的核心调仓逻辑，即每月定期卖出不符合最新选股条件的股票，并买入新入选的股票。接着，我们经历了实际的代码调试过程，修正了参数缺失、模型未训练、拼写错误等常见问题。最后，我们执行了回测并分析了结果，观察策略是否能够产生超越基准的收益。

![](img/6782095d3740028bf23e34b75fd2fcbc_102.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_103.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_104.png)

![](img/6782095d3740028bf23e34b75fd2fcbc_105.png)

整个流程涵盖了策略开发、编写、调试和评估的基本步骤，是量化交易实战中的重要环节。记住，本教程内容仅用于回测模拟和学习，实际市场应用需充分考虑更多复杂因素。