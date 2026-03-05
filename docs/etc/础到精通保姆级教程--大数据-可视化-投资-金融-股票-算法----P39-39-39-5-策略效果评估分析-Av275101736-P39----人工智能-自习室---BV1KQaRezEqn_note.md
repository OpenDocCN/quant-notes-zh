# Python金融量化交易：P39：策略效果评估分析 📊

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_1.png)

在本节课中，我们将学习如何评估一个量化交易策略的效果。我们将通过运行一个基于因子选股的策略回测，观察其表现，并分析关键指标，以判断策略的有效性。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_3.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_5.png)

上一节我们介绍了策略的构建与调仓逻辑，本节中我们来看看策略在实际回测中的表现如何。

---

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_7.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_9.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_11.png)

## 策略回测与调试

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_13.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_15.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_17.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_19.png)

首先，我们需要运行编写好的策略代码。在初次运行时，代码中可能存在一些拼写错误或逻辑遗漏，导致程序报错。以下是调试过程中遇到并解决的主要问题：

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_21.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_23.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_25.png)

以下是调试步骤与修正：

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_27.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_29.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_31.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_33.png)

1.  **中性化处理参数缺失**：在中性化函数调用时，缺少了必要的参数。需要将指标名称作为参数正确传入。
    ```python
    # 修正前（可能缺少参数）
    neutralized_data = some_neutralize_function(factor_data)
    # 修正后
    neutralized_data = some_neutralize_function(factor_data, ‘factor_name‘)
    ```

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_35.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_37.png)

2.  **模型未训练**：在使用普通最小二乘法进行中性化时，调用了模型属性，但忘记了先执行 `fit` 训练操作。
    ```python
    # 修正前
    model = sm.OLS(y, X)
    residuals = model.resid
    # 修正后
    model = sm.OLS(y, X).fit()  # 添加 .fit() 进行训练
    residuals = model.resid
    ```

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_39.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_41.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_43.png)

3.  **函数名拼写错误**：在选股函数中，调用了错误的分位数函数名。
    ```python
    # 修正前（错误拼写）
    selected = data[data[‘factor‘] <= data[‘factor‘].quantile(0.3)]  # 假设 quantile 拼写错误
    # 修正后
    selected = data[data[‘factor‘] <= data[‘factor‘].quantile(0.3)]
    ```

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_45.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_47.png)

4.  **变量名引用错误**：在删除股票的循环中，引用了一个未定义的列表变量名。
    ```python
    # 修正前
    for stock in stocks_to_delete:  # stocks_to_delete 可能未定义
        order_target_percent(stock, 0)
    # 修正后（确保使用正确的变量名）
    for stock in delete_list:  # 使用实际定义的列表名
        order_target_percent(stock, 0)
    ```

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_49.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_51.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_53.png)

解决上述错误后，策略代码可以成功编译并开始回测运行。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_55.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_57.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_59.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_61.png)

---

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_63.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_65.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_67.png)

## 回测过程观察

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_69.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_71.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_73.png)

策略设置了每月调仓的定时器。在回测过程中，可以通过日志观察调仓是否按计划执行。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_75.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_77.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_79.png)

以下是回测日志的关键观察点：

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_81.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_83.png)

*   程序会在每个月的第一个交易日打印调仓信息，这表明定时器工作正常。
*   回测界面会显示一个进度条，由于计算涉及全市场数据预处理和每月调仓，速度可能较慢。
*   在回测运行时，可以实时看到策略收益曲线与基准收益曲线的对比。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_85.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_87.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_89.png)

---

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_91.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_93.png)

## 回测结果分析

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_95.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_97.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_99.png)

回测完成后，我们需要关注几个核心的评估指标，以判断策略表现。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_101.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_103.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_105.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_107.png)

以下是主要的策略评估指标：

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_109.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_111.png)

*   **累计收益率**：策略在整个回测期间的总收益。图中显示，策略收益曲线最终高于基准（如沪深300指数）收益曲线，表明策略跑赢了市场。
*   **年化收益率**：将累计收益率折算成年化利率，便于比较。策略的年化收益率为正，而基准年化收益率为负。
*   **夏普比率**：衡量每承受一单位风险，所获得的超额回报。公式为 `(策略年化收益率 - 无风险利率) / 策略年化波动率`。该策略的夏普比率虽然不高，但相对于负收益的基准，仍有其价值。
*   **最大回撤**：策略净值从高点下降到低点的最大幅度。该策略的最大回撤约为9%，在可接受范围内。
*   **阿尔法与贝塔**：
    *   **阿尔法** 代表策略超越基准的超额收益。正阿尔法表明策略具有选股能力。
    *   **贝塔** 代表策略相对于基准的系统性风险。图中显示策略的阿尔法为正，贝塔值适中。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_113.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_115.png)

总体来看，这个简单的双因子（市值、市净率）选股策略在回测期内取得了相对于基准的正向超额收益，风控指标（如最大回撤）也表现尚可，证明了因子选股框架的有效性。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_117.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_119.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_121.png)

---

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_123.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_125.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_127.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_129.png)

## 策略逻辑回顾

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_131.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_133.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_135.png)

本节课中我们一起学习了如何评估一个量化策略。我们的策略遵循了清晰的流程：

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_137.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_139.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_141.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_143.png)

1.  **定时触发**：在 `initialize` 中设置每月调仓的定时器。
2.  **股票池过滤**：在 `before_trading_start` 中，先过滤掉不符合基本条件的股票。
3.  **因子计算与处理**：对剩余股票计算所需因子（如市值、市净率），并进行标准化、中性化等预处理。
4.  **因子筛选**：根据因子值（例如，选择市净率最低的30%股票）构建目标持仓股票池。
5.  **调仓执行**：在 `rebalance` 函数中，卖出当前持有但不在新股票池中的股票，并平均买入新股票池中的所有股票。

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_145.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_147.png)

![](img/a688c9a0ad5dfefa9c3a33728ed6cbbb_149.png)

通过回测，我们验证了这一流程的可行性，并学会了通过收益率曲线、夏普比率、最大回撤等关键指标来综合评价一个策略的绩效。记住，回测是验证想法的重要工具，但实际市场表现可能因流动性、交易成本、模型过拟合等因素而有所不同。