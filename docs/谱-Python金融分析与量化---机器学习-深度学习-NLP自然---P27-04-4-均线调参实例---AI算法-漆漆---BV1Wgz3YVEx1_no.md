# 金融量化分析：P27：04-4-均线调参实例 📈

![](img/b9e16b1afd23e31f834e44d1e3f4e525_0.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_2.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_4.png)

在本节课中，我们将学习如何通过调整移动平均线的参数来优化双均线交易策略。我们将使用Python的`itertools`工具包来遍历不同的参数组合，并评估每种组合的表现，从而找到更优的策略参数。

---

![](img/b9e16b1afd23e31f834e44d1e3f4e525_6.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_8.png)

上一节我们介绍了双均线策略的基本实现与回测。本节中，我们来看看如何通过调整短期和长期移动平均线的窗口大小来优化策略表现。策略的最终收益与这两个参数的选择密切相关。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_10.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_12.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_14.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_16.png)

## 参数空间定义与遍历

![](img/b9e16b1afd23e31f834e44d1e3f4e525_18.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_20.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_22.png)

为了找到最优的参数组合，我们需要遍历一系列可能的短期和长期移动平均线窗口值。以下是定义参数空间并进行遍历的步骤。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_24.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_26.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_28.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_30.png)

首先，我们需要导入必要的工具。`itertools.product`函数可以帮助我们生成所有可能的参数组合。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_32.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_34.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_36.png)

```python
from itertools import product
```

![](img/b9e16b1afd23e31f834e44d1e3f4e525_38.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_40.png)

接下来，我们定义短期移动平均线（SMA1）和长期移动平均线（SMA2）的参数空间。例如，短期窗口可以从20天到60天，步长为4；长期窗口可以从180天到280天，步长为10。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_42.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_44.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_46.png)

```python
# 定义参数空间
sma1_range = range(20, 61, 4)   # 短期均线窗口：20, 24, 28, ..., 60
sma2_range = range(180, 281, 10) # 长期均线窗口：180, 190, 200, ..., 280
```

![](img/b9e16b1afd23e31f834e44d1e3f4e525_48.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_50.png)

`itertools.product`函数会生成这两个范围的所有组合，例如(20, 180), (20, 190), (24, 180)等。我们将遍历这些组合，对每一组参数都执行一次完整的策略回测。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_52.png)

## 构建回测循环

在循环中，我们将为每一组参数重新计算移动平均线、生成交易信号，并计算策略收益。以下是循环的核心结构。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_54.png)

```python
import pandas as pd

![](img/b9e16b1afd23e31f834e44d1e3f4e525_56.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_58.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_60.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_62.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/b9e16b1afd23e31f834e44d1e3f4e525_64.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_66.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载并预处理数据（例如苹果股价）
    data = pd.DataFrame(your_data['AAPL'])  # 请替换为实际数据加载代码
    data = data.dropna()  # 去除缺失值

    # 2. 计算收益率和移动平均线
    data['returns'] = np.log(data['AAPL'] / data['AAPL'].shift(1))
    data['SMA1'] = data['AAPL'].rolling(window=sma1).mean()
    data['SMA2'] = data['AAPL'].rolling(window=sma2).mean()
    data = data.dropna()

    # 3. 生成交易信号（双均线策略）
    data['position'] = 0
    data.loc[data['SMA1'] > data['SMA2'], 'position'] = 1
    data.loc[data['SMA1'] < data['SMA2'], 'position'] = -1

    # 4. 计算策略收益
    data['strategy'] = data['position'].shift(1) * data['returns']
    data = data.dropna()

    # 5. 计算累计收益并评估
    cum_returns = data[['returns', 'strategy']].cumsum().apply(np.exp)
    final_return = cum_returns.iloc[-1]
    outperformance = final_return['strategy'] - final_return['returns']

    # 6. 将本次结果存储到总表中
    result_dict = {
        'SMA1': sma1,
        'SMA2': sma2,
        'Buy&Hold_Return': final_return['returns'],
        'Strategy_Return': final_return['strategy'],
        'Outperformance': outperformance
    }
    results = results.append(result_dict, ignore_index=True)
```

![](img/b9e16b1afd23e31f834e44d1e3f4e525_68.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_70.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_72.png)

## 结果分析与解读

![](img/b9e16b1afd23e31f834e44d1e3f4e525_74.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_76.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_78.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_80.png)

循环结束后，`results` DataFrame 将包含所有参数组合的回测结果。我们可以通过排序来快速找到表现最佳的策略参数。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_82.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_84.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_86.png)

以下是查看和分析结果的示例：

```python
# 按策略超额收益（Outperformance）降序排列，找到最佳参数
best_results = results.sort_values(by='Outperformance', ascending=False)
print(best_results.head(10))
```

![](img/b9e16b1afd23e31f834e44d1e3f4e525_88.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_90.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_92.png)

通过分析结果表格，我们可以观察到：
*   某些参数组合下，策略收益可能低于简单的买入持有（`Outperformance`为负值）。
*   大部分参数组合能带来正向的超额收益。
*   我们可以从中选择`Outperformance`最高的参数组合，作为当前数据集下的较优策略参数。

![](img/b9e16b1afd23e31f834e44d1e3f4e525_94.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_96.png)

---

![](img/b9e16b1afd23e31f834e44d1e3f4e525_98.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_100.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_102.png)

![](img/b9e16b1afd23e31f834e44d1e3f4e525_104.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们使用`itertools.product`遍历了不同的短期和长期移动平均线窗口组合，并通过回测循环评估了每一组参数的表现。最后，我们通过分析结果表格，学会了如何识别和选择表现更优的策略参数。这个过程是量化策略开发中优化环节的基础实践。