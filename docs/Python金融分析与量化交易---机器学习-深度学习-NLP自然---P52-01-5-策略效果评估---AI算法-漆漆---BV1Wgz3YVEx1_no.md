# 量化交易实战：P52：01-5-策略效果评估分析

在本节课中，我们将学习如何实现一个完整的因子选股策略，并对其进行回测和效果评估。我们将编写代码来执行月度调仓，处理数据，并根据选定的财务指标（如市值和市净率）来调整投资组合。

![](img/cf13566375e760183312d60ac380b00c_1.png)

![](img/cf13566375e760183312d60ac380b00c_3.png)

## 策略逻辑与代码实现

上一节我们介绍了策略的基本框架，本节中我们来看看具体的代码实现。核心逻辑是每月初检查股票池，卖出不符合条件的股票，并平均买入符合条件的股票。

![](img/cf13566375e760183312d60ac380b00c_5.png)

首先，在策略的初始化函数中，我们设置按月调仓的定时器。

![](img/cf13566375e760183312d60ac380b00c_7.png)

![](img/cf13566375e760183312d60ac380b00c_9.png)

![](img/cf13566375e760183312d60ac380b00c_11.png)

```python
def initialize(context):
    # 设置按月调仓，每月第一个交易日执行
    run_monthly(rebalance, date_rule=1)
```

![](img/cf13566375e760183312d60ac380b00c_13.png)

![](img/cf13566375e760183312d60ac380b00c_15.png)

![](img/cf13566375e760183312d60ac380b00c_17.png)

接下来，我们定义核心的调仓函数 `rebalance`。该函数负责执行选股和交易操作。

![](img/cf13566375e760183312d60ac380b00c_19.png)

![](img/cf13566375e760183312d60ac380b00c_21.png)

```python
def rebalance(context):
    # 1. 获取当前时间
    current_date = context.current_dt.date()
    # 2. 执行选股逻辑，获取目标股票池
    target_stocks = select_stocks(current_date)
    # 3. 获取当前持仓的股票列表
    held_stocks = list(context.portfolio.positions.keys())
    # 4. 找出需要卖出的股票（当前持有但不在目标池中）
    stocks_to_sell = [s for s in held_stocks if s not in target_stocks]
```

![](img/cf13566375e760183312d60ac380b00c_23.png)

![](img/cf13566375e760183312d60ac380b00c_25.png)

![](img/cf13566375e760183312d60ac380b00c_27.png)

以下是调仓操作的具体步骤：

![](img/cf13566375e760183312d60ac380b00c_29.png)

![](img/cf13566375e760183312d60ac380b00c_31.png)

首先，判断是否需要执行调仓操作。如果存在需要卖出的股票，则执行卖出。

![](img/cf13566375e760183312d60ac380b00c_33.png)

![](img/cf13566375e760183312d60ac380b00c_35.png)

![](img/cf13566375e760183312d60ac380b00c_37.png)

```python
    if len(stocks_to_sell) > 0:
        print(f‘执行调仓，日期：{current_date}’)
        # 卖出不在目标池中的股票
        for stock in stocks_to_sell:
            order_target_percent(stock, 0)
```

![](img/cf13566375e760183312d60ac380b00c_39.png)

![](img/cf13566375e760183312d60ac380b00c_41.png)

卖出操作完成后，需要买入目标股票池中的股票。我们采用等权重配置的方式。

![](img/cf13566375e760183312d60ac380b00c_43.png)

![](img/cf13566375e760183312d60ac380b00c_45.png)

```python
        # 等权重买入目标池中的所有股票
        if len(target_stocks) > 0:
            weight = 1.0 / len(target_stocks)
            for stock in target_stocks:
                order_target_percent(stock, weight)
```

![](img/cf13566375e760183312d60ac380b00c_47.png)

![](img/cf13566375e760183312d60ac380b00c_49.png)

![](img/cf13566375e760183312d60ac380b00c_51.png)

## 选股函数与数据处理

![](img/cf13566375e760183312d60ac380b00c_53.png)

![](img/cf13566375e760183312d60ac380b00c_55.png)

选股函数 `select_stocks` 是策略的核心。它负责筛选出符合我们设定条件的股票。

![](img/cf13566375e760183312d60ac380b00c_57.png)

![](img/cf13566375e760183312d60ac380b00c_59.png)

![](img/cf13566375e760183312d60ac380b00c_61.png)

以下是选股流程的关键步骤：

![](img/cf13566375e760183312d60ac380b00c_63.png)

![](img/cf13566375e760183312d60ac380b00c_65.png)

![](img/cf13566375e760183312d60ac380b00c_67.png)

1.  **数据获取与初步过滤**：首先获取全市场股票，并过滤掉ST股、停牌股等。
2.  **因子查询**：查询我们关注的因子数据，例如市值和市净率。
3.  **数据预处理**：对因子数据进行标准化、去极值、中性化等处理，以消除量纲和行业、市值等风格的影响。
4.  **综合筛选**：根据处理后的因子值，设定阈值（例如选择市净率最低的30%股票），得到最终的股票池。

