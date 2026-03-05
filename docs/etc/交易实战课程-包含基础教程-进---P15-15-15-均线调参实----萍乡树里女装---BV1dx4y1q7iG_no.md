# 量化交易教程：P15：均线调参实例 📈

![](img/6de2cba4fde2494119824ad077f065c2_0.png)

![](img/6de2cba4fde2494119824ad077f065c2_2.png)

![](img/6de2cba4fde2494119824ad077f065c2_4.png)

在本节课中，我们将学习如何对双均线策略中的参数进行调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的策略表现。

![](img/6de2cba4fde2494119824ad077f065c2_6.png)

![](img/6de2cba4fde2494119824ad077f065c2_7.png)

上一节我们介绍了双均线策略的基本原理和实现，本节中我们来看看如何通过参数优化来提升策略效果。

## 参数调优的必要性

![](img/6de2cba4fde2494119824ad077f065c2_9.png)

![](img/6de2cba4fde2494119824ad077f065c2_10.png)

![](img/6de2cba4fde2494119824ad077f065c2_11.png)

策略的最终收益与许多因素有关，其中均线的参数（短期窗口和长期窗口）是关键。我们需要找到合适的参数组合，例如短期是5天还是10天更合适，长期是20天还是40天更合适。在Python中，我们可以轻松地通过迭代来测试这些组合。

![](img/6de2cba4fde2494119824ad077f065c2_13.png)

## 导入迭代工具

![](img/6de2cba4fde2494119824ad077f065c2_15.png)

![](img/6de2cba4fde2494119824ad077f065c2_16.png)

![](img/6de2cba4fde2494119824ad077f065c2_17.png)

首先，我们需要导入用于生成参数组合的工具。

![](img/6de2cba4fde2494119824ad077f065c2_18.png)

```python
from itertools import product
```

![](img/6de2cba4fde2494119824ad077f065c2_20.png)

![](img/6de2cba4fde2494119824ad077f065c2_21.png)

![](img/6de2cba4fde2494119824ad077f065c2_22.png)

`itertools.product` 函数可以帮助我们生成两个参数空间的所有可能组合。

![](img/6de2cba4fde2494119824ad077f065c2_23.png)

![](img/6de2cba4fde2494119824ad077f065c2_24.png)

![](img/6de2cba4fde2494119824ad077f065c2_25.png)

![](img/6de2cba4fde2494119824ad077f065c2_26.png)

![](img/6de2cba4fde2494119824ad077f065c2_27.png)

## 定义参数空间

![](img/6de2cba4fde2494119824ad077f065c2_28.png)

![](img/6de2cba4fde2494119824ad077f065c2_29.png)

![](img/6de2cba4fde2494119824ad077f065c2_31.png)

接下来，我们定义短期均线（SMA1）和长期均线（SMA2）的参数范围。

以下是参数空间的定义：
*   **短期均线（SMA1）**：范围从20到60（步长为4）。
*   **长期均线（SMA2）**：范围从180到280（步长为10）。

![](img/6de2cba4fde2494119824ad077f065c2_33.png)

![](img/6de2cba4fde2494119824ad077f065c2_34.png)

![](img/6de2cba4fde2494119824ad077f065c2_35.png)

```python
sma1_range = range(20, 61, 4)   # 短期均线参数空间
sma2_range = range(180, 281, 10) # 长期均线参数空间
```

![](img/6de2cba4fde2494119824ad077f065c2_36.png)

![](img/6de2cba4fde2494119824ad077f065c2_37.png)

![](img/6de2cba4fde2494119824ad077f065c2_39.png)

`product(sma1_range, sma2_range)` 将生成所有可能的（SMA1， SMA2）组合，例如（20， 180）、（20， 190）……（60， 280）。

![](img/6de2cba4fde2494119824ad077f065c2_41.png)

![](img/6de2cba4fde2494119824ad077f065c2_42.png)

## 构建回测函数

![](img/6de2cba4fde2494119824ad077f065c2_44.png)

我们将之前实现的双均线策略代码整合到一个函数中，以便对每一组参数进行回测。

以下是回测函数的主要步骤：
1.  **数据准备**：重新加载苹果公司股价数据，并处理缺失值。
2.  **计算指标**：根据传入的 `sma1` 和 `sma2` 参数，计算短期均线、长期均线和日收益率。
3.  **生成交易信号**：当短期均线上穿长期均线时，产生买入信号（1）；下穿时，产生卖出信号（-1）。
4.  **计算策略收益**：根据交易信号计算策略的累计收益。
5.  **汇总结果**：将参数和对应的收益结果保存下来。

