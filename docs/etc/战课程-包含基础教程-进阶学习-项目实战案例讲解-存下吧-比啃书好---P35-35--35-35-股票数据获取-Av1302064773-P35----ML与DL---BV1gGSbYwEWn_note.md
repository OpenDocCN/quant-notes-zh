# 量化交易教程：P35：因子选股策略实现

![](img/af1cdffc557f831a70908a9ae278874e_0.png)

## 📋 概述
在本节课中，我们将学习如何基于预处理后的因子数据，构建并实现一个完整的因子选股策略。我们将从数据获取开始，逐步完成策略的初始化、股票池筛选、因子计算以及定期的调仓操作。

---

![](img/af1cdffc557f831a70908a9ae278874e_2.png)

## 🛠️ 第一步：导入必要的工具包
首先，我们需要导入实现策略所需的Python工具包。以下是核心的库：

![](img/af1cdffc557f831a70908a9ae278874e_4.png)

```python
import numpy as np
import pandas as pd
from statsmodels.api import OLS
```

*   **`numpy`** 用于高效的数值计算。
*   **`pandas`** 用于数据处理和分析。
*   **`statsmodels`** 中的 **`OLS`** 模块用于构建回归模型，这在后续的因子中性化处理中至关重要。

---

![](img/af1cdffc557f831a70908a9ae278874e_6.png)

![](img/af1cdffc557f831a70908a9ae278874e_8.png)

## ⚙️ 第二步：策略初始化与定时器设置
上一节我们介绍了所需的工具包，本节中我们来看看如何初始化策略并设置核心的运行周期。

![](img/af1cdffc557f831a70908a9ae278874e_10.png)

在策略的初始化函数中，我们需要定义策略的运行逻辑和时间框架。通常，因子选股策略不会每天调仓，而是以月或周为周期进行再平衡。

![](img/af1cdffc557f831a70908a9ae278874e_12.png)

以下是初始化模块的关键步骤：

![](img/af1cdffc557f831a70908a9ae278874e_14.png)

![](img/af1cdffc557f831a70908a9ae278874e_16.png)

```python
def initialize(context):
    # 设置按月执行的定时再平衡函数
    run_monthly(rebalance, monthday=1, time='open')
```

![](img/af1cdffc557f831a70908a9ae278874e_18.png)

*   **`run_monthly`** 函数指定策略每月执行一次 `rebalance` 函数。
*   **`monthday=1`** 参数表示在每个月的第一个交易日执行。
*   **`time='open'`** 表示在交易日开盘时执行调仓操作。

![](img/af1cdffc557f831a70908a9ae278874e_20.png)

![](img/af1cdffc557f831a70908a9ae278874e_22.png)

---

![](img/af1cdffc557f831a70908a9ae278874e_24.png)

## 📊 第三步：获取与筛选股票池
初始化完成后，我们需要一个基础的股票池。在 `rebalance` 函数中，第一步就是获取所有可交易的股票。

以下是获取和初步筛选股票池的步骤：

![](img/af1cdffc557f831a70908a9ae278874e_26.png)

![](img/af1cdffc557f831a70908a9ae278874e_28.png)

1.  **获取全市场股票**：使用API获取所有A股股票的基本信息。
2.  **股票池筛选**：并非所有股票都适合纳入策略，我们需要根据一些基本条件（如上市时间、是否ST股等）进行过滤。

![](img/af1cdffc557f831a70908a9ae278874e_30.png)

![](img/af1cdffc557f831a70908a9ae278874e_32.png)

```python
def rebalance(context):
    # 1. 获取所有股票代码
    all_stocks = get_all_securities(types=['stock'], date=None).index.tolist()
    
    # 2. 对股票进行初步筛选（示例：过滤ST股和上市不足60天的股票）
    current_date = context.current_dt
    filtered_stocks = []
    for stock in all_stocks:
        if not is_st_stock(stock, current_date) and days_since_listed(stock, current_date) > 60:
            filtered_stocks.append(stock)
```

![](img/af1cdffc557f831a70908a9ae278874e_34.png)

---

