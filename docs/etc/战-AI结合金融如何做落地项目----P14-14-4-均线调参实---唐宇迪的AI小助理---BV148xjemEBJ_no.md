# Python金融分析与量化交易实战：P14：14.4-均线调参实例 📈

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_0.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_2.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_4.png)

在本节课中，我们将学习如何对双均线策略中的参数进行调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

上一节我们介绍了双均线策略的基本实现，本节中我们来看看如何通过调整参数来优化策略表现。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_6.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_8.png)

## 概述

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_10.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_12.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_14.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_16.png)

双均线策略的表现与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。为了找到更优的参数组合，我们可以系统地遍历一系列可能的参数值，并计算每种组合下的策略表现。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_18.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_20.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_22.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_24.png)

## 参数空间定义与遍历

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_26.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_28.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_30.png)

首先，我们需要导入用于生成参数组合的工具 `product`。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_32.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_34.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_36.png)

```python
from itertools import product
```

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_38.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_40.png)

`product` 函数可以帮助我们生成两个参数空间的所有可能组合。例如，我们可以定义短期均线参数从20到60（步长为4），长期均线参数从180到280（步长为10）。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_42.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_44.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_46.png)

以下是定义参数空间并进行遍历的代码：

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_48.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_50.png)

```python
# 定义短期均线（SMA1）和长期均线（SMA2）的参数空间
sma1_range = range(20, 61, 4)   # 20, 24, 28, ..., 60
sma2_range = range(180, 281, 10) # 180, 190, 200, ..., 280

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_52.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_54.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 在这里执行策略计算
    # ...
```

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_56.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_58.png)

## 策略计算流程

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_60.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_62.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_64.png)

在每次循环中，我们需要用当前的 `sma1` 和 `sma2` 参数重新计算策略。以下是每一步的核心操作：

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_66.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_68.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_70.png)

1.  **数据准备**：重新加载苹果公司（AAPL）的股价数据，并处理缺失值。
2.  **计算指标**：计算收益率、短期均线和长期均线。
3.  **生成交易信号**：根据双均线交叉规则生成持仓信号（`position`）。
4.  **计算策略收益**：根据持仓信号计算策略的累计收益。
5.  **评估表现**：计算策略收益相对于简单持有（基准收益）的超额收益。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_72.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_74.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_76.png)

以下是整合了上述步骤的代码框架：

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_78.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_80.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_82.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_84.png)

```python
    # 1. 加载并准备数据
    data = pd.DataFrame(raw_data['AAPL']) # 假设raw_data是原始数据源
    data = data.dropna()

    # 2. 计算收益率和均线
    data['returns'] = np.log(data / data.shift(1))
    data['SMA1'] = data['AAPL'].rolling(window=sma1).mean()
    data['SMA2'] = data['AAPL'].rolling(window=sma2).mean()
    data = data.dropna()

    # 3. 生成交易信号：短期均线上穿长期均线时持仓为1，否则为0
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, 0)

    # 4. 计算策略收益
    data['strategy'] = data['position'].shift(1) * data['returns']
    data = data.dropna()

    # 5. 计算累计收益和超额收益
    cumulative_returns = data[['returns', 'strategy']].cumsum().apply(np.exp)
    outperformance = cumulative_returns['strategy'].iloc[-1] - cumulative_returns['returns'].iloc[-1]

    # 6. 将本次结果保存到总表中
    result_table = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Cumulative Returns': [cumulative_returns['returns'].iloc[-1]],
        'Cumulative Strategy': [cumulative_returns['strategy'].iloc[-1]],
        'Outperformance': [outperformance]
    })
    results = pd.concat([results, result_table], ignore_index=True)
```

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_86.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_88.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_90.png)

## 结果分析与解读

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_92.png)

运行完所有参数组合的遍历后，我们可以查看 `results` 表格。表格中包含了每种参数组合下的累计收益、策略累计收益以及策略相对于基准的超额收益（`Outperformance`）。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_94.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_96.png)

通过分析 `Outperformance` 列，我们可以：
*   找出 `Outperformance` 为正且值较大的参数组合，这些可能是相对较优的策略参数。
*   观察不同参数对策略表现的影响，理解参数敏感性。
*   认识到并非所有参数组合都能战胜市场，参数选择需要谨慎。

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_98.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_100.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_102.png)

## 总结

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_104.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_106.png)

![](img/5738f51e4fa4e2f5e81f3fb6f519fa43_108.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用 `itertools.product` 函数遍历了不同的短期和长期均线窗口组合，并通过完整的策略回测流程评估了每一种组合的表现。这种方法帮助我们系统地寻找更优的策略参数，是量化策略开发中至关重要的一步。