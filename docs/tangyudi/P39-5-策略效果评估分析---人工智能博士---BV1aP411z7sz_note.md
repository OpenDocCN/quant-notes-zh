# 量化策略开发：P39：5-策略效果评估分析 📊

![](img/93060691f34d9e1de62a0f7bb5eaeadc_1.png)

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其效果进行评估分析。我们将编写一个每月调仓的策略，通过筛选特定因子（如市值、市盈率）来构建股票池，并执行买卖操作。课程将涵盖策略逻辑构建、代码实现、错误调试以及最终的回测结果分析。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_3.png)

---

上一节我们介绍了策略的选股逻辑和预处理步骤。本节中，我们来看看如何实现具体的调仓操作，并运行策略进行回测。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_5.png)

## 调仓逻辑实现

![](img/93060691f34d9e1de62a0f7bb5eaeadc_7.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_9.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_11.png)

首先，我们需要判断当前持有的股票中，哪些已经不在最新的选股池里。如果存在需要卖出的股票，则执行调仓操作。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_13.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_15.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_17.png)

以下是调仓操作的具体步骤：

![](img/93060691f34d9e1de62a0f7bb5eaeadc_19.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_21.png)

1.  **判断是否需要调仓**：比较当前持仓股票列表与最新选股池的差异。如果存在差异（即需要卖出的股票数量大于0），则执行调仓。
    ```python
    if len(delete_list) > 0:
        print(f"执行调仓操作，时间：{context.current_dt}")
    ```

![](img/93060691f34d9e1de62a0f7bb5eaeadc_23.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_25.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_27.png)

2.  **卖出不在池中的股票**：遍历需要删除的股票列表，使用 `order_target_percent` 函数将其持仓比例调整为0，即全部卖出。
    ```python
    for stock in delete_list:
        order_target_percent(stock, 0)
    ```

![](img/93060691f34d9e1de62a0f7bb5eaeadc_29.png)

3.  **买入池中的股票**：遍历最新的选股池，使用 `order_target_percent` 函数平均分配资金买入所有股票。
    ```python
    for stock in stock_list:
        order_target_percent(stock, 1.0 / len(stock_list))
    ```

![](img/93060691f34d9e1de62a0f7bb5eaeadc_31.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_33.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_35.png)

通过以上步骤，我们完成了每月一次的调仓逻辑，确保持仓始终与最新的选股标准保持一致。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_37.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_39.png)

---

![](img/93060691f34d9e1de62a0f7bb5eaeadc_41.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_43.png)

## 代码调试与运行

![](img/93060691f34d9e1de62a0f7bb5eaeadc_45.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_47.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_49.png)

在编写完核心逻辑后，运行代码时可能会遇到各种错误。以下是调试过程中遇到的一些典型问题及其解决方法。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_51.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_53.png)

以下是调试过程中遇到的主要问题：

![](img/93060691f34d9e1de62a0f7bb5eaeadc_55.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_57.png)

1.  **中性化函数参数缺失**：在调用中性化处理函数时，缺少了必要的参数（如因子列名）。需要检查函数定义并传入正确的参数。
    ```python
    # 错误示例：缺少列名参数
    # neutralized_data = neutralize(factor_data)
    # 正确示例
    neutralized_data = neutralize(factor_data, factor_column_name)
    ```

![](img/93060691f34d9e1de62a0f7bb5eaeadc_59.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_61.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_63.png)

2.  **函数名拼写错误**：例如，将 `quantile` 误写为 `qantile`。需要仔细检查代码中的拼写。
    ```python
    # 错误示例
    # selected = factor_data[factor_data[‘factor‘] <= factor_data[‘factor‘].qantile(0.2)]
    # 正确示例
    selected = factor_data[factor_data[‘factor‘] <= factor_data[‘factor‘].quantile(0.2)]
    ```

![](img/93060691f34d9e1de62a0f7bb5eaeadc_65.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_67.png)

3.  **变量未定义或类型错误**：例如，使用了未定义的列表变量 `stock_list`。需要确保所有变量在使用前都已正确定义和赋值。
    ```python
    # 确保 stock_list 在使用前已定义
    stock_list = get_selected_stocks(context)
    for stock in stock_list:
        ...
    ```

![](img/93060691f34d9e1de62a0f7bb5eaeadc_69.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_71.png)

经过一系列调试和修正，代码最终成功通过编译并开始执行回测。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_73.png)

---

## 回测结果分析

代码运行后，我们可以在回测进度条和日志中观察策略的执行情况。策略会按照设定的每月频率进行调仓，并在日志中打印调仓时间点。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_75.png)

以下是回测结果的关键观察点：

![](img/93060691f34d9e1de62a0f7bb5eaeadc_77.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_79.png)

1.  **策略执行日志**：控制台会打印每次调仓的日期，这验证了定时器在正常工作。
2.  **收益曲线对比**：回测界面会展示策略收益曲线与基准收益曲线（如上证指数）的对比。目标是策略收益能跑赢基准。
3.  **关键绩效指标**：
    *   **年化收益率**：策略在一年内获得的平均收益率。
    *   **夏普比率**：衡量每承担一单位风险所获得的超额回报。越高越好。
    *   **最大回撤**：策略净值从高点下降到低点的最大幅度。越小越好。
4.  **交易详情**：可以查看每日的交易记录和持仓变化，用于深入分析策略行为。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_81.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_83.png)

在本例的回测中，策略在所选时间段内取得了相对于基准指数更好的表现（尽管绝对收益可能不高），这初步验证了基于市值和市盈率因子进行选股的有效性。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_85.png)

---

![](img/93060691f34d9e1de62a0f7bb5eaeadc_87.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_89.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_91.png)

## 策略流程总结

![](img/93060691f34d9e1de62a0f7bb5eaeadc_93.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_95.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_97.png)

本节课中我们一起学习并实现了一个完整的因子选股策略流程。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_99.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_101.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_103.png)

以下是策略开发的核心步骤总结：

![](img/93060691f34d9e1de62a0f7bb5eaeadc_105.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_107.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_109.png)

1.  **初始化与定时器设置**：在 `initialize` 函数中设置策略的定时调仓频率（如每月）。
2.  **股票池预处理**：在 `before_trading_start` 或类似函数中，剔除不符合基本条件的股票（如ST股、停牌股）。
3.  **因子计算与处理**：获取所需的因子数据（如市值、市盈率），并进行必要的预处理（如去极值、标准化、中性化）。
4.  **选股逻辑**：根据因子值对股票进行排序和筛选（例如，选择市盈率最低的20%的股票），形成最终的投资组合。
5.  **调仓执行**：在定时触发的函数中，比较当前持仓与目标持仓的差异，执行卖出和买入操作，使持仓与目标组合一致。
6.  **回测与评估**：运行策略回测，通过收益曲线和各项绩效指标（年化收益、夏普比率、最大回撤）来评估策略效果。

![](img/93060691f34d9e1de62a0f7bb5eaeadc_111.png)

![](img/93060691f34d9e1de62a0f7bb5eaeadc_113.png)

通过这个流程，我们构建了一个系统化的量化策略框架。需要注意的是，本例仅为教学演示，实际应用中需要更严谨的因子研究、风险控制和市场环境分析。