![](img/af1cdffc557f831a70908a9ae278874e_36.png)

## 🧮 第四步：计算因子与选股
有了基础的股票池后，核心步骤是基于因子进行选股。这通常包括计算原始因子值，并进行标准化、行业中性化等处理。

![](img/af1cdffc557f831a70908a9ae278874e_38.png)

以下是基于因子选股的核心流程：

![](img/af1cdffc557f831a70908a9ae278874e_40.png)

1.  **计算原始因子**：例如，计算每只股票的市值、市盈率、动量等因子。
2.  **因子预处理**：对原始因子进行去极值、标准化和行业中性化处理，以消除量纲和行业偏见的影响。
3.  **综合评分与排序**：将处理后的多个因子按一定权重合成一个综合得分，并据此对股票进行排序。
4.  **选择目标股票**：选取排名靠前的股票构成最终的投资组合。

```python
    # 3. 计算因子值（以市值和市盈率为例）
    factor_data = pd.DataFrame()
    for stock in filtered_stocks:
        market_cap = get_market_cap(stock, current_date) # 获取市值
        pe_ratio = get_pe_ratio(stock, current_date)     # 获取市盈率
        # ... 可以计算更多因子
        factor_data.loc[stock, 'market_cap'] = market_cap
        factor_data.loc[stock, 'pe_ratio'] = pe_ratio
    
    # 4. 因子预处理：去极值、标准化
    processed_factors = (factor_data - factor_data.mean()) / factor_data.std()
    
    # 5. 行业中性化（以申万一级行业为例）
    # 此处需要获取股票的行业信息，并利用OLS回归消除行业影响
    # industry_dummies = get_industry_dummies(filtered_stocks)
    # model = OLS(processed_factors['target_factor'], industry_dummies).fit()
    # neutralized_factor = model.resid
    
    # 6. 合成因子得分并选股（假设我们偏好小市值、低市盈率）
    processed_factors['score'] = - processed_factors['market_cap'] - processed_factors['pe_ratio']
    selected_stocks = processed_factors.nlargest(20, 'score').index.tolist() # 选择得分最高的20只
```

![](img/af1cdffc557f831a70908a9ae278874e_42.png)

---

![](img/af1cdffc557f831a70908a9ae278874e_44.png)

![](img/af1cdffc557f831a70908a9ae278874e_46.png)

## 🔄 第五步：执行调仓交易
最后一步是根据选股结果调整持仓，使实际持仓与目标组合一致。

![](img/af1cdffc557f831a70908a9ae278874e_48.png)

以下是调仓交易的具体操作：

![](img/af1cdffc557f831a70908a9ae278874e_50.png)

1.  **卖出不在目标组合中的持仓**。
2.  **买入新入选的目标股票**。
3.  **调整资金分配**，通常采用等权重或按因子得分加权的方式。

![](img/af1cdffc557f831a70908a9ae278874e_52.png)

![](img/af1cdffc557f831a70908a9ae278874e_54.png)

```python
    # 7. 获取当前持仓
    current_positions = context.portfolio.positions
    
    # 8. 卖出已持有但不在新选股列表中的股票
    for stock in current_positions:
        if stock not in selected_stocks:
            order_target_value(stock, 0) # 平仓该股票
    
    # 9. 买入新选中的股票（等权重分配）
    position_value_per_stock = context.portfolio.total_value / len(selected_stocks)
    for stock in selected_stocks:
        order_target_value(stock, position_value_per_stock)
```

![](img/af1cdffc557f831a70908a9ae278874e_56.png)

---

![](img/af1cdffc557f831a70908a9ae278874e_58.png)

## 📝 总结
本节课中我们一起学习了如何从零开始实现一个因子选股策略。我们首先导入了必要的工具包，然后在策略初始化中设置了按月调仓的定时器。在每次调仓时，我们获取全市场股票并进行初步筛选，接着计算并预处理关键因子，通过综合评分选出最具潜力的股票，最后执行买卖指令完成持仓的再平衡。这个过程涵盖了量化策略从数据到执行的核心闭环，是构建更复杂策略的重要基础。