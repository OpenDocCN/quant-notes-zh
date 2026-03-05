# 量化交易入门：P15：4-均线调参实例 📈

![](img/60f62b7d42ac4fed921d21456b628e78_0.png)

![](img/60f62b7d42ac4fed921d21456b628e78_2.png)

![](img/60f62b7d42ac4fed921d21456b628e78_4.png)

在本节课中，我们将学习如何通过调整双均线策略的参数来优化交易策略。我们将使用Python的`itertools`工具遍历不同的参数组合，并评估每种组合的表现，从而找到更优的参数设置。

---

![](img/60f62b7d42ac4fed921d21456b628e78_6.png)

上一节我们介绍了双均线策略的基本实现，本节中我们来看看如何通过参数调优来提升策略效果。策略的表现与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最合适的参数组合。

![](img/60f62b7d42ac4fed921d21456b628e78_8.png)

## 参数空间定义与遍历

![](img/60f62b7d42ac4fed921d21456b628e78_10.png)

![](img/60f62b7d42ac4fed921d21456b628e78_12.png)

![](img/60f62b7d42ac4fed921d21456b628e78_14.png)

为了找到最优的短期和长期均线窗口，我们需要测试一系列参数组合。以下是定义参数空间并使用`itertools.product`进行遍历的步骤。

![](img/60f62b7d42ac4fed921d21456b628e78_16.png)

![](img/60f62b7d42ac4fed921d21456b628e78_18.png)

![](img/60f62b7d42ac4fed921d21456b628e78_20.png)

首先，导入必要的工具。

![](img/60f62b7d42ac4fed921d21456b628e78_22.png)

![](img/60f62b7d42ac4fed921d21456b628e78_24.png)

![](img/60f62b7d42ac4fed921d21456b628e78_26.png)

```python
from itertools import product
```

![](img/60f62b7d42ac4fed921d21456b628e78_28.png)

![](img/60f62b7d42ac4fed921d21456b628e78_30.png)

![](img/60f62b7d42ac4fed921d21456b628e78_32.png)

接下来，定义短期均线（SMA1）和长期均线（SMA2）的参数测试范围。

![](img/60f62b7d42ac4fed921d21456b628e78_34.png)

```python
# 定义短期均线参数空间，例如从20天到60天，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线参数空间，例如从180天到280天，步长为10
sma2_range = range(180, 281, 10)
```

![](img/60f62b7d42ac4fed921d21456b628e78_36.png)

![](img/60f62b7d42ac4fed921d21456b628e78_38.png)

`itertools.product`函数可以生成两个参数范围的所有可能组合。

![](img/60f62b7d42ac4fed921d21456b628e78_40.png)

![](img/60f62b7d42ac4fed921d21456b628e78_42.png)

```python
# 生成所有参数组合
param_combinations = product(sma1_range, sma2_range)
```

![](img/60f62b7d42ac4fed921d21456b628e78_44.png)

以下是遍历过程的示例组合：
*   20天短期均线与180天长期均线
*   20天短期均线与190天长期均线
*   24天短期均线与180天长期均线
*   24天短期均线与190天长期均线

![](img/60f62b7d42ac4fed921d21456b628e78_46.png)

## 策略评估循环

定义了参数空间后，我们需要在一个循环中对每一组参数执行完整的策略计算，并记录结果。

首先，重新加载并准备数据。

![](img/60f62b7d42ac4fed921d21456b628e78_48.png)

```python
import pandas as pd

![](img/60f62b7d42ac4fed921d21456b628e78_50.png)

![](img/60f62b7d42ac4fed921d21456b628e78_52.png)

![](img/60f62b7d42ac4fed921d21456b628e78_54.png)

# 重新加载苹果股价数据
data = pd.read_csv('你的数据文件路径.csv', index_col=0, parse_dates=True)['Adj Close']
data = data.dropna()  # 去除缺失值
```

![](img/60f62b7d42ac4fed921d21456b628e78_56.png)

![](img/60f62b7d42ac4fed921d21456b628e78_58.png)

然后，开始遍历每一组参数，计算策略收益。

![](img/60f62b7d42ac4fed921d21456b628e78_60.png)

![](img/60f62b7d42ac4fed921d21456b628e78_62.png)

```python
results_list = []  # 用于存储所有结果的列表

![](img/60f62b7d42ac4fed921d21456b628e78_64.png)

![](img/60f62b7d42ac4fed921d21456b628e78_66.png)

![](img/60f62b7d42ac4fed921d21456b628e78_68.png)

for sma1, sma2 in param_combinations:
    # 计算收益率
    returns = data.pct_change().dropna()
    
    # 计算短期和长期移动平均线
    sma1_series = data.rolling(window=sma1).mean().dropna()
    sma2_series = data.rolling(window=sma2).mean().dropna()
    
    # 生成交易信号：短期均线上穿长期均线为1，否则为0
    position = (sma1_series > sma2_series).astype(int)
    # 将信号与未来一期的收益率对齐，计算策略收益
    strategy_returns = position.shift(1) * returns
    strategy_returns = strategy_returns.dropna()
    
    # 计算策略超额收益（策略收益 - 基准收益）
    excess_return = strategy_returns.sum() - returns.loc[strategy_returns.index].sum()
    
    # 将当前参数组合及结果保存到字典中
    result_dict = {
        'SMA1': sma1,
        'SMA2': sma2,
        '基准收益': returns.loc[strategy_returns.index].sum(),
        '策略收益': strategy_returns.sum(),
        '超额收益': excess_return
    }
    results_list.append(result_dict)
```

![](img/60f62b7d42ac4fed921d21456b628e78_70.png)

![](img/60f62b7d42ac4fed921d21456b628e78_72.png)

## 结果分析与展示

![](img/60f62b7d42ac4fed921d21456b628e78_74.png)

![](img/60f62b7d42ac4fed921d21456b628e78_76.png)

计算完成后，我们将所有结果汇总到一个`DataFrame`中，以便进行分析和比较。

![](img/60f62b7d42ac4fed921d21456b628e78_78.png)

```python
# 将所有结果转换为DataFrame
results_df = pd.DataFrame(results_list)
# 重置索引，使其更整洁
results_df = results_df.reset_index(drop=True)

![](img/60f62b7d42ac4fed921d21456b628e78_80.png)

# 查看结果的前10行
print(results_df.head(10))
```

![](img/60f62b7d42ac4fed921d21456b628e78_82.png)

![](img/60f62b7d42ac4fed921d21456b628e78_84.png)

输出结果将展示不同参数组合下的基准收益、策略收益和超额收益。通过观察`超额收益`列，我们可以判断哪些参数组合使策略表现优于简单持有（基准），哪些组合反而更差。

![](img/60f62b7d42ac4fed921d21456b628e78_86.png)

![](img/60f62b7d42ac4fed921d21456b628e78_88.png)

---

![](img/60f62b7d42ac4fed921d21456b628e78_90.png)

![](img/60f62b7d42ac4fed921d21456b628e78_92.png)

![](img/60f62b7d42ac4fed921d21456b628e78_94.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们通过定义参数空间、遍历所有组合并计算策略表现，最终能够量化评估不同参数的效果，从而为选择更优的策略参数提供了数据支持。这是量化策略开发中优化环节的重要一步。