# Python金融量化实战：P38：因子分析实战 - 策略效果评估分析 📊

在本节课中，我们将学习如何对一个基于因子的选股策略进行效果评估分析。我们将通过编写代码实现一个完整的策略回测流程，包括数据预处理、因子筛选、调仓逻辑以及最终的结果分析。

![](img/2727c96930b28000c6f1efe30a1e55a1_1.png)

---

![](img/2727c96930b28000c6f1efe30a1e55a1_3.png)

## 策略逻辑与代码实现

上一节我们介绍了因子选股的基本概念，本节中我们来看看如何将理论转化为实际的代码，并评估其效果。

### 1. 初始化与定时器设置

![](img/2727c96930b28000c6f1efe30a1e55a1_5.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_7.png)

首先，在策略的构造函数中，我们需要设置一个定时器，用于定期执行调仓操作。这里我们选择按月进行调仓。

![](img/2727c96930b28000c6f1efe30a1e55a1_9.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_11.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_13.png)

```python
def initialize(context):
    # 设置按月调仓的定时器
    run_monthly(rebalance, monthday=1)
```

![](img/2727c96930b28000c6f1efe30a1e55a1_15.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_17.png)

### 2. 数据预处理与因子筛选

![](img/2727c96930b28000c6f1efe30a1e55a1_19.png)

在每次调仓时，我们首先需要获取股票池，并进行数据预处理。以下是核心步骤：

![](img/2727c96930b28000c6f1efe30a1e55a1_21.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_23.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_25.png)

**第一步：过滤股票**
我们首先过滤掉不符合基本条件的股票，例如ST股、停牌股等。

![](img/2727c96930b28000c6f1efe30a1e55a1_27.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_29.png)

**第二步：查询因子数据**
我们选取了两个因子进行示例：市值和市净率。通过API获取这些数据。

![](img/2727c96930b28000c6f1efe30a1e55a1_31.png)

```python
# 示例：获取市值和市净率数据
factor_data = get_factors(security_list, factors=[‘market_cap‘, ‘pb_ratio‘])
```

![](img/2727c96930b28000c6f1efe30a1e55a1_33.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_35.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_37.png)

**第三步：数据预处理（中性化）**
为了消除行业、市值等因素的影响，我们需要对因子进行中性化处理。这通常通过线性回归完成。

![](img/2727c96930b28000c6f1efe30a1e55a1_39.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_41.png)

```python
# 中性化处理示例代码
def neutralize_factor(factor_series, control_variables):
    # control_variables 是控制变量，如行业哑变量
    model = sm.OLS(factor_series, control_variables)
    results = model.fit() # 之前遗漏了fit操作
    neutralized_factor = results.resid # 获取残差作为中性化后的因子
    return neutralized_factor
```

**第四步：基于因子选股**
我们设定一个筛选逻辑。例如，认为市净率因子越低越好，因此选择市净率小于等于某个分位数的股票。

![](img/2727c96930b28000c6f1efe30a1e55a1_43.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_45.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_47.png)

```python
# 选择市净率因子值在最低30%的股票
threshold = factor_data[‘pb_ratio‘].quantile(0.3)
selected_stocks = factor_data[factor_data[‘pb_ratio‘] <= threshold].index.tolist()
```

![](img/2727c96930b28000c6f1efe30a1e55a1_49.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_51.png)

### 3. 调仓逻辑实现

![](img/2727c96930b28000c6f1efe30a1e55a1_53.png)

以下是调仓操作的具体实现步骤。核心是卖出不在新股票池中的持仓，并买入新股票池中的股票。

![](img/2727c96930b28000c6f1efe30a1e55a1_55.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_57.png)

**判断是否需要调仓**
比较当前持仓股票与新选出的股票池，找出需要卖出的股票。

![](img/2727c96930b28000c6f1efe30a1e55a1_59.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_61.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_63.png)

```python
# 找出当前持仓中不在新股票池的股票
current_holdings = list(context.portfolio.positions.keys())
stocks_to_sell = list(set(current_holdings) - set(selected_stocks))
```

![](img/2727c96930b28000c6f1efe30a1e55a1_65.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_67.png)

**执行卖出操作**
如果存在需要卖出的股票，则执行卖出，使其持仓比例变为0。

![](img/2727c96930b28000c6f1efe30a1e55a1_69.png)

