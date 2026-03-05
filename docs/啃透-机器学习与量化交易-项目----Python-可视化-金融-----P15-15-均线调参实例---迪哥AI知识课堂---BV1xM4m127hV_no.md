# 机器学习与量化交易：P15：均线调参实例 📈

![](img/61cfc64f420d6202eeb037b93a37b7ec_0.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_2.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_4.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_6.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_8.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的投资回报。

![](img/61cfc64f420d6202eeb037b93a37b7ec_10.png)

---

![](img/61cfc64f420d6202eeb037b93a37b7ec_12.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_14.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_16.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略中的参数来优化其表现。

![](img/61cfc64f420d6202eeb037b93a37b7ec_18.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_20.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_22.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_24.png)

策略的最终收益与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天还是10天更合适，长期窗口是20天还是40天更合适。

![](img/61cfc64f420d6202eeb037b93a37b7ec_26.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_28.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_30.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_32.png)

在Python中，我们可以使用迭代工具轻松地遍历所有可能的参数组合。

![](img/61cfc64f420d6202eeb037b93a37b7ec_34.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_36.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_38.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_40.png)

以下是实现参数遍历的步骤：

![](img/61cfc64f420d6202eeb037b93a37b7ec_42.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_44.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_46.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_48.png)

首先，我们需要导入必要的工具。`itertools`库中的`product`函数可以帮助我们生成所有参数组合的笛卡尔积。

![](img/61cfc64f420d6202eeb037b93a37b7ec_50.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_52.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_54.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_56.png)

```python
from itertools import product
```

![](img/61cfc64f420d6202eeb037b93a37b7ec_58.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_60.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_62.png)

接下来，我们定义短期均线（SMA1）和长期均线（SMA2）的参数空间。例如，短期窗口可以从20天到60天，步长为4；长期窗口可以从180天到280天，步长为10。

![](img/61cfc64f420d6202eeb037b93a37b7ec_64.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_66.png)

```python
# 定义参数空间
sma1_range = range(20, 61, 4)   # 短期均线窗口：20, 24, 28, ..., 60
sma2_range = range(180, 281, 10) # 长期均线窗口：180, 190, 200, ..., 280
```

![](img/61cfc64f420d6202eeb037b93a37b7ec_68.png)

`product`函数会生成所有可能的组合，例如(20, 180), (20, 190), (24, 180), (24, 190)等。然后，我们可以在一个循环中遍历这些组合，对每个组合执行一次完整的策略回测。

![](img/61cfc64f420d6202eeb037b93a37b7ec_70.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_72.png)

以下是遍历参数并执行策略的核心循环结构：

![](img/61cfc64f420d6202eeb037b93a37b7ec_74.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_76.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_78.png)

```python
import pandas as pd

![](img/61cfc64f420d6202eeb037b93a37b7ec_80.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_82.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_84.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_86.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/61cfc64f420d6202eeb037b93a37b7ec_88.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_90.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_92.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载或复制原始股价数据（例如苹果股票）
    data = ... # 加载数据代码

    # 2. 数据预处理：处理缺失值
    data = data.dropna()

    # 3. 计算收益率和均线
    data['returns'] = ... # 计算每日收益率
    data['SMA1'] = data['Close'].rolling(window=sma1).mean() # 短期均线
    data['SMA2'] = data['Close'].rolling(window=sma2).mean() # 长期均线

    # 4. 生成交易信号（金叉买入，死叉卖出）
    data['position'] = ... # 根据SMA1和SMA2生成持仓信号

    # 5. 计算策略收益率
    data['strategy'] = ... # 根据持仓信号计算策略累计收益

    # 6. 清理中间步骤产生的缺失值
    data = data.dropna()

    # 7. 计算策略相对于简单持有（买入并持有）的超额收益
    outperformance = data['strategy'].iloc[-1] - data['returns'].iloc[-1]

    # 8. 将本次参数组合的结果保存到results DataFrame中
    result_row = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Buy&Hold_Return': [data['returns'].iloc[-1]],
        'Strategy_Return': [data['strategy'].iloc[-1]],
        'Outperformance': [outperformance]
    })
    results = pd.concat([results, result_row], ignore_index=True)
```

![](img/61cfc64f420d6202eeb037b93a37b7ec_94.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_96.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_98.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_100.png)

在实现过程中，可能会遇到一些技术细节问题，例如数据格式错误或索引设置不当。关键在于确保每一步操作后数据的结构正确，并且最终结果能够清晰地展示出来。

![](img/61cfc64f420d6202eeb037b93a37b7ec_102.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_104.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_106.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_108.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_110.png)

执行完所有参数组合的回测后，我们可以查看`results`表格。表格中会列出每一组参数对应的简单持有收益、策略收益以及策略的超额收益（Outperformance）。

![](img/61cfc64f420d6202eeb037b93a37b7ec_112.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_114.png)

通过分析结果，我们可以发现：
*   有些参数组合下，策略收益可能还不如简单的买入持有策略。
*   大部分参数组合下，策略能带来正向的超额收益。
*   有些组合则表现持平。

![](img/61cfc64f420d6202eeb037b93a37b7ec_116.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_118.png)

这直观地展示了参数选择对策略效果的显著影响。

![](img/61cfc64f420d6202eeb037b93a37b7ec_120.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_122.png)

---

![](img/61cfc64f420d6202eeb037b93a37b7ec_124.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_126.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_128.png)

![](img/61cfc64f420d6202eeb037b93a37b7ec_130.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们通过定义参数空间、使用`itertools.product`进行遍历、并在循环中完成完整的策略回测，最终评估了不同参数组合的表现。这个过程是量化交易中优化策略性能的基础步骤。