```python
import pandas as pd

![](img/6de2cba4fde2494119824ad077f065c2_46.png)

![](img/6de2cba4fde2494119824ad077f065c2_47.png)

![](img/6de2cba4fde2494119824ad077f065c2_48.png)

![](img/6de2cba4fde2494119824ad077f065c2_49.png)

def backtest_strategy(sma1, sma2):
    # 1. 加载并准备数据
    data = pd.read_csv(‘你的数据文件路径.csv‘, index_col=0, parse_dates=True)[‘AAPL‘] # 示例，请替换为实际数据
    data = data.dropna()

    # 2. 计算收益率和均线
    returns = data.pct_change()
    sma1_values = data.rolling(window=sma1).mean()
    sma2_values = data.rolling(window=sma2).mean()

    # 3. 生成交易信号
    position = pd.Series(index=data.index, data=0)
    position[sma1_values > sma2_values] = 1
    position[sma1_values < sma2_values] = -1
    # 为了简化，这里假设信号产生次日开盘执行，故将信号滞后一期
    position = position.shift(1)

    # 4. 计算策略收益
    strategy_returns = position * returns
    # 处理可能因滚动计算和移位产生的缺失值
    strategy_returns = strategy_returns.dropna()
    baseline_returns = returns.loc[strategy_returns.index] # 对齐基准收益

    # 5. 计算累计收益（或年化收益等评价指标）
    cumulative_strategy_return = (1 + strategy_returns).prod() - 1
    cumulative_baseline_return = (1 + baseline_returns).prod() - 1
    outperformance = cumulative_strategy_return - cumulative_baseline_return

    return sma1, sma2, cumulative_baseline_return, cumulative_strategy_return, outperformance
```

![](img/6de2cba4fde2494119824ad077f065c2_51.png)

![](img/6de2cba4fde2494119824ad077f065c2_52.png)

## 遍历参数并收集结果

![](img/6de2cba4fde2494119824ad077f065c2_54.png)

![](img/6de2cba4fde2494119824ad077f065c2_55.png)

![](img/6de2cba4fde2494119824ad077f065c2_56.png)

现在，我们遍历所有参数组合，运行回测函数，并将结果收集到一个DataFrame中。

![](img/6de2cba4fde2494119824ad077f065c2_58.png)

```python
results_list = []

![](img/6de2cba4fde2494119824ad077f065c2_59.png)

![](img/6de2cba4fde2494119824ad077f065c2_60.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    result = backtest_strategy(sma1, sma2)
    results_list.append(result)

![](img/6de2cba4fde2494119824ad077f065c2_62.png)

![](img/6de2cba4fde2494119824ad077f065c2_63.png)

![](img/6de2cba4fde2494119824ad077f065c2_64.png)

![](img/6de2cba4fde2494119824ad077f065c2_65.png)

![](img/6de2cba4fde2494119824ad077f065c2_66.png)

# 将结果列表转换为DataFrame
results_df = pd.DataFrame(results_list,
                          columns=[‘SMA1‘, ‘SMA2‘, ‘基准收益‘, ‘策略收益‘, ‘超额收益‘])
results_df = results_df.reset_index(drop=True) # 重置索引

![](img/6de2cba4fde2494119824ad077f065c2_67.png)

![](img/6de2cba4fde2494119824ad077f065c2_69.png)

![](img/6de2cba4fde2494119824ad077f065c2_70.png)

# 查看结果
print(results_df.head(10))
```

![](img/6de2cba4fde2494119824ad077f065c2_72.png)

![](img/6de2cba4fde2494119824ad077f065c2_73.png)

## 结果分析

![](img/6de2cba4fde2494119824ad077f065c2_74.png)

运行上述代码后，我们将得到一个结果表格。其中 `超额收益` 列表示策略收益相对于简单持有（基准收益）的优劣。

以下是结果分析要点：
*   如果 `超额收益` 为正，说明该参数组合下的策略表现优于简单持有。
*   如果 `超额收益` 为负，说明该参数组合效果不佳，甚至不如不采取策略。
*   我们可以对 `results_df` 进行排序，例如按 `超额收益` 降序排列，来快速找到表现最好的参数组合。

![](img/6de2cba4fde2494119824ad077f065c2_76.png)

![](img/6de2cba4fde2494119824ad077f065c2_77.png)

![](img/6de2cba4fde2494119824ad077f065c2_78.png)

```python
# 按超额收益排序，找到最佳参数
best_params = results_df.sort_values(by=‘超额收益‘, ascending=False).iloc[0]
print(“最佳参数组合：“)
print(best_params)
```

## 总结

![](img/6de2cba4fde2494119824ad077f065c2_80.png)

![](img/6de2cba4fde2494119824ad077f065c2_81.png)

![](img/6de2cba4fde2494119824ad077f065c2_82.png)

![](img/6de2cba4fde2494119824ad077f065c2_83.png)

![](img/6de2cba4fde2494119824ad077f065c2_84.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们通过定义参数空间、构建回测函数、遍历所有参数组合并分析结果，系统地评估了不同参数对策略表现的影响。这个方法不仅适用于均线策略，也可以扩展到其他需要参数优化的量化策略中。通过参数优化，我们可以努力寻找使策略表现更优的配置，但需要注意避免在历史数据上过度拟合。