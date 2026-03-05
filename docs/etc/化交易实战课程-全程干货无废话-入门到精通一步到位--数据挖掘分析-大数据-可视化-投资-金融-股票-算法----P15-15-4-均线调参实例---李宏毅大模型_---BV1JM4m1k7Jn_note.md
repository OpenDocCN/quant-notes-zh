# Python金融分析与量化交易实战：P15：15.4：均线调参实例 📈

![](img/5b9c2c0b33fef2e86daa40b8473902e1_0.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_2.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_4.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_6.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的策略收益。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_8.png)

---

![](img/5b9c2c0b33fef2e86daa40b8473902e1_10.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_12.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_14.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略参数来优化其表现。策略的收益与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最优的参数组合。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_16.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_18.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_20.png)

以下是实现参数调优的核心步骤：

![](img/5b9c2c0b33fef2e86daa40b8473902e1_22.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_24.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_26.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_28.png)

1.  **导入必要的工具**：我们将使用 `itertools.product` 来生成所有可能的参数组合。
2.  **定义参数空间**：为短期均线和长期均线分别设定一个可能的取值范围。
3.  **遍历参数组合**：对每一组参数，重新计算均线、生成交易信号并回测策略收益。
4.  **汇总并比较结果**：将不同参数组合下的策略收益汇总成表格，并与基准收益（不采取策略）进行比较。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_30.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_32.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_34.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_36.png)

首先，我们需要导入用于生成参数组合的工具。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_38.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_40.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_42.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_44.png)

```python
from itertools import product
```

![](img/5b9c2c0b33fef2e86daa40b8473902e1_46.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_48.png)

接下来，我们定义短期均线（SMA1）和长期均线（SMA2）的参数空间。例如，短期均线窗口从20到60，步长为4；长期均线窗口从180到280，步长为10。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_50.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_52.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_54.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_56.png)

```python
sma1_range = range(20, 61, 4)   # 短期均线参数空间
sma2_range = range(180, 281, 10) # 长期均线参数空间
```

![](img/5b9c2c0b33fef2e86daa40b8473902e1_58.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_60.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_62.png)

`itertools.product` 函数会生成这两个范围所有可能的组合，例如 (20, 180), (20, 190), (24, 180) 等。我们将遍历这些组合。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_64.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_66.png)

在开始遍历前，我们需要重新加载股价数据，并确保数据格式正确。

```python
import pandas as pd
# 假设我们重新加载苹果公司股价数据，这里用 ‘AAPL‘ 作为示例
data = pd.read_csv(‘your_stock_data.csv‘, index_col=‘Date‘, parse_dates=True)[‘AAPL‘]
data = data.dropna()  # 去除缺失值
```

![](img/5b9c2c0b33fef2e86daa40b8473902e1_68.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_70.png)

现在，我们进入核心的循环遍历部分。对于每一组参数 (sma1, sma2)，我们执行以下操作：

![](img/5b9c2c0b33fef2e86daa40b8473902e1_72.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_74.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_76.png)

以下是每一轮迭代中需要执行的代码逻辑：

![](img/5b9c2c0b33fef2e86daa40b8473902e1_78.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_80.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_82.png)

*   **计算收益率**：`returns = data.pct_change().dropna()`
*   **计算短期均线**：`sma1 = data.rolling(window=sma1).mean().dropna()`
*   **计算长期均线**：`sma2 = data.rolling(window=sma2).mean().dropna()`
*   **生成交易信号**：当短期均线上穿长期均线时，做多（标记为1）；下穿时，平仓（标记为0）。
*   **计算策略收益**：`strategy_returns = position.shift(1) * returns`
*   **汇总结果**：将参数和对应的累计收益记录下来。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_84.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_86.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_88.png)

我们将所有步骤整合在一个循环中，并初始化一个空的DataFrame来存储结果。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_90.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_92.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_94.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_96.png)

```python
results = pd.DataFrame()  # 初始化结果表格

![](img/5b9c2c0b33fef2e86daa40b8473902e1_98.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_100.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_102.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_104.png)

for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 计算收益率
    returns = data.pct_change().dropna()
    
    # 2. 计算双均线
    sma1_series = data.rolling(window=sma1).mean().dropna()
    sma2_series = data.rolling(window=sma2).mean().dropna()
    
    # 3. 生成交易信号 (1为持有，0为空仓)
    position = pd.Series(0, index=sma1_series.index)
    position[sma1_series > sma2_series] = 1
    position = position.dropna()
    
    # 4. 计算策略收益 (考虑交易延迟，使用shift(1))
    strategy_returns = position.shift(1) * returns.loc[position.index]
    strategy_returns = strategy_returns.dropna()
    
    # 5. 计算累计收益
    cum_returns = (1 + returns).cumprod().iloc[-1] - 1  # 基准累计收益
    cum_strategy = (1 + strategy_returns).cumprod().iloc[-1] - 1  # 策略累计收益
    outperformance = cum_strategy - cum_returns  # 策略超额收益
    
    # 6. 将结果存入表格
    temp_df = pd.DataFrame({
        ‘SMA1‘: [sma1],
        ‘SMA2‘: [sma2],
        ‘Benchmark_Return‘: [cum_returns],
        ‘Strategy_Return‘: [cum_strategy],
        ‘Outperformance‘: [outperformance]
    })
    results = pd.concat([results, temp_df], ignore_index=True)
```

![](img/5b9c2c0b33fef2e86daa40b8473902e1_106.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_108.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_110.png)

运行完成后，我们可以查看 `results` 表格。表格中包含了每一组参数对应的基准收益、策略收益以及策略相对于基准的超额收益（Outperformance）。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_112.png)

```python
print(results.head(10))  # 查看前10组参数的结果
```

![](img/5b9c2c0b33fef2e86daa40b8473902e1_114.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_116.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_118.png)

通过观察 `Outperformance` 列，我们可以快速识别哪些参数组合使策略表现优于简单持有（正数），哪些组合反而更差（负数）。这为我们选择最优的均线参数提供了数据依据。

![](img/5b9c2c0b33fef2e86daa40b8473902e1_120.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_122.png)

---

![](img/5b9c2c0b33fef2e86daa40b8473902e1_124.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_126.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_128.png)

![](img/5b9c2c0b33fef2e86daa40b8473902e1_130.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用 `itertools.product` 遍历了不同的参数组合，并通过回测计算了每种组合的策略收益。最后，通过比较策略超额收益，我们可以科学地筛选出表现更优的参数，从而优化我们的交易策略。这是一个在量化实践中非常实用的网格搜索（Grid Search）方法的简单应用。