![](img/cf13566375e760183312d60ac380b00c_69.png)

![](img/cf13566375e760183312d60ac380b00c_71.png)

```python
def select_stocks(date):
    # 获取全市场股票
    all_stocks = get_all_securities(date).index.tolist()
    # 过滤ST、停牌等股票
    filtered_stocks = filter_stocks(all_stocks, date)
    # 查询因子数据
    factor_data = get_factor_data(filtered_stocks, date, [‘market_cap‘， ‘pb_ratio’])
    # 数据预处理（标准化、中性化等）
    processed_data = preprocess_factors(factor_data)
    # 根据处理后的因子进行筛选（例如，选择市净率最低的30%）
    selected_stocks = select_by_factor(processed_data, ‘pb_ratio’， 0.3)
    return selected_stocks
```

![](img/cf13566375e760183312d60ac380b00c_73.png)

## 代码调试与运行

![](img/cf13566375e760183312d60ac380b00c_75.png)

![](img/cf13566375e760183312d60ac380b00c_77.png)

在编写完整代码后，我们进行了多次调试以修正语法错误和逻辑问题。例如，在数据预处理的中性化步骤中，我们最初遗漏了模型的拟合（`fit`）操作。

![](img/cf13566375e760183312d60ac380b00c_79.png)

![](img/cf13566375e760183312d60ac380b00c_81.png)

```python
# 错误示例：缺少 fit 步骤
model = OLS(y, X) # 仅初始化模型
residuals = model.resid # 直接调用会报错

# 正确示例：先拟合模型
model = OLS(y, X).fit() # 拟合模型
residuals = model.resid # 获取残差作为中性化后的因子
```

![](img/cf13566375e760183312d60ac380b00c_83.png)

经过修正后，策略代码成功编译并运行。回测进度条开始推进，并在每月初打印调仓日志。

![](img/cf13566375e760183312d60ac380b00c_85.png)

## 回测结果分析

![](img/cf13566375e760183312d60ac380b00c_87.png)

![](img/cf13566375e760183312d60ac380b00c_89.png)

策略回测完成后，我们得到了关键的性能指标。通过与基准（例如沪深300指数）对比，可以评估策略的有效性。

![](img/cf13566375e760183312d60ac380b00c_91.png)

![](img/cf13566375e760183312d60ac380b00c_93.png)

![](img/cf13566375e760183312d60ac380b00c_95.png)

*   **年化收益率**：策略在一段时间内的平均收益。
*   **夏普比率**：衡量每承担一单位风险所获得的超额回报。比率越高，风险调整后收益越好。
*   **最大回撤**：策略净值从峰值到谷底的最大跌幅，反映历史最大亏损程度。
*   **阿尔法（Alpha）与贝塔（Beta）**：阿尔法代表超越基准的超额收益，贝塔代表相对于基准的系统性风险。

![](img/cf13566375e760183312d60ac380b00c_97.png)

![](img/cf13566375e760183312d60ac380b00c_99.png)

在我们的示例回测中，策略的年化收益率和夏普比率虽然不高，但在所选回测期内表现优于基准指数（基准收益为负，策略收益为正或负值更小），表明因子选股逻辑起到了一定的作用。最大回撤控制在10%以内，属于可接受范围。

![](img/cf13566375e760183312d60ac380b00c_101.png)

![](img/cf13566375e760183312d60ac380b00c_103.png)

![](img/cf13566375e760183312d60ac380b00c_105.png)

## 总结

![](img/cf13566375e760183312d60ac380b00c_107.png)

![](img/cf13566375e760183312d60ac380b00c_109.png)

![](img/cf13566375e760183312d60ac380b00c_111.png)

![](img/cf13566375e760183312d60ac380b00c_113.png)

本节课中我们一起学习并实现了一个完整的月度调仓因子选股策略。我们掌握了以下核心内容：

![](img/cf13566375e760183312d60ac380b00c_115.png)

![](img/cf13566375e760183312d60ac380b00c_117.png)

![](img/cf13566375e760183312d60ac380b00c_119.png)

1.  **策略框架**：使用定时器 `run_monthly` 触发周期性的调仓函数。
2.  **交易逻辑**：比较当前持仓与目标股票池，卖出差异部分，并以等权重方式买入目标股票。
3.  **选股流程**：实现了从数据获取、过滤、因子计算、数据预处理到最终筛选的完整链条。
4.  **代码调试**：在实践中修正了函数调用、参数传递和数据处理中的常见错误。
5.  **效果评估**：通过回测结果的关键指标（年化收益、夏普比率、最大回撤）与基准对比，评估策略的初步表现。

![](img/cf13566375e760183312d60ac380b00c_121.png)

![](img/cf13566375e760183312d60ac380b00c_123.png)

这个策略示例展示了量化交易从想法到代码实现，再到回测验证的基本流程。需要注意的是，回测结果受多种因素影响，实际应用前需进行更严格的检验和优化。