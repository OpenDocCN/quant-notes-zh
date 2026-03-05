# Python金融分析与量化交易实战：P12：4-均线调参实例 📈

![](img/9b7d14229c8e56fac0729739052f8e1c_0.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_2.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能带来更好收益的参数设置。

上一节我们介绍了双均线策略的基本实现和回测方法。本节中，我们来看看如何系统地测试不同参数组合对策略表现的影响。

## 参数调优的必要性

![](img/9b7d14229c8e56fac0729739052f8e1c_6.png)

策略的最终结果与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。短期窗口是5天合适还是10天合适？长期窗口是20天合适还是40天合适？我们需要通过计算不同参数组合的表现来寻找答案。

![](img/9b7d14229c8e56fac0729739052f8e1c_8.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_10.png)

在Python中，我们可以借助工具轻松实现这种参数遍历。

![](img/9b7d14229c8e56fac0729739052f8e1c_12.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_14.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_16.png)

## 实现步骤

![](img/9b7d14229c8e56fac0729739052f8e1c_18.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_20.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_22.png)

以下是实现参数调优的核心步骤。

![](img/9b7d14229c8e56fac0729739052f8e1c_24.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_26.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_28.png)

### 1. 导入迭代工具

![](img/9b7d14229c8e56fac0729739052f8e1c_30.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_32.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_34.png)

首先，我们需要导入用于生成参数组合的迭代工具 `product`。

![](img/9b7d14229c8e56fac0729739052f8e1c_36.png)

```python
from itertools import product
```

![](img/9b7d14229c8e56fac0729739052f8e1c_38.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_40.png)

`product` 函数可以帮助我们生成两个参数空间的所有可能组合。

![](img/9b7d14229c8e56fac0729739052f8e1c_42.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_44.png)

### 2. 定义参数空间

![](img/9b7d14229c8e56fac0729739052f8e1c_46.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_48.png)

接下来，我们定义短期均线和长期均线的参数取值范围。

![](img/9b7d14229c8e56fac0729739052f8e1c_50.png)

```python
# 定义短期均线参数空间，例如从20到60，步长为4
short_params = range(20, 61, 4)
# 定义长期均线参数空间，例如从180到280，步长为10
long_params = range(180, 281, 10)
```

### 3. 遍历参数组合并计算策略表现

我们将使用一个循环来遍历所有参数组合，并对每个组合执行完整的策略计算流程。

![](img/9b7d14229c8e56fac0729739052f8e1c_52.png)

```python
import pandas as pd

![](img/9b7d14229c8e56fac0729739052f8e1c_54.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_56.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/9b7d14229c8e56fac0729739052f8e1c_58.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_60.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_62.png)

# 遍历所有参数组合
for SMA1, SMA2 in product(short_params, long_params):
    # 1. 重新加载数据（确保每次循环数据是干净的）
    data = pd.read_csv(‘your_stock_data.csv‘, index_col=‘Date‘, parse_dates=True)[‘AAPL‘]  # 以苹果股价为例
    data = pd.DataFrame(data)  # 确保是DataFrame格式

    # 2. 处理缺失值
    data = data.dropna()

    # 3. 计算收益率和均线
    data[‘returns‘] = np.log(data / data.shift(1))
    data[‘SMA1‘] = data[‘AAPL‘].rolling(SMA1).mean()
    data[‘SMA2‘] = data[‘AAPL‘].rolling(SMA2).mean()

    # 4. 再次处理因计算产生的缺失值
    data = data.dropna()

    # 5. 生成交易信号（双均线策略）
    data[‘position‘] = np.where(data[‘SMA1‘] > data[‘SMA2‘], 1, -1)

    # 6. 计算策略收益率
    data[‘strategy‘] = data[‘position‘].shift(1) * data[‘returns‘]

    # 7. 再次处理缺失值并计算累计收益率
    data = data.dropna()
    data[‘cumulative_returns‘] = data[‘returns‘].cumsum().apply(np.exp)
    data[‘cumulative_strategy‘] = data[‘strategy‘].cumsum().apply(np.exp)

    # 8. 计算策略相对于简单持有的超额收益（最终净值）
    final_return = data[‘cumulative_strategy‘].iloc[-1]
    final_buy_hold = data[‘cumulative_returns‘].iloc[-1]
    outperformance = final_return - final_buy_hold

    # 9. 将本次参数组合的结果存储到表格中
    result_dict = {
        ‘SMA1‘: SMA1,
        ‘SMA2‘: SMA2,
        ‘Buy&Hold_Final‘: final_buy_hold,
        ‘Strategy_Final‘: final_return,
        ‘Outperformance‘: outperformance
    }
    # 将本次结果追加到总结果表中
    results = results.append(result_dict, ignore_index=True)
```

![](img/9b7d14229c8e56fac0729739052f8e1c_64.png)

### 4. 结果分析与解读

![](img/9b7d14229c8e56fac0729739052f8e1c_66.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_68.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_70.png)

执行完循环后，我们可以查看 `results` 这个DataFrame。

![](img/9b7d14229c8e56fac0729739052f8e1c_72.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_74.png)

```python
# 查看结果的前几行
print(results.head(10))

![](img/9b7d14229c8e56fac0729739052f8e1c_76.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_78.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_80.png)

# 可以按‘Outperformance‘（超额收益）进行排序，找到表现最好的参数组合
best_params = results.sort_values(by=‘Outperformance‘, ascending=False).iloc[0]
print(“最佳参数组合：“, best_params)
```

![](img/9b7d14229c8e56fac0729739052f8e1c_82.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_84.png)

在结果中，每一行代表一组参数（SMA1， SMA2）的回测表现。`Outperformance` 列表示策略最终净值相对于简单买入持有（Buy & Hold）的超额收益。如果该值为正，说明策略有效；如果为负，则说明该参数组合下的策略表现还不如不做任何操作。

## 核心概念与代码要点

![](img/9b7d14229c8e56fac0729739052f8e1c_86.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_88.png)

*   **参数空间**：指需要测试的参数（如SMA1， SMA2）所有可能取值的集合。
*   **网格搜索**：本例中使用 `product` 遍历所有参数组合的方法就是一种简单的网格搜索（Grid Search）。
*   **策略评估指标**：我们使用策略最终净值与基准（买入持有）最终净值的差值作为评估指标。在实际中，也可能使用夏普比率、最大回撤等更复杂的指标。

![](img/9b7d14229c8e56fac0729739052f8e1c_90.png)

## 总结

![](img/9b7d14229c8e56fac0729739052f8e1c_92.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_94.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们通过以下步骤实现：
1.  使用 `itertools.product` 生成所有待测试的参数组合。
2.  对每一组参数，重新执行完整的数据加载、指标计算、信号生成和回测流程。
3.  计算并记录每组参数下策略的最终表现（特别是相对于基准的超额收益）。
4.  通过分析结果表格，寻找表现最优的参数组合。

![](img/9b7d14229c8e56fac0729739052f8e1c_96.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_98.png)

![](img/9b7d14229c8e56fac0729739052f8e1c_100.png)

这个过程是量化策略开发中至关重要的一步，它帮助我们从主观猜测转向基于数据的客观参数选择。需要注意的是，在历史数据上表现最优的参数，在未来不一定仍然有效，因此在实际应用中还需结合其他方法（如交叉验证）来防范过拟合风险。