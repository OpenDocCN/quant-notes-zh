# Python金融时间序列分析与量化交易实战教程：P36：35.策略效果评估分析 📊

![](img/acf9206481d0c57bb2ec6cddb22a08d6_1.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_3.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_5.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_7.png)

在本节课中，我们将学习如何评估一个量化交易策略的效果。我们将通过分析策略的回测结果，包括收益率、夏普比率、最大回撤等关键指标，来理解策略的实际表现。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_9.png)

---

![](img/acf9206481d0c57bb2ec6cddb22a08d6_11.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_13.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_15.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_17.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_19.png)

上一节我们介绍了策略的构建与调仓逻辑，本节中我们来看看如何执行回测并评估策略的最终效果。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_21.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_23.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_25.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_27.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_29.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_31.png)

首先，我们需要确保股票池的更新逻辑正确执行。如果当前持有的股票不在最新的筛选池中，则需要执行卖出操作。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_33.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_35.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_37.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_39.png)

```python
if len(stocks_to_delete) > 0:
    print(f"执行调仓操作，时间：{context.current_dt}")
```

![](img/acf9206481d0c57bb2ec6cddb22a08d6_41.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_43.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_45.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_47.png)

以下是调仓操作的具体步骤：

![](img/acf9206481d0c57bb2ec6cddb22a08d6_49.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_51.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_53.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_55.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_57.png)

1.  **卖出不在池中的股票**：遍历需要删除的股票列表，使用 `order_target_percent` 函数将其持仓比例调整为0，即全部卖出。
    ```python
    for stock in stocks_to_delete:
        order_target_percent(stock, 0)
    ```
2.  **买入池中的股票**：遍历最新的股票池，使用 `order_target_percent` 函数将资金平均分配到每只股票上，实现买入。
    ```python
    for stock in stock_list:
        order_target_percent(stock, 1.0 / len(stock_list))
    ```

![](img/acf9206481d0c57bb2ec6cddb22a08d6_59.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_61.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_63.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_65.png)

在代码执行过程中，可能会遇到一些错误，需要逐一调试解决。例如，在中性化处理函数中缺少必要的参数，或者在调用函数时存在拼写错误。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_67.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_69.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_71.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_73.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_75.png)

```python
# 修正前（缺少参数）
result = sm.OLS(y, X)
# 修正后（添加训练操作）
result = sm.OLS(y, X).fit()
```

![](img/acf9206481d0c57bb2ec6cddb22a08d6_77.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_79.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_81.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_83.png)

完成代码修正后，运行回测程序。程序会按月定时执行调仓逻辑，并在日志中打印调仓时间点。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_85.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_87.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_89.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_91.png)

回测完成后，我们可以查看策略的各项表现指标。关键指标包括：

![](img/acf9206481d0c57bb2ec6cddb22a08d6_93.png)

*   **策略年化收益率**：策略在回测期间的年化收益。
*   **基准年化收益率**：比较基准（如沪深300指数）的年化收益。
*   **夏普比率**：衡量策略风险调整后收益的指标，越高越好。
*   **最大回撤**：策略净值从峰值到谷底的最大跌幅，越小越好。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_95.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_97.png)

通过对比策略收益与基准收益，可以评估策略是否跑赢了市场。同时，分析夏普比率和最大回撤有助于了解策略的稳定性和风险控制能力。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_99.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_101.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_103.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_105.png)

在本策略中，我们基于市值和市净率两个因子进行选股，并加入了数据预处理步骤。虽然最终策略收益在某些时段可能为负，但通过与基准对比，可以观察到策略在部分时期表现出了相对优势。

![](img/acf9206481d0c57bb2ec6cddb22a08d6_107.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_109.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_111.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_113.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_115.png)

---

![](img/acf9206481d0c57bb2ec6cddb22a08d6_117.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_119.png)

![](img/acf9206481d0c57bb2ec6cddb22a08d6_121.png)

本节课中我们一起学习了如何评估量化交易策略的效果。我们通过回测分析了策略的收益率、夏普比率和最大回撤等核心指标，并将其与市场基准进行比较。这个过程不仅帮助我们验证了策略逻辑的有效性，也让我们认识到在实际应用中持续优化和风险控制的重要性。