# Python金融分析+量化交易：P15：15.均线调参实例 📈

![](img/62f376cfe05aa10b670bede6196210eb_0.png)

![](img/62f376cfe05aa10b670bede6196210eb_2.png)

![](img/62f376cfe05aa10b670bede6196210eb_4.png)

![](img/62f376cfe05aa10b670bede6196210eb_6.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找能带来更好收益的参数设置。

![](img/62f376cfe05aa10b670bede6196210eb_8.png)

---

![](img/62f376cfe05aa10b670bede6196210eb_10.png)

![](img/62f376cfe05aa10b670bede6196210eb_12.png)

![](img/62f376cfe05aa10b670bede6196210eb_14.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中我们来看看如何通过调整参数来优化这个策略。策略的收益结果与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最合适的参数组合。

![](img/62f376cfe05aa10b670bede6196210eb_16.png)

![](img/62f376cfe05aa10b670bede6196210eb_18.png)

首先，我们需要导入一个用于生成参数组合的工具。

![](img/62f376cfe05aa10b670bede6196210eb_20.png)

![](img/62f376cfe05aa10b670bede6196210eb_22.png)

![](img/62f376cfe05aa10b670bede6196210eb_24.png)

![](img/62f376cfe05aa10b670bede6196210eb_26.png)

```python
from itertools import product
```

![](img/62f376cfe05aa10b670bede6196210eb_28.png)

![](img/62f376cfe05aa10b670bede6196210eb_30.png)

![](img/62f376cfe05aa10b670bede6196210eb_32.png)

![](img/62f376cfe05aa10b670bede6196210eb_34.png)

`product` 函数的作用是生成多个可迭代对象的笛卡尔积。简单来说，它能帮我们列出所有可能的参数组合。

![](img/62f376cfe05aa10b670bede6196210eb_36.png)

![](img/62f376cfe05aa10b670bede6196210eb_38.png)

![](img/62f376cfe05aa10b670bede6196210eb_40.png)

![](img/62f376cfe05aa10b670bede6196210eb_42.png)

以下是定义参数空间并进行遍历的步骤。

![](img/62f376cfe05aa10b670bede6196210eb_44.png)

![](img/62f376cfe05aa10b670bede6196210eb_46.png)

我们需要分别指定短期均线和长期均线的参数取值范围。然后使用 `product` 函数生成所有组合，并对每一种组合计算策略收益。

![](img/62f376cfe05aa10b670bede6196210eb_48.png)

![](img/62f376cfe05aa10b670bede6196210eb_50.png)

![](img/62f376cfe05aa10b670bede6196210eb_52.png)

```python
# 定义参数空间
# 短期均线参数范围：20到60，步长为4
sma1_range = range(20, 61, 4)
# 长期均线参数范围：180到280，步长为10
sma2_range = range(180, 281, 10)

![](img/62f376cfe05aa10b670bede6196210eb_54.png)

![](img/62f376cfe05aa10b670bede6196210eb_56.png)

![](img/62f376cfe05aa10b670bede6196210eb_58.png)

# 使用product生成所有参数组合
parameters = product(sma1_range, sma2_range)
```

![](img/62f376cfe05aa10b670bede6196210eb_60.png)

接下来，我们开始遍历每一个参数组合，并计算对应的策略表现。为了确保数据准确，我们重新加载苹果公司的股价数据。

```python
import pandas as pd

![](img/62f376cfe05aa10b670bede6196210eb_62.png)

![](img/62f376cfe05aa10b670bede6196210eb_64.png)

# 重新加载数据
data = pd.read_csv(‘你的数据文件路径‘)  # 请替换为实际文件路径
# 假设我们只使用‘Adj Close‘列
price = data[‘Adj Close‘]
```

![](img/62f376cfe05aa10b670bede6196210eb_66.png)

![](img/62f376cfe05aa10b670bede6196210eb_68.png)

![](img/62f376cfe05aa10b670bede6196210eb_70.png)

拿到数据后，第一步是处理缺失值。

![](img/62f376cfe05aa10b670bede6196210eb_72.png)

![](img/62f376cfe05aa10b670bede6196210eb_74.png)

![](img/62f376cfe05aa10b670bede6196210eb_76.png)

![](img/62f376cfe05aa10b670bede6196210eb_78.png)

```python
# 去除缺失值
price = price.dropna()
```

![](img/62f376cfe05aa10b670bede6196210eb_80.png)

![](img/62f376cfe05aa10b670bede6196210eb_82.png)

然后，在循环中为每一组参数计算收益率、均线值和交易信号。

![](img/62f376cfe05aa10b670bede6196210eb_84.png)

![](img/62f376cfe05aa10b670bede6196210eb_86.png)

![](img/62f376cfe05aa10b670bede6196210eb_88.png)

![](img/62f376cfe05aa10b670bede6196210eb_90.png)

```python
results_list = []  # 用于存储所有结果的列表

![](img/62f376cfe05aa10b670bede6196210eb_92.png)

![](img/62f376cfe05aa10b670bede6196210eb_94.png)

![](img/62f376cfe05aa10b670bede6196210eb_96.png)

![](img/62f376cfe05aa10b670bede6196210eb_98.png)

# 遍历所有参数组合
for sma1, sma2 in parameters:
    # 计算收益率
    returns = price.pct_change()
    # 计算短期均线
    sma1_values = price.rolling(window=sma1).mean()
    # 计算长期均线
    sma2_values = price.rolling(window=sma2).mean()
    
    # 去除计算产生的缺失值
    returns = returns.dropna()
    sma1_values = sma1_values.dropna()
    sma2_values = sma2_values.dropna()
    
    # 生成交易信号：短期均线上穿长期均线为1，否则为0
    # 需要确保数据长度对齐
    min_len = min(len(returns), len(sma1_values), len(sma2_values))
    returns = returns.iloc[-min_len:]
    sma1_values = sma1_values.iloc[-min_len:]
    sma2_values = sma2_values.iloc[-min_len:]
    
    position = pd.Series(0, index=returns.index)
    position[sma1_values > sma2_values] = 1
    
    # 计算策略收益率：持仓信号 * 下一期收益率（这里简化为当期）
    strategy_returns = position.shift(1) * returns
    strategy_returns = strategy_returns.dropna()
    
    # 计算基准收益率（买入并持有）和策略超额收益
    # 需要对齐数据长度
    common_index = returns.index.intersection(strategy_returns.index)
    benchmark = returns.loc[common_index].cumsum().iloc[-1]
    strategy = strategy_returns.loc[common_index].cumsum().iloc[-1]
    outperformance = strategy - benchmark
    
    # 将结果保存为字典
    result_dict = {
        ‘SMA1‘: sma1,
        ‘SMA2‘: sma2,
        ‘Benchmark_Return‘: benchmark,
        ‘Strategy_Return‘: strategy,
        ‘Outperformance‘: outperformance
    }
    results_list.append(result_dict)
```

![](img/62f376cfe05aa10b670bede6196210eb_100.png)

![](img/62f376cfe05aa10b670bede6196210eb_102.png)

![](img/62f376cfe05aa10b670bede6196210eb_104.png)

遍历完成后，我们将所有结果整理成一个清晰的表格，便于分析。

![](img/62f376cfe05aa10b670bede6196210eb_106.png)

```python
# 将结果列表转换为DataFrame
results_df = pd.DataFrame(results_list)
# 重置索引
results_df = results_df.reset_index(drop=True)
# 展示前10行结果
print(results_df.head(10))
```

![](img/62f376cfe05aa10b670bede6196210eb_108.png)

![](img/62f376cfe05aa10b670bede6196210eb_110.png)

运行代码后，我们可以观察结果表格。`Outperformance` 列显示了策略收益超越基准收益的程度。通过分析这些数据，我们可以发现：
*   某些参数组合下，策略收益可能不如简单的买入持有策略。
*   大部分参数组合能带来正向的超额收益。
*   我们需要从中筛选出 `Outperformance` 值较高的参数组合，作为更优的策略参数。

![](img/62f376cfe05aa10b670bede6196210eb_112.png)

![](img/62f376cfe05aa10b670bede6196210eb_114.png)

![](img/62f376cfe05aa10b670bede6196210eb_116.png)

---

![](img/62f376cfe05aa10b670bede6196210eb_118.png)

![](img/62f376cfe05aa10b670bede6196210eb_120.png)

![](img/62f376cfe05aa10b670bede6196210eb_122.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们使用 `itertools.product` 工具遍历了不同的短期和长期均线参数组合，计算了每种组合下的策略表现，并通过结果分析找到了表现更优的参数。这展示了在Python中借助现有工具包，系统化进行策略参数优化的完整流程。