# 量化交易实战：P15：均线调参实例 📊

![](img/0458465dc06911c1f5deec12a3ea635c_0.png)

![](img/0458465dc06911c1f5deec12a3ea635c_2.png)

![](img/0458465dc06911c1f5deec12a3ea635c_4.png)

![](img/0458465dc06911c1f5deec12a3ea635c_6.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的策略表现。

![](img/0458465dc06911c1f5deec12a3ea635c_8.png)

---

![](img/0458465dc06911c1f5deec12a3ea635c_10.png)

![](img/0458465dc06911c1f5deec12a3ea635c_12.png)

上一节我们介绍了双均线策略的基本原理和回测方法。本节中，我们来看看如何通过调整策略参数来优化其表现。

![](img/0458465dc06911c1f5deec12a3ea635c_14.png)

![](img/0458465dc06911c1f5deec12a3ea635c_16.png)

## 参数调优的必要性

![](img/0458465dc06911c1f5deec12a3ea635c_18.png)

![](img/0458465dc06911c1f5deec12a3ea635c_20.png)

![](img/0458465dc06911c1f5deec12a3ea635c_22.png)

![](img/0458465dc06911c1f5deec12a3ea635c_24.png)

策略的最终表现与许多因素有关，其中均线的参数（短期窗口和长期窗口）是关键。我们需要探索不同的参数组合，例如短期窗口是5天还是10天更合适，长期窗口是20天还是40天更合适。

![](img/0458465dc06911c1f5deec12a3ea635c_26.png)

![](img/0458465dc06911c1f5deec12a3ea635c_28.png)

![](img/0458465dc06911c1f5deec12a3ea635c_30.png)

![](img/0458465dc06911c1f5deec12a3ea635c_32.png)

在Python中，我们可以借助工具轻松实现这种参数遍历。

![](img/0458465dc06911c1f5deec12a3ea635c_34.png)

![](img/0458465dc06911c1f5deec12a3ea635c_36.png)

![](img/0458465dc06911c1f5deec12a3ea635c_38.png)

## 导入迭代工具

![](img/0458465dc06911c1f5deec12a3ea635c_40.png)

![](img/0458465dc06911c1f5deec12a3ea635c_42.png)

![](img/0458465dc06911c1f5deec12a3ea635c_44.png)

![](img/0458465dc06911c1f5deec12a3ea635c_46.png)

首先，我们需要导入用于生成参数组合的迭代工具。我们将使用 `itertools` 库中的 `product` 函数。

![](img/0458465dc06911c1f5deec12a3ea635c_48.png)

![](img/0458465dc06911c1f5deec12a3ea635c_50.png)

```python
from itertools import product
```

![](img/0458465dc06911c1f5deec12a3ea635c_52.png)

![](img/0458465dc06911c1f5deec12a3ea635c_54.png)

![](img/0458465dc06911c1f5deec12a3ea635c_56.png)

![](img/0458465dc06911c1f5deec12a3ea635c_58.png)

`product` 函数可以帮助我们生成两个或多个序列的笛卡尔积，即所有可能的参数组合。

![](img/0458465dc06911c1f5deec12a3ea635c_60.png)

![](img/0458465dc06911c1f5deec12a3ea635c_62.png)

![](img/0458465dc06911c1f5deec12a3ea635c_64.png)

## 定义参数空间

![](img/0458465dc06911c1f5deec12a3ea635c_66.png)

接下来，我们需要定义短期均线（SMA1）和长期均线（SMA2）的参数空间。我们可以使用 `range` 函数来指定一个数值范围。

![](img/0458465dc06911c1f5deec12a3ea635c_68.png)

```python
# 定义短期均线参数空间，例如从20到60，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线参数空间，例如从180到280，步长为10
sma2_range = range(180, 281, 10)
```

![](img/0458465dc06911c1f5deec12a3ea635c_70.png)

参数空间的意思是，`product` 函数会帮我们计算出所有可能的组合，例如 (20, 180), (20, 190), (24, 180), (24, 190) 等等。

![](img/0458465dc06911c1f5deec12a3ea635c_72.png)

![](img/0458465dc06911c1f5deec12a3ea635c_74.png)

![](img/0458465dc06911c1f5deec12a3ea635c_76.png)

## 遍历参数并计算策略表现

![](img/0458465dc06911c1f5deec12a3ea635c_78.png)

![](img/0458465dc06911c1f5deec12a3ea635c_80.png)

![](img/0458465dc06911c1f5deec12a3ea635c_82.png)

以下是遍历所有参数组合并计算对应策略表现的核心步骤。我们将把之前构建策略的代码整合到一个循环中。

![](img/0458465dc06911c1f5deec12a3ea635c_84.png)

![](img/0458465dc06911c1f5deec12a3ea635c_86.png)

![](img/0458465dc06911c1f5deec12a3ea635c_88.png)

```python
import pandas as pd

![](img/0458465dc06911c1f5deec12a3ea635c_90.png)

![](img/0458465dc06911c1f5deec12a3ea635c_92.png)

![](img/0458465dc06911c1f5deec12a3ea635c_94.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/0458465dc06911c1f5deec12a3ea635c_96.png)

![](img/0458465dc06911c1f5deec12a3ea635c_98.png)

![](img/0458465dc06911c1f5deec12a3ea635c_100.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载数据（确保每次循环数据是干净的）
    data = pd.DataFrame(your_data['AAPL'])  # 请替换‘your_data’和‘AAPL’为你的实际数据
    data = data.dropna()  # 去除缺失值

    # 2. 计算收益率
    data['returns'] = np.log(data['AAPL'] / data['AAPL'].shift(1))

    # 3. 计算短期和长期移动平均线
    data['SMA1'] = data['AAPL'].rolling(window=sma1).mean()
    data['SMA2'] = data['AAPL'].rolling(window=sma2).mean()

    # 4. 去除因计算移动平均线产生的缺失值
    data = data.dropna()

    # 5. 生成交易信号（双均线策略）
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)

    # 6. 计算策略收益率
    data['strategy'] = data['position'].shift(1) * data['returns']

    # 7. 去除因移位产生的缺失值并计算累计收益率
    data = data.dropna()
    data['cumulative_returns'] = data['returns'].cumsum().apply(np.exp)
    data['cumulative_strategy'] = data['strategy'].cumsum().apply(np.exp)

    # 8. 计算策略相对于简单持有的超额收益（最终值）
    final_return = data['cumulative_strategy'].iloc[-1]
    final_buy_hold = data['cumulative_returns'].iloc[-1]
    outperformance = final_return - final_buy_hold

    # 9. 将本次循环的结果存储为一个临时DataFrame
    temp_df = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Final_Buy_Hold_Return': [final_buy_hold],
        'Final_Strategy_Return': [final_return],
        'Outperformance': [outperformance]
    })

    # 10. 将本次结果追加到总结果表中
    results = pd.concat([results, temp_df], ignore_index=True)
```

![](img/0458465dc06911c1f5deec12a3ea635c_102.png)

![](img/0458465dc06911c1f5deec12a3ea635c_104.png)

![](img/0458465dc06911c1f5deec12a3ea635c_106.png)

![](img/0458465dc06911c1f5deec12a3ea635c_108.png)

## 代码解析与错误处理

![](img/0458465dc06911c1f5deec12a3ea635c_110.png)

![](img/0458465dc06911c1f5deec12a3ea635c_112.png)

![](img/0458465dc06911c1f5deec12a3ea635c_114.png)

在编写上述代码时，可能会遇到一些错误。以下是两个常见问题及其解决方法：

1.  **数据列名丢失**：当我们从原始数据中选取一列（如 `your_data['AAPL']`）时，如果操作不当，可能会得到一个Series而非DataFrame，导致后续操作缺少列名。解决方法是用 `pd.DataFrame()` 将其显式转换为DataFrame。
2.  **构建DataFrame时的索引问题**：使用 `pd.DataFrame()` 创建单行数据时，必须指定 `index` 参数。我们可以简单地设置为 `[0]`。在后续使用 `pd.concat` 合并时，则使用 `ignore_index=True` 来重置索引。

![](img/0458465dc06911c1f5deec12a3ea635c_116.png)

![](img/0458465dc06911c1f5deec12a3ea635c_118.png)

![](img/0458465dc06911c1f5deec12a3ea635c_120.png)

运行代码后，我们可以查看 `results` 表格。其中 `Outperformance` 列显示了策略收益超过简单买入持有收益的部分。通过这个表格，我们可以：
*   找出表现最好的参数组合（`Outperformance` 值最高的行）。
*   观察不同参数下策略的表现差异。
*   理解并非所有参数组合都能战胜市场，参数选择至关重要。

![](img/0458465dc06911c1f5deec12a3ea635c_122.png)

![](img/0458465dc06911c1f5deec12a3ea635c_124.png)

![](img/0458465dc06911c1f5deec12a3ea635c_126.png)

---

![](img/0458465dc06911c1f5deec12a3ea635c_128.png)

![](img/0458465dc06911c1f5deec12a3ea635c_130.png)

![](img/0458465dc06911c1f5deec12a3ea635c_132.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用 `itertools.product` 函数遍历了不同的短期和长期均线参数组合，并通过回测计算了每个组合的策略表现。最后，我们将所有结果汇总成表格进行分析，从而为选择最优策略参数提供了数据依据。这是量化策略开发中优化环节的重要一步。