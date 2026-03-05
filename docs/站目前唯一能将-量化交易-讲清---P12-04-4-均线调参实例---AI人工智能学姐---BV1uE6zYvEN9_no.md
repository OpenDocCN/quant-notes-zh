# Python金融量化：P12：04-4-均线调参实例 📈

![](img/a20e81d3cee26045b6e08bc248de901e_0.png)

![](img/a20e81d3cee26045b6e08bc248de901e_2.png)

![](img/a20e81d3cee26045b6e08bc248de901e_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

---

![](img/a20e81d3cee26045b6e08bc248de901e_6.png)

![](img/a20e81d3cee26045b6e08bc248de901e_8.png)

上一节我们介绍了双均线策略的基本实现与回测。本节中，我们来看看如何通过调整策略参数来优化策略表现。

![](img/a20e81d3cee26045b6e08bc248de901e_10.png)

![](img/a20e81d3cee26045b6e08bc248de901e_12.png)

![](img/a20e81d3cee26045b6e08bc248de901e_14.png)

![](img/a20e81d3cee26045b6e08bc248de901e_16.png)

策略的最终收益与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天合适还是10天合适，长期窗口是20天合适还是40天合适。

![](img/a20e81d3cee26045b6e08bc248de901e_18.png)

![](img/a20e81d3cee26045b6e08bc248de901e_20.png)

![](img/a20e81d3cee26045b6e08bc248de901e_22.png)

![](img/a20e81d3cee26045b6e08bc248de901e_24.png)

在Python中，我们可以借助工具轻松地遍历所有参数组合。

![](img/a20e81d3cee26045b6e08bc248de901e_26.png)

![](img/a20e81d3cee26045b6e08bc248de901e_28.png)

![](img/a20e81d3cee26045b6e08bc248de901e_30.png)

![](img/a20e81d3cee26045b6e08bc248de901e_32.png)

以下是实现参数遍历的步骤：

![](img/a20e81d3cee26045b6e08bc248de901e_34.png)

![](img/a20e81d3cee26045b6e08bc248de901e_36.png)

首先，我们需要导入必要的工具。`itertools`库中的`product`函数可以帮助我们生成所有可能的参数组合。

![](img/a20e81d3cee26045b6e08bc248de901e_38.png)

![](img/a20e81d3cee26045b6e08bc248de901e_40.png)

![](img/a20e81d3cee26045b6e08bc248de901e_42.png)

```python
from itertools import product
```

![](img/a20e81d3cee26045b6e08bc248de901e_44.png)

![](img/a20e81d3cee26045b6e08bc248de901e_46.png)

接下来，我们定义参数空间。例如，短期均线窗口从20到60，步长为4；长期均线窗口从180到280，步长为10。

![](img/a20e81d3cee26045b6e08bc248de901e_48.png)

![](img/a20e81d3cee26045b6e08bc248de901e_50.png)

```python
sma1_range = range(20, 61, 4)   # 短期均线参数空间
sma2_range = range(180, 281, 10) # 长期均线参数空间
```

`product`函数会生成这两个参数空间的所有组合，例如(20, 180), (20, 190), (24, 180)等。然后，我们在一个循环中遍历这些组合，对每一组参数重新计算策略并评估其表现。

![](img/a20e81d3cee26045b6e08bc248de901e_52.png)

![](img/a20e81d3cee26045b6e08bc248de901e_54.png)

以下是核心的遍历与计算循环结构：

![](img/a20e81d3cee26045b6e08bc248de901e_56.png)

![](img/a20e81d3cee26045b6e08bc248de901e_58.png)

![](img/a20e81d3cee26045b6e08bc248de901e_60.png)

```python
import pandas as pd

![](img/a20e81d3cee26045b6e08bc248de901e_62.png)

![](img/a20e81d3cee26045b6e08bc248de901e_64.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/a20e81d3cee26045b6e08bc248de901e_66.png)

![](img/a20e81d3cee26045b6e08bc248de901e_68.png)

![](img/a20e81d3cee26045b6e08bc248de901e_70.png)

# 重新加载苹果股价数据，确保数据干净
data = pd.read_csv(‘your_data.csv‘, index_col=‘Date‘, parse_dates=True)[‘AAPL‘]
data = data.dropna()  # 去除缺失值

![](img/a20e81d3cee26045b6e08bc248de901e_72.png)

![](img/a20e81d3cee26045b6e08bc248de901e_74.png)

![](img/a20e81d3cee26045b6e08bc248de901e_76.png)

![](img/a20e81d3cee26045b6e08bc248de901e_78.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 计算收益率
    returns = data.pct_change()
    
    # 2. 计算短期和长期均线
    sma1_values = data.rolling(window=sma1).mean()
    sma2_values = data.rolling(window=sma2).mean()
    
    # 3. 去除计算均线产生的缺失值
    data_clean = data.copy()
    data_clean[‘Returns‘] = returns
    data_clean[‘SMA1‘] = sma1_values
    data_clean[‘SMA2‘] = sma2_values
    data_clean = data_clean.dropna()
    
    # 4. 生成交易信号（当短期均线上穿长期均线时买入/持有）
    data_clean[‘Position‘] = 0
    data_clean.loc[data_clean[‘SMA1‘] > data_clean[‘SMA2‘], ‘Position‘] = 1
    
    # 5. 计算策略收益率（假设每日根据Position调整头寸）
    data_clean[‘Strategy‘] = data_clean[‘Position‘].shift(1) * data_clean[‘Returns‘]
    
    # 6. 计算累计收益，并对比“买入持有”策略
    cum_returns = (data_clean[[‘Returns‘, ‘Strategy‘]] + 1).cumprod()
    outperformance = cum_returns.iloc[-1][‘Strategy‘] - cum_returns.iloc[-1][‘Returns‘]
    
    # 7. 将本次参数组合及结果保存
    result_table = pd.DataFrame({
        ‘SMA1‘: [sma1],
        ‘SMA2‘: [sma2],
        ‘Returns‘: [cum_returns.iloc[-1][‘Returns‘]],
        ‘Strategy‘: [cum_returns.iloc[-1][‘Strategy‘]],
        ‘Outperformance‘: [outperformance]
    })
    
    # 将本次结果追加到总结果表中
    results = pd.concat([results, result_table], ignore_index=True)
```

![](img/a20e81d3cee26045b6e08bc248de901e_80.png)

![](img/a20e81d3cee26045b6e08bc248de901e_82.png)

![](img/a20e81d3cee26045b6e08bc248de901e_84.png)

在代码执行过程中，可能会遇到一些错误，例如数据列名丢失或DataFrame索引问题。一个常见的解决方法是确保每次计算后，数据都被正确地包装在DataFrame中，并处理好索引。

![](img/a20e81d3cee26045b6e08bc248de901e_86.png)

运行上述代码后，我们可以查看`results`这个DataFrame。它列出了每一组参数对应的“买入持有”收益、策略收益以及策略的超额收益（`Outperformance`）。

![](img/a20e81d3cee26045b6e08bc248de901e_88.png)

![](img/a20e81d3cee26045b6e08bc248de901e_90.png)

![](img/a20e81d3cee26045b6e08bc248de901e_92.png)

通过观察结果，我们可以发现：
*   有些参数组合下，策略收益甚至不如简单的“买入持有”策略。
*   大部分参数组合下，策略能带来正向的超额收益。
*   我们可以根据`Outperformance`列对结果进行排序，从而找到在本段历史数据上表现最优的参数组合。

![](img/a20e81d3cee26045b6e08bc248de901e_94.png)

![](img/a20e81d3cee26045b6e08bc248de901e_96.png)

---

![](img/a20e81d3cee26045b6e08bc248de901e_98.png)

![](img/a20e81d3cee26045b6e08bc248de901e_100.png)

![](img/a20e81d3cee26045b6e08bc248de901e_102.png)

![](img/a20e81d3cee26045b6e08bc248de901e_104.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们使用`itertools.product`遍历了不同的短期和长期均线窗口组合，并对每一组参数进行了完整的策略回测和收益对比。这个过程是量化策略开发中优化和验证的关键步骤，帮助我们理解参数如何影响策略表现，并寻找更优的配置。