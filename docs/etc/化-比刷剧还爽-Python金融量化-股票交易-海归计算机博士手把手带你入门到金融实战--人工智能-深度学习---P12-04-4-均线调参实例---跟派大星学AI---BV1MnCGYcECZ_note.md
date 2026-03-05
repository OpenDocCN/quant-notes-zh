# 量化交易全教程：P12：04-4-均线调参实例 📈

![](img/a6150493506bda6993a2a4bcf0e1d4ab_0.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_2.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

## 概述

![](img/a6150493506bda6993a2a4bcf0e1d4ab_6.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_8.png)

上一节我们介绍了双均线策略的基本实现。本节中，我们来看看如何通过调整策略参数来优化其表现。核心问题是：短期均线（SMA1）和长期均线（SMA2）的窗口大小如何影响策略的最终收益？我们将通过编写代码，系统地测试不同的参数组合来寻找答案。

![](img/a6150493506bda6993a2a4bcf0e1d4ab_10.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_12.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_14.png)

## 参数空间定义

![](img/a6150493506bda6993a2a4bcf0e1d4ab_16.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_18.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_20.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_22.png)

首先，我们需要定义要测试的短期和长期均线参数范围。我们将使用Python的`itertools.product`函数来生成所有可能的参数组合。

![](img/a6150493506bda6993a2a4bcf0e1d4ab_24.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_26.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_28.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_30.png)

以下是参数空间定义的代码：

![](img/a6150493506bda6993a2a4bcf0e1d4ab_32.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_34.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_36.png)

```python
from itertools import product

![](img/a6150493506bda6993a2a4bcf0e1d4ab_38.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_40.png)

# 定义短期均线（SMA1）的参数空间：从20天到60天，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线（SMA2）的参数空间：从180天到280天，步长为10
sma2_range = range(180, 281, 10)
```

![](img/a6150493506bda6993a2a4bcf0e1d4ab_42.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_44.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_46.png)

## 遍历参数组合与策略回测

![](img/a6150493506bda6993a2a4bcf0e1d4ab_48.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_50.png)

接下来，我们将遍历所有参数组合，对每个组合执行完整的双均线策略计算，并记录结果。

![](img/a6150493506bda6993a2a4bcf0e1d4ab_52.png)

以下是遍历与回测的核心循环结构：

```python
import pandas as pd

![](img/a6150493506bda6993a2a4bcf0e1d4ab_54.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/a6150493506bda6993a2a4bcf0e1d4ab_56.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_58.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_60.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_62.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载并准备数据（例如苹果股价）
    data = ... # 加载数据代码
    data = data.dropna() # 去除缺失值

    # 2. 计算收益率和均线
    data['returns'] = ... # 计算收益率
    data['SMA1'] = data['Close'].rolling(window=sma1).mean()
    data['SMA2'] = data['Close'].rolling(window=sma2).mean()
    data = data.dropna()

    # 3. 生成交易信号（金叉买入，死叉卖出）
    data['position'] = ... # 根据SMA1和SMA2生成持仓信号（1或-1）
    data = data.dropna()

    # 4. 计算策略收益
    data['strategy'] = ... # 策略收益 = 持仓信号 * 未来收益率
    data = data.dropna()

    # 5. 汇总本次参数组合的结果
    result = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Returns': [data['returns'].sum()], # 基准收益（买入并持有）
        'Strategy': [data['strategy'].sum()], # 策略收益
        'Outperformance': [data['strategy'].sum() - data['returns'].sum()] # 策略超额收益
    })

    # 6. 将本次结果追加到总结果表中
    results = results.append(result, ignore_index=True)
```

![](img/a6150493506bda6993a2a4bcf0e1d4ab_64.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_66.png)

## 结果分析与解读

![](img/a6150493506bda6993a2a4bcf0e1d4ab_68.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_70.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_72.png)

执行完上述代码后，我们会得到一个包含所有参数组合及其对应收益结果的表格。

![](img/a6150493506bda6993a2a4bcf0e1d4ab_74.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_76.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_78.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_80.png)

以下是查看和分析结果的示例：

![](img/a6150493506bda6993a2a4bcf0e1d4ab_82.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_84.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_86.png)

```python
# 查看前10个结果
print(results.head(10))

# 可以按‘Outperformance’（超额收益）进行排序，找到表现最好的参数组合
best_params = results.sort_values(by='Outperformance', ascending=False).head(1)
print("表现最佳的参数组合：")
print(best_params)
```

![](img/a6150493506bda6993a2a4bcf0e1d4ab_88.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_90.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_92.png)

通过分析`results`表格，我们可以观察到：
1.  不同的`(SMA1, SMA2)`组合会产生不同的收益结果。
2.  `Outperformance`列直观地展示了策略收益相对于简单买入持有（`Returns`）的优劣。正值表示策略跑赢市场，负值则表示跑输。
3.  并非所有参数组合都能带来正收益，这凸显了参数优化的重要性。

![](img/a6150493506bda6993a2a4bcf0e1d4ab_94.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_96.png)

## 总结

![](img/a6150493506bda6993a2a4bcf0e1d4ab_98.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_100.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_102.png)

![](img/a6150493506bda6993a2a4bcf0e1d4ab_104.png)

本节课中我们一起学习了双均线策略的参数调优方法。我们首先定义了短期和长期均线的参数搜索空间，然后通过遍历所有组合，系统地回测了每个参数下的策略表现，并将结果汇总分析。这个过程是量化策略开发中至关重要的一步，它帮助我们理解策略对参数的敏感性，并可能找到更优的策略配置。记住，在历史数据上表现好的参数，在未来不一定持续有效，在实际应用中还需结合其他分析方法。