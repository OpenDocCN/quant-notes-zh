# Python金融分析与量化交易实战：P15：4-均线调参实例 📈

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_0.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_2.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_4.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_6.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_8.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的策略表现。

## 概述

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_10.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_12.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_14.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略中的参数（短期均线周期和长期均线周期）来优化策略效果。我们将使用Python的`itertools.product`工具来遍历所有可能的参数组合，并计算每种组合下的策略收益。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_16.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_18.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_20.png)

## 参数空间定义

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_22.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_24.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_26.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_28.png)

首先，我们需要定义要测试的短期均线（SMA1）和长期均线（SMA2）的参数范围。以下是参数空间的设置方法。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_30.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_32.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_34.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_36.png)

以下是短期和长期均线参数的取值范围：
*   **短期均线 (SMA1)**：从20天到60天，步长为4。即测试 `[20, 24, 28, ..., 60]`。
*   **长期均线 (SMA2)**：从180天到280天，步长为10。即测试 `[180, 190, 200, ..., 280]`。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_38.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_40.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_42.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_44.png)

## 使用迭代工具

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_46.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_48.png)

为了遍历所有参数组合，我们需要导入`itertools`库中的`product`函数。这个函数可以生成两个或多个序列的笛卡尔积，即所有可能的组合。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_50.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_52.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_54.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_56.png)

以下是导入迭代工具和定义参数空间的代码：
```python
from itertools import product

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_58.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_60.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_62.png)

# 定义参数空间
sma1_range = range(20, 61, 4)  # 短期均线参数：20, 24, 28, ..., 60
sma2_range = range(180, 281, 10) # 长期均线参数：180, 190, 200, ..., 280
```

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_64.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_66.png)

## 遍历参数组合与策略回测

接下来，我们将在一个循环中遍历所有参数组合，并对每种组合执行完整的双均线策略计算流程。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_68.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_70.png)

以下是遍历所有参数组合并执行策略回测的核心步骤：
1.  **重新加载数据**：为确保每次计算的数据是干净的，我们重新加载苹果公司（AAPL）的股价数据。
2.  **数据预处理**：处理缺失值。
3.  **计算指标**：根据当前循环中的`sma1`和`sma2`参数，计算收益率、短期均线和长期均线。
4.  **生成交易信号**：根据双均线交叉规则生成持仓信号（`position`）。
5.  **计算策略收益**：根据交易信号计算策略的累计收益。
6.  **汇总结果**：将当前参数组合及对应的策略收益结果保存下来。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_72.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_74.png)

```python
import pandas as pd
import yfinance as yf

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_76.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_78.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_80.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_82.png)

# 重新加载数据
data = yf.download('AAPL', start='2020-01-01', end='2023-01-01')['Adj Close']
data = pd.DataFrame(data) # 确保是DataFrame格式
data.dropna(inplace=True) # 处理缺失值

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_84.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_86.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_88.png)

# 初始化一个空的DataFrame来存储所有结果
results_df = pd.DataFrame()

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_90.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_92.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_94.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_96.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 复制原始数据，避免修改原数据
    df = data.copy()
    
    # 计算收益率
    df['returns'] = np.log(df['Adj Close'] / df['Adj Close'].shift(1))
    
    # 计算短期和长期移动平均线
    df['SMA1'] = df['Adj Close'].rolling(window=sma1).mean()
    df['SMA2'] = df['Adj Close'].rolling(window=sma2).mean()
    
    # 根据均线交叉生成交易信号：短期均线上穿长期均线为1（买入），否则为0（卖出/空仓）
    df['position'] = np.where(df['SMA1'] > df['SMA2'], 1, 0)
    
    # 计算策略收益：持仓信号 * 次日收益率
    df['strategy'] = df['position'].shift(1) * df['returns']
    
    # 删除计算产生的缺失值
    df.dropna(inplace=True)
    
    # 计算累计收益
    cum_returns = df['returns'].cumsum().apply(np.exp)
    cum_strategy = df['strategy'].cumsum().apply(np.exp)
    
    # 计算策略相对于简单买入持有的超额收益（最终净值）
    outperformance = cum_strategy.iloc[-1] - cum_returns.iloc[-1]
    
    # 将本次参数组合的结果保存为一行数据
    result_row = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Buy&Hold_Return': [cum_returns.iloc[-1]],
        'Strategy_Return': [cum_strategy.iloc[-1]],
        'Outperformance': [outperformance]
    })
    
    # 将当前结果追加到总结果表中
    results_df = pd.concat([results_df, result_row], ignore_index=True)
```

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_98.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_100.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_102.png)

## 结果分析与解读

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_104.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_106.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_108.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_110.png)

代码执行完毕后，`results_df`中包含了所有参数组合的回测结果。我们可以通过排序来找出表现最好的参数组合。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_112.png)

以下是查看和分析结果的示例：
```python
# 按超额收益降序排列，查看表现最好的参数组合
print(results_df.sort_values(by='Outperformance', ascending=False).head(10))

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_114.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_116.png)

# 也可以找出绝对收益最高的策略
print(results_df.sort_values(by='Strategy_Return', ascending=False).head(10))
```
通过观察结果可以发现：
*   不同的参数组合会导致策略表现差异巨大。
*   有些参数组合下，策略收益甚至不如简单的买入持有（`Outperformance`为负值）。
*   我们的目标就是通过这样的遍历，找到在该段历史数据上表现相对更优的参数组合。

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_118.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_120.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_122.png)

## 总结

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_124.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_126.png)

![](img/3194ed01c8bab6d2cdb778d3c4482ac4_128.png)

本节课中我们一起学习了如何对双均线交易策略进行参数优化。我们掌握了定义参数空间、使用`itertools.product`遍历所有组合、以及在循环中集成策略回测流程的方法。通过这种方式，我们可以系统地评估不同参数对策略效果的影响，并为策略选择更合适的参数，这是量化策略开发中至关重要的一步。