# Python金融分析与量化交易实战：P13：04-4-均线调参实例 📈

![](img/4135a88e47a9ed6b1cfe7a212848a368_0.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_2.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

---

![](img/4135a88e47a9ed6b1cfe7a212848a368_6.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_8.png)

上一节我们介绍了双均线策略的基本实现与回测。本节中，我们来看看如何通过调整策略参数来优化其表现。

![](img/4135a88e47a9ed6b1cfe7a212848a368_10.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_12.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_14.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_16.png)

策略的最终收益与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天合适还是10天合适，长期窗口是20天合适还是40天合适。

![](img/4135a88e47a9ed6b1cfe7a212848a368_18.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_20.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_22.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_24.png)

以下是实现参数遍历调优的步骤：

![](img/4135a88e47a9ed6b1cfe7a212848a368_26.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_28.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_30.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_32.png)

首先，我们需要导入必要的工具。我们将使用 `itertools` 库中的 `product` 函数来生成所有可能的参数组合。

![](img/4135a88e47a9ed6b1cfe7a212848a368_34.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_36.png)

```python
from itertools import product
```

![](img/4135a88e47a9ed6b1cfe7a212848a368_38.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_40.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_42.png)

接下来，我们定义要测试的参数空间。例如，短期均线窗口从20到60，步长为4；长期均线窗口从180到280，步长为10。

![](img/4135a88e47a9ed6b1cfe7a212848a368_44.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_46.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_48.png)

```python
# 定义参数空间
short_windows = range(20, 61, 4)   # 短期均线窗口：20, 24, 28, ..., 60
long_windows = range(180, 281, 10) # 长期均线窗口：180, 190, 200, ..., 280
```

![](img/4135a88e47a9ed6b1cfe7a212848a368_50.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_52.png)

`product` 函数会生成这两个列表的笛卡尔积，即所有可能的（短期窗口，长期窗口）组合。例如，(20, 180), (20, 190), (24, 180) 等。

然后，我们创建一个循环来遍历每一个参数组合，并对每个组合执行完整的策略回测流程。

![](img/4135a88e47a9ed6b1cfe7a212848a368_54.png)

以下是循环遍历和策略评估的核心代码框架：

![](img/4135a88e47a9ed6b1cfe7a212848a368_56.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_58.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_60.png)

```python
import pandas as pd

![](img/4135a88e47a9ed6b1cfe7a212848a368_62.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_64.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/4135a88e47a9ed6b1cfe7a212848a368_66.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_68.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_70.png)

# 遍历所有参数组合
for sma1, sma2 in product(short_windows, long_windows):
    # 1. 重新加载并预处理数据（确保每次循环数据是干净的）
    data = pd.read_csv(‘your_stock_data.csv‘, index_col=0, parse_dates=True)[‘Close‘]
    data = data.dropna()

    # 2. 计算收益率和均线
    returns = data.pct_change()
    sma1_series = data.rolling(window=sma1).mean()
    sma2_series = data.rolling(window=sma2).mean()

    # 3. 生成交易信号（双均线策略）
    data = pd.DataFrame({‘Price‘: data, ‘SMA1‘: sma1_series, ‘SMA2‘: sma2_series})
    data.dropna(inplace=True)
    data[‘Position‘] = 0
    data.loc[data[‘SMA1‘] > data[‘SMA2‘], ‘Position‘] = 1  # 金叉买入
    data.loc[data[‘SMA1‘] < data[‘SMA2‘], ‘Position‘] = -1 # 死叉卖出或做空

    # 4. 计算策略收益
    data[‘Strategy‘] = data[‘Position‘].shift(1) * returns
    data.dropna(inplace=True)

    # 5. 计算累计收益
    cum_returns = (data[‘Strategy‘] + 1).cumprod().iloc[-1] - 1
    cum_baseline = (returns + 1).cumprod().iloc[-1] - 1  # 基准（买入持有）收益
    outperformance = cum_returns - cum_baseline  # 策略超额收益

    # 6. 将本次结果保存到总表中
    temp_df = pd.DataFrame({
        ‘SMA1‘: [sma1],
        ‘SMA2‘: [sma2],
        ‘Baseline_Return‘: [cum_baseline],
        ‘Strategy_Return‘: [cum_returns],
        ‘Outperformance‘: [outperformance]
    })
    results = pd.concat([results, temp_df], ignore_index=True)
```

![](img/4135a88e47a9ed6b1cfe7a212848a368_72.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_74.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_76.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_78.png)

在代码执行过程中，可能会遇到一些错误，例如数据格式问题或索引错误。一个常见的错误是，从CSV文件读取单列数据后，它可能变成一个Series对象，导致后续操作缺少列名。解决方法是用 `DataFrame` 将其包装起来。

![](img/4135a88e47a9ed6b1cfe7a212848a368_80.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_82.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_84.png)

另一个常见错误是在创建DataFrame时未指定索引。Pandas要求显式指定索引，即使我们不需要特定的索引。我们可以传入一个简单的列表（如 `[0]`）作为索引，然后在合并结果时使用 `ignore_index=True` 来重置索引。

![](img/4135a88e47a9ed6b1cfe7a212848a368_86.png)

执行完成后，我们可以查看 `results` 表格。表格中会列出每一组参数对应的基准收益、策略收益以及策略相对于基准的超额收益（Outperformance）。

![](img/4135a88e47a9ed6b1cfe7a212848a368_88.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_90.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_92.png)

通过分析结果，我们可以发现：
*   有些参数组合下，策略收益可能还不如简单的买入持有策略（Outperformance为负）。
*   大部分参数组合下，策略能产生正向的超额收益。
*   我们需要从中筛选出 **Outperformance** 值较高的参数组合，作为候选的较优策略参数。

![](img/4135a88e47a9ed6b1cfe7a212848a368_94.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_96.png)

---

![](img/4135a88e47a9ed6b1cfe7a212848a368_98.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_100.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_102.png)

![](img/4135a88e47a9ed6b1cfe7a212848a368_104.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们利用 `itertools.product` 函数遍历了不同的短期和长期均线窗口组合，并对每个组合进行了完整的策略回测，最终通过对比超额收益来评估参数优劣。这是量化策略开发中优化模型性能的关键一步。