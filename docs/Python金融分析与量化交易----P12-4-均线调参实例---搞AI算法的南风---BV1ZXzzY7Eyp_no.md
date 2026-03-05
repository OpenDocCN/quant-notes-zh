# Python金融分析与量化交易实战教程：P12：4-均线调参实例 📈

![](img/12ab71ba21e23f56252c00416da2e718_0.png)

![](img/12ab71ba21e23f56252c00416da2e718_2.png)

![](img/12ab71ba21e23f56252c00416da2e718_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

---

![](img/12ab71ba21e23f56252c00416da2e718_6.png)

上一节我们介绍了双均线策略的基本原理和回测方法。本节中，我们来看看如何通过调整策略参数来优化其表现。

![](img/12ab71ba21e23f56252c00416da2e718_8.png)

## 参数调优的必要性

![](img/12ab71ba21e23f56252c00416da2e718_10.png)

![](img/12ab71ba21e23f56252c00416da2e718_12.png)

![](img/12ab71ba21e23f56252c00416da2e718_14.png)

![](img/12ab71ba21e23f56252c00416da2e718_16.png)

策略的最终表现与许多因素有关，其中最关键的因素之一就是均线的参数设置。例如，短期均线（SMA1）是5天合适还是10天合适？长期均线（SMA2）是20天合适还是40天合适？为了找到更优的参数组合，我们可以通过遍历计算来评估不同组合的表现。

![](img/12ab71ba21e23f56252c00416da2e718_18.png)

![](img/12ab71ba21e23f56252c00416da2e718_20.png)

![](img/12ab71ba21e23f56252c00416da2e718_22.png)

## 使用 `itertools.product` 进行参数遍历

![](img/12ab71ba21e23f56252c00416da2e718_24.png)

![](img/12ab71ba21e23f56252c00416da2e718_26.png)

![](img/12ab71ba21e23f56252c00416da2e718_28.png)

在Python中，我们可以借助 `itertools` 模块中的 `product` 函数轻松实现参数组合的遍历。这个函数可以帮助我们生成两个或多个参数空间的所有可能组合。

![](img/12ab71ba21e23f56252c00416da2e718_30.png)

![](img/12ab71ba21e23f56252c00416da2e718_32.png)

![](img/12ab71ba21e23f56252c00416da2e718_34.png)

以下是导入该工具和定义参数空间的代码：

![](img/12ab71ba21e23f56252c00416da2e718_36.png)

```python
from itertools import product

![](img/12ab71ba21e23f56252c00416da2e718_38.png)

![](img/12ab71ba21e23f56252c00416da2e718_40.png)

![](img/12ab71ba21e23f56252c00416da2e718_42.png)

# 定义短期均线（SMA1）的参数空间，从20到60，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线（SMA2）的参数空间，从180到280，步长为10
sma2_range = range(180, 281, 10)
```

![](img/12ab71ba21e23f56252c00416da2e718_44.png)

![](img/12ab71ba21e23f56252c00416da2e718_46.png)

`product(sma1_range, sma2_range)` 将生成所有可能的 `(SMA1, SMA2)` 组合，例如 `(20, 180)`, `(20, 190)`, `(24, 180)` 等。

![](img/12ab71ba21e23f56252c00416da2e718_48.png)

## 构建参数调优循环

![](img/12ab71ba21e23f56252c00416da2e718_50.png)

接下来，我们将在一个循环中遍历所有参数组合，对每个组合执行完整的策略回测流程，并记录结果。

以下是循环和数据处理的核心步骤：

![](img/12ab71ba21e23f56252c00416da2e718_52.png)

```python
import pandas as pd

![](img/12ab71ba21e23f56252c00416da2e718_54.png)

![](img/12ab71ba21e23f56252c00416da2e718_56.png)

![](img/12ab71ba21e23f56252c00416da2e718_58.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/12ab71ba21e23f56252c00416da2e718_60.png)

![](img/12ab71ba21e23f56252c00416da2e718_62.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载数据（确保每次循环数据是干净的）
    data = pd.read_csv(‘your_data.csv‘, index_col=0, parse_dates=True)[‘AAPL‘]  # 以苹果股价为例
    data = data.dropna()  # 去除缺失值

    # 2. 计算收益率和均线
    returns = data.pct_change().dropna()
    sma1_values = data.rolling(window=sma1).mean().dropna()
    sma2_values = data.rolling(window=sma2).mean().dropna()

    # 3. 生成交易信号（双均线策略）
    # 当短期均线上穿长期均线时，做多（信号为1）；下穿时，平仓（信号为0）
    position = pd.Series(0, index=sma1_values.index)
    position[sma1_values > sma2_values] = 1
    position = position.shift(1).dropna()  # 避免未来函数，信号延迟一天

    # 4. 计算策略收益
    strategy_returns = position * returns
    strategy_returns = strategy_returns.dropna()

    # 5. 计算基准收益（买入并持有）和策略超额收益
    benchmark_returns = returns.loc[strategy_returns.index]
    outperformance = strategy_returns.sum() - benchmark_returns.sum()

    # 6. 将本次循环的结果保存到DataFrame中
    temp_df = pd.DataFrame({
        ‘SMA1‘: [sma1],
        ‘SMA2‘: [sma2],
        ‘Benchmark_Return‘: [benchmark_returns.sum()],
        ‘Strategy_Return‘: [strategy_returns.sum()],
        ‘Outperformance‘: [outperformance]
    })
    results = pd.concat([results, temp_df], ignore_index=True)
```

![](img/12ab71ba21e23f56252c00416da2e718_64.png)

![](img/12ab71ba21e23f56252c00416da2e718_66.png)

## 代码调试与优化

![](img/12ab71ba21e23f56252c00416da2e718_68.png)

![](img/12ab71ba21e23f56252c00416da2e718_70.png)

![](img/12ab71ba21e23f56252c00416da2e718_72.png)

在编写上述代码时，可能会遇到一些错误。以下是两个常见问题及其解决方法：

![](img/12ab71ba21e23f56252c00416da2e718_74.png)

![](img/12ab71ba21e23f56252c00416da2e718_76.png)

![](img/12ab71ba21e23f56252c00416da2e718_78.png)

1.  **数据格式问题**：从CSV读取单列数据后，`pct_change()` 等操作可能因列名丢失而报错。确保数据是 `DataFrame` 格式并具有正确的列名。
2.  **DataFrame拼接问题**：使用 `pd.concat` 时，如果忽略索引设置不当，可能导致索引混乱。使用 `ignore_index=True` 可以重置索引。

![](img/12ab71ba21e23f56252c00416da2e718_80.png)

![](img/12ab71ba21e23f56252c00416da2e718_82.png)

![](img/12ab71ba21e23f56252c00416da2e718_84.png)

## 结果分析与解读

![](img/12ab71ba21e23f56252c00416da2e718_86.png)

代码运行完毕后，`results` DataFrame 将包含所有参数组合的回测结果。我们可以通过排序来快速找到表现最好的参数组合。

![](img/12ab71ba21e23f56252c00416da2e718_88.png)

```python
# 按超额收益（Outperformance）降序排列，查看表现最好的前10个组合
top_results = results.sort_values(by=‘Outperformance‘, ascending=False).head(10)
print(top_results)
```

![](img/12ab71ba21e23f56252c00416da2e718_90.png)

![](img/12ab71ba21e23f56252c00416da2e718_92.png)

分析结果时，你会发现：
*   有些参数组合的策略收益甚至低于基准（买入并持有），说明参数选择不当。
*   大部分参数组合能产生正向的超额收益。
*   通过排序，我们可以直观地找到在当前历史数据上表现最优的 `SMA1` 和 `SMA2` 参数。

![](img/12ab71ba21e23f56252c00416da2e718_94.png)

![](img/12ab71ba21e23f56252c00416da2e718_96.png)

![](img/12ab71ba21e23f56252c00416da2e718_98.png)

---

![](img/12ab71ba21e23f56252c00416da2e718_100.png)

![](img/12ab71ba21e23f56252c00416da2e718_102.png)

![](img/12ab71ba21e23f56252c00416da2e718_104.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们使用 `itertools.product` 遍历了不同的参数组合，为每个组合执行了完整的回测流程，并通过分析结果找到了表现更优的策略配置。这个过程是量化策略开发中优化和验证的关键一步。记住，在历史数据上表现好的参数，在未来不一定持续有效，因此需要结合其他方法进行验证。