```python
if len(stocks_to_sell) > 0:
    print(f‘执行调仓，卖出股票: {stocks_to_sell}‘)
    for stock in stocks_to_sell:
        # order_target_percent 函数用于调整持仓至目标比例
        order_target_percent(stock, 0)
```

![](img/2727c96930b28000c6f1efe30a1e55a1_71.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_73.png)

**执行买入操作**
对于新股票池中的股票，进行平均买入。

![](img/2727c96930b28000c6f1efe30a1e55a1_75.png)

```python
# 平均买入新股票池中的所有股票
position_per_stock = 1.0 / len(selected_stocks)
for stock in selected_stocks:
    order_target_percent(stock, position_per_stock)
```

![](img/2727c96930b28000c6f1efe30a1e55a1_77.png)

### 4. 运行回测与错误调试

![](img/2727c96930b28000c6f1efe30a1e55a1_79.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_81.png)

代码编写完成后，首次运行通常会遇到一些错误，需要逐步调试解决。

**常见错误与修正：**
1.  **函数参数缺失**：例如中性化函数缺少必要的控制变量参数。需要检查并传入正确的参数名。
2.  **拼写错误**：变量或函数名拼写错误，如 `quantile` 拼成 `qant`。需要仔细核对。
3.  **方法调用遗漏**：例如进行线性回归时，只创建了模型对象，未调用 `fit()` 方法进行训练。
4.  **对象属性错误**：尝试访问不存在的列表或Series属性，如 `stock_list` 误写为 `stock list`。

![](img/2727c96930b28000c6f1efe30a1e55a1_83.png)

通过逐一修正这些错误，代码最终能够成功编译并运行回测。

---

![](img/2727c96930b28000c6f1efe30a1e55a1_85.png)

## 回测结果分析

![](img/2727c96930b28000c6f1efe30a1e55a1_87.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_89.png)

程序运行后，我们可以观察回测结果，评估策略性能。

![](img/2727c96930b28000c6f1efe30a1e55a1_91.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_93.png)

**观察输出日志**
我们在调仓时设置了打印语句，可以观察调仓是否按计划（每月初）执行。

![](img/2727c96930b28000c6f1efe30a1e55a1_95.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_97.png)

**分析业绩指标**
回测结束后，重点关注以下指标：
*   **累计收益率**：策略相对于初始本金的收益。
*   **年化收益率**：折算成年度的收益率。
*   **基准收益率**：同期市场基准（如沪深300指数）的收益，用于对比。
*   **夏普比率**：衡量风险调整后的收益，越高越好。
*   **最大回撤**：策略运行期间最大的亏损幅度，越小越好。

![](img/2727c96930b28000c6f1efe30a1e55a1_99.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_101.png)

**结果解读**
在示例回测中，策略的年化收益率可能仅为小幅正值或负值，但关键在于其表现是否优于基准。例如，当基准收益率为负时，策略若能减少亏损或实现微利，则说明因子选股逻辑起到了一定的作用。夏普比率和最大回撤则帮助我们从风险和稳定性角度评估策略。

![](img/2727c96930b28000c6f1efe30a1e55a1_103.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_105.png)

---

![](img/2727c96930b28000c6f1efe30a1e55a1_107.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_109.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_111.png)

## 核心流程总结

![](img/2727c96930b28000c6f1efe30a1e55a1_113.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_115.png)

本节课中我们一起学习了基于因子的量化策略从构建到评估的全流程：

![](img/2727c96930b28000c6f1efe30a1e55a1_117.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_119.png)

1.  **策略初始化**：设定调仓频率（如每月）。
2.  **数据准备**：过滤股票、查询因子数据、进行数据预处理（如中性化）。
3.  **选股逻辑**：根据处理后的因子值（如选择低市净率股票）构建股票池。
4.  **调仓执行**：卖出不在新股票池的持仓，并平均买入新股票池中的股票。
5.  **回测与评估**：运行回测，通过收益率、夏普比率、最大回撤等指标评估策略效果。

![](img/2727c96930b28000c6f1efe30a1e55a1_121.png)

![](img/2727c96930b28000c6f1efe30a1e55a1_123.png)

这个流程展示了量化策略开发的基本骨架。需要注意的是，本例仅为教学演示，实际策略需要考虑更多因子、更复杂的预处理和更严谨的风控逻辑。大家可以根据这个框架，尝试加入更多因子或优化选股逻辑，进行更深入的探索。