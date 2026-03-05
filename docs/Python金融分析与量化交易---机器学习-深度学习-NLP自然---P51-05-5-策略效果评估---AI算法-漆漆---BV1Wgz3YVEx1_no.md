# 人工智能金融量化实战：P51：05-5-策略效果评估分析 📊

![](img/0fc205ea8188990e75a9151dfafa532c_1.png)

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其进行回测和效果评估。我们将编写代码，按月调仓，根据市值和市净率因子筛选股票，并观察策略的最终表现。

![](img/0fc205ea8188990e75a9151dfafa532c_3.png)

---

![](img/0fc205ea8188990e75a9151dfafa532c_5.png)

## 策略逻辑与代码实现

![](img/0fc205ea8188990e75a9151dfafa532c_7.png)

![](img/0fc205ea8188990e75a9151dfafa532c_9.png)

![](img/0fc205ea8188990e75a9151dfafa532c_11.png)

上一节我们介绍了因子选股的基本概念。本节中，我们来看看如何将理论转化为具体的代码逻辑。

![](img/0fc205ea8188990e75a9151dfafa532c_13.png)

![](img/0fc205ea8188990e75a9151dfafa532c_15.png)

![](img/0fc205ea8188990e75a9151dfafa532c_17.png)

我们的策略核心流程如下：
1.  **初始化**：设置定时器，决定调仓频率（例如每月）。
2.  **股票池筛选**：从全市场股票中，过滤掉不符合基本条件的股票（如ST股、停牌股）。
3.  **因子计算与处理**：对筛选后的股票计算目标因子（如市值、市净率），并进行必要的预处理（如中性化处理）。
4.  **选股**：根据处理后的因子值，选择符合策略逻辑的股票（例如，选择市净率最低的30%的股票）。
5.  **调仓执行**：比较当前持仓与新选出的股票池，卖出不在新池中的股票，并平均买入新池中的所有股票。

![](img/0fc205ea8188990e75a9151dfafa532c_19.png)

![](img/0fc205ea8188990e75a9151dfafa532c_21.png)

![](img/0fc205ea8188990e75a9151dfafa532c_23.png)

以下是实现这一流程的核心代码框架：

![](img/0fc205ea8188990e75a9151dfafa532c_25.png)

![](img/0fc205ea8188990e75a9151dfafa532c_27.png)

![](img/0fc205ea8188990e75a9151dfafa532c_29.png)

![](img/0fc205ea8188990e75a9151dfafa532c_31.png)

```python
def rebalance(context):
    # 1. 获取当前时间
    current_date = context.current_dt

    # 2. 初步过滤股票池
    # 例如，过滤掉ST股、停牌股、上市不足N天的股票等
    stock_list = filter_stocks(current_date)

    # 3. 获取因子数据并进行预处理
    # 获取市值和市净率数据
    market_cap = get_factor(stock_list, ‘market_cap‘, current_date)
    pb_ratio = get_factor(stock_list, ‘pb_ratio‘, current_date)

    # 对因子进行中性化处理（例如，消除行业影响）
    pb_ratio_neutral = neutralize(pb_ratio, market_cap)

    # 4. 根据处理后的因子进行选股
    # 例如，选择市净率（中性化后）最低的30%的股票
    selected_stocks = select_stocks_by_factor(pb_ratio_neutral, percent=0.3)

    # 5. 执行调仓操作
    # 获取当前持仓
    current_positions = context.portfolio.positions

    # 找出需要卖出的股票（当前持有但不在新选股池中）
    stocks_to_sell = [s for s in current_positions if s not in selected_stocks]
    if len(stocks_to_sell) > 0:
        print(f‘执行调仓，卖出股票: {stocks_to_sell}‘)
        for stock in stocks_to_sell:
            # 将持仓调整为0，即全数卖出
            order_target_percent(stock, 0)

    # 平均买入新选股池中的所有股票
    if len(selected_stocks) > 0:
        weight = 1.0 / len(selected_stocks)  # 等权重配置
        for stock in selected_stocks:
            order_target_percent(stock, weight)
```

![](img/0fc205ea8188990e75a9151dfafa532c_33.png)

![](img/0fc205ea8188990e75a9151dfafa532c_35.png)

---

![](img/0fc205ea8188990e75a9151dfafa532c_37.png)

![](img/0fc205ea8188990e75a9151dfafa532c_39.png)

![](img/0fc205ea8188990e75a9151dfafa532c_41.png)

## 代码调试与运行

![](img/0fc205ea8188990e75a9151dfafa532c_43.png)

![](img/0fc205ea8188990e75a9151dfafa532c_45.png)

编写完策略逻辑后，下一步是运行和调试代码。初学者常会遇到各种语法错误或逻辑问题。

![](img/0fc205ea8188990e75a9151dfafa532c_47.png)

![](img/0fc205ea8188990e75a9151dfafa532c_49.png)

![](img/0fc205ea8188990e75a9151dfafa532c_51.png)

以下是调试过程中可能遇到并解决的几个典型问题：

![](img/0fc205ea8188990e75a9151dfafa532c_53.png)

![](img/0fc205ea8188990e75a9151dfafa532c_55.png)

![](img/0fc205ea8188990e75a9151dfafa532c_57.png)

*   **问题1：函数调用参数缺失**
    *   **错误信息**：提示某个函数缺少必要的参数。
    *   **解决方案**：仔细检查函数定义，确保传入所有必需的参数。例如，中性化函数可能需要传入因子名称。
