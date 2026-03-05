# Python金融分析与量化交易实战：P35：5-策略效果评估分析 📊

在本节课中，我们将学习如何实现一个基于因子的选股策略，并完成策略的调仓操作。我们将编写代码来筛选股票、处理数据，并定期调整投资组合。

![](img/fc799c2c5959604ccf59634bef2c45f6_1.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_3.png)

---

## 策略实现与调仓逻辑

![](img/fc799c2c5959604ccf59634bef2c45f6_5.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_7.png)

上一节我们介绍了因子选股的基本概念，本节中我们来看看如何将其转化为具体的代码逻辑并执行调仓。

![](img/fc799c2c5959604ccf59634bef2c45f6_9.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_11.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_13.png)

首先，我们需要判断是否需要进行调仓操作。如果筛选后需要移除的股票数量大于零，则执行调仓。

![](img/fc799c2c5959604ccf59634bef2c45f6_15.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_17.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_19.png)

以下是调仓操作的具体步骤：

![](img/fc799c2c5959604ccf59634bef2c45f6_21.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_23.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_25.png)

1.  **卖出不符合条件的股票**：遍历需要移除的股票列表，将每只股票的持仓量设置为零，即全部卖出。
    ```python
    for stock in stocks_to_delete:
        order_target_percent(stock, 0)
    ```

![](img/fc799c2c5959604ccf59634bef2c45f6_27.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_29.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_31.png)

2.  **买入符合条件的股票**：遍历当前股票池中的每一只股票，进行买入操作。为了简化，我们采用平均分配资金的方式买入。
    ```python
    for stock in current_stock_pool:
        order_target_percent(stock, 1.0 / len(current_stock_pool))
    ```

![](img/fc799c2c5959604ccf59634bef2c45f6_33.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_35.png)

这样，我们就完成了一次完整的调仓操作。该操作会定期（例如每月）执行，以检查并更新投资组合。

![](img/fc799c2c5959604ccf59634bef2c45f6_37.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_39.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_41.png)

---

![](img/fc799c2c5959604ccf59634bef2c45f6_43.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_45.png)

## 代码调试与问题修复

![](img/fc799c2c5959604ccf59634bef2c45f6_47.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_49.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_51.png)

在编写和运行策略代码时，常常会遇到各种错误。我们需要逐步调试并修复这些问题。

![](img/fc799c2c5959604ccf59634bef2c45f6_53.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_55.png)

以下是调试过程中发现并修复的几个关键问题：

![](img/fc799c2c5959604ccf59634bef2c45f6_57.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_59.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_61.png)

1.  **中性化函数参数缺失**：在调用中性化函数时，缺少了必要的参数。需要将指标名称作为参数传入。
    ```python
    # 修复前（可能缺少参数）
    neutralized_data = neutralize(factor_data)
    # 修复后
    neutralized_data = neutralize(factor_data, ‘factor_name‘)
    ```

![](img/fc799c2c5959604ccf59634bef2c45f6_63.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_65.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_67.png)

2.  **缺少模型训练步骤**：在使用线性回归进行中性化前，必须先调用 `fit` 方法训练模型。
    ```python
    # 修复前
    model = sm.OLS(y, X)
    # 修复后
    model = sm.OLS(y, X).fit()
    ```

![](img/fc799c2c5959604ccf59634bef2c45f6_69.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_71.png)

3.  **变量名拼写错误**：代码中存在单词拼写错误，例如将 `quantile` 误写为 `qantile`。
    ```python
    # 修复前
    selected = data[data[‘factor‘] <= data[‘factor‘].qantile(0.3)]
    # 修复后
    selected = data[data[‘factor‘] <= data[‘factor‘].quantile(0.3)]
    ```

![](img/fc799c2c5959604ccf59634bef2c45f6_73.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_75.png)

4.  **列表变量引用错误**：在删除股票的操作中，引用了一个未定义的列表变量名。
    ```python
    # 修复前
    for stock in delete_list:  # delete_list 可能未定义
    # 修复后
    for stock in stocks_to_delete:  # 使用正确的变量名
    ```

![](img/fc799c2c5959604ccf59634bef2c45f6_77.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_79.png)

逐一修复这些问题后，代码得以成功编译并运行。

![](img/fc799c2c5959604ccf59634bef2c45f6_81.png)

---

![](img/fc799c2c5959604ccf59634bef2c45f6_83.png)

## 策略回测与结果分析

![](img/fc799c2c5959604ccf59634bef2c45f6_85.png)

代码运行后，我们可以在回测日志中观察调仓操作的执行点（例如每月初）。回测完成后，可以分析策略的整体表现。

![](img/fc799c2c5959604ccf59634bef2c45f6_87.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_89.png)

策略的核心流程可以总结为以下几步：

![](img/fc799c2c5959604ccf59634bef2c45f6_91.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_93.png)

1.  **设定定时器**：在策略的 `initialize` 函数中，设定调仓的频率（如每月）。
2.  **初步过滤股票**：过滤掉不符合基本条件的股票（如ST股、停牌股）。
3.  **计算因子指标**：对剩余股票计算选股因子（如市值、市净率）。
4.  **数据预处理**：对因子数据进行去极值、标准化、中性化等处理。
5.  **筛选股票**：根据处理后的因子值筛选股票（例如，选择市净率最低的30%的股票）。
6.  **执行调仓**：
    *   卖出当前持有但不在新股票池中的股票。
    *   平均买入新股票池中的所有股票。

![](img/fc799c2c5959604ccf59634bef2c45f6_95.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_97.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_99.png)

在本次回测示例中，策略的年化收益率和夏普比率虽然不高，但相比基准指数（如沪深300）有相对更好的表现，体现了因子选股策略在一定条件下的有效性。

![](img/fc799c2c5959604ccf59634bef2c45f6_101.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_103.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_105.png)

---

![](img/fc799c2c5959604ccf59634bef2c45f6_107.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_109.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_111.png)

## 总结

![](img/fc799c2c5959604ccf59634bef2c45f6_113.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_115.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_117.png)

本节课中我们一起学习了如何实现一个完整的因子选股策略。我们从判断调仓条件开始，编写了卖出和买入的逻辑，并经历了调试代码、修复错误的完整过程。最后，我们回顾了策略从数据准备、因子计算到定期调仓的核心步骤，并分析了回测结果。

![](img/fc799c2c5959604ccf59634bef2c45f6_119.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_121.png)

![](img/fc799c2c5959604ccf59634bef2c45f6_123.png)

需要注意的是，本课程内容仅用于量化策略的学习与回测模拟，不构成任何实际投资建议。市场的实际运行远比回测复杂。