*   **问题2：未训练模型就进行预测**
    *   **错误信息**：在使用线性回归等模型进行中性化处理时，直接调用`predict`方法报错。
    *   **解决方案**：在调用`predict`之前，必须先调用`fit`方法对模型进行训练。
*   **问题3：变量名拼写错误**
    *   **错误信息**：提示“AttributeError”或“NameError”，指出某个属性或变量不存在。
    *   **解决方案**：逐行检查代码，核对变量名和函数名的拼写。例如，`quantile`误写为`qantile`。
*   **问题4：对象类型错误**
    *   **错误信息**：提示对`NoneType`或错误的集合类型进行操作。
    *   **解决方案**：确保变量在操作前已被正确赋值和初始化。例如，确保选股函数返回的是列表（List）而非其他类型。

![](img/0fc205ea8188990e75a9151dfafa532c_59.png)

![](img/0fc205ea8188990e75a9151dfafa532c_61.png)

![](img/0fc205ea8188990e75a9151dfafa532c_63.png)

![](img/0fc205ea8188990e75a9151dfafa532c_65.png)

通过逐步解决这些错误，代码最终能够成功编译并开始回测运行。

![](img/0fc205ea8188990e75a9151dfafa532c_67.png)

![](img/0fc205ea8188990e75a9151dfafa532c_69.png)

---

![](img/0fc205ea8188990e75a9151dfafa532c_71.png)

![](img/0fc205ea8188990e75a9151dfafa532c_73.png)

## 回测结果分析

![](img/0fc205ea8188990e75a9151dfafa532c_75.png)

![](img/0fc205ea8188990e75a9151dfafa532c_77.png)

![](img/0fc205ea8188990e75a9151dfafa532c_79.png)

代码成功运行后，我们将进入回测阶段，并分析策略的表现。

![](img/0fc205ea8188990e75a9151dfafa532c_81.png)

程序会按月打印调仓日志，并最终输出一系列绩效指标。以下是我们需要关注的核心评估指标：

![](img/0fc205ea8188990e75a9151dfafa532c_83.png)

*   **累计收益率**：策略在整个回测期间的总收益。
*   **年化收益率**：将累计收益率折算为每年的平均收益。
*   **基准收益率**：同期市场指数（如沪深300）的收益，用于对比。
*   **夏普比率**：衡量每承受一单位风险，能获得多少超额回报。**公式**为 `(策略年化收益率 - 无风险利率) / 策略年化波动率`。该值越高，说明风险调整后收益越好。
*   **最大回撤**：策略净值从最高点到最低点的最大跌幅，反映策略可能面临的最大亏损风险。

![](img/0fc205ea8188990e75a9151dfafa532c_85.png)

**分析示例**：
在本次回测中，我们可能观察到：
*   策略的年化收益率虽然不高，但为**正数**。
*   同期基准指数的年化收益率为**负数**。
*   这表明，我们的简单因子策略在测试期内**跑赢了市场基准**，起到了一定的防御或选股作用。
*   夏普比率较低，说明策略的超额收益并不显著，或波动较大。
*   最大回撤控制在10%以内，属于可接受范围。

![](img/0fc205ea8188990e75a9151dfafa532c_87.png)

![](img/0fc205ea8188990e75a9151dfafa532c_89.png)

**重要提示**：回测结果基于历史数据，不代表未来表现。它主要用于验证策略逻辑的合理性，不能直接用于实盘交易。

![](img/0fc205ea8188990e75a9151dfafa532c_91.png)

![](img/0fc205ea8188990e75a9151dfafa532c_93.png)

![](img/0fc205ea8188990e75a9151dfafa532c_95.png)

---

![](img/0fc205ea8188990e75a9151dfafa532c_97.png)

![](img/0fc205ea8188990e75a9151dfafa532c_99.png)

![](img/0fc205ea8188990e75a9151dfafa532c_101.png)

![](img/0fc205ea8188990e75a9151dfafa532c_103.png)

## 策略总结与回顾

![](img/0fc205ea8188990e75a9151dfafa532c_105.png)

![](img/0fc205ea8188990e75a9151dfafa532c_107.png)

![](img/0fc205ea8188990e75a9151dfafa532c_109.png)

![](img/0fc205ea8188990e75a9151dfafa532c_111.png)

本节课中我们一起学习了如何构建并评估一个完整的因子选股策略。

![](img/0fc205ea8188990e75a9151dfafa532c_113.png)

![](img/0fc205ea8188990e75a9151dfafa532c_115.png)

![](img/0fc205ea8188990e75a9151dfafa532c_117.png)

我们首先明确了策略的**三步走流程**：数据预处理、因子筛选、定期调仓。接着，我们将其转化为具体的**Python代码**，实现了按月调仓、基于市值和市净率选股的逻辑。在代码编写后，我们经历了**调试过程**，解决了参数缺失、模型未训练等常见问题。最后，策略成功运行并输出了**回测结果**。通过分析累计收益、夏普比率、最大回撤等关键指标，我们评估了策略在历史数据上的表现，发现其虽然简单，但在测试期内能够取得相对于基准指数的更好表现。

![](img/0fc205ea8188990e75a9151dfafa532c_119.png)

![](img/0fc205ea8188990e75a9151dfafa532c_121.png)

![](img/0fc205ea8188990e75a9151dfafa532c_123.png)

这个实战案例清晰地展示了从策略构思到代码实现，再到效果评估的完整量化研究闭环。你可以在此基础上，尝试引入更多因子、优化选股条件或调整调仓频率，以进一步探索和改进策略。