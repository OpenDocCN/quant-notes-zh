# 量化交易实战课程：P10：均线调参实例

![](img/9340fe6305a450ba06ea8b17b12b7ee0_0.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_2.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_4.png)

## 概述

![](img/9340fe6305a450ba06ea8b17b12b7ee0_6.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置，并对比不同参数下的策略收益。

上一节我们介绍了双均线策略的基本实现，本节中我们来看看如何通过调整参数来优化策略表现。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_8.png)

## 参数调优的必要性

![](img/9340fe6305a450ba06ea8b17b12b7ee0_10.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_12.png)

双均线策略的表现与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。不同的参数组合可能导致截然不同的交易信号和最终收益。因此，我们需要系统地探索不同的参数组合，以找到更优的策略配置。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_14.png)

## 使用 `itertools.product` 遍历参数组合

![](img/9340fe6305a450ba06ea8b17b12b7ee0_16.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_18.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_20.png)

为了高效地遍历所有可能的参数组合，我们将使用Python标准库中的 `itertools.product` 函数。这个函数可以生成多个可迭代对象的笛卡尔积，即所有可能的组合。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_22.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_24.png)

以下是导入该工具的代码：

![](img/9340fe6305a450ba06ea8b17b12b7ee0_26.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_28.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_30.png)

```python
from itertools import product
```

![](img/9340fe6305a450ba06ea8b17b12b7ee0_32.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_34.png)

接下来，我们需要定义短期均线和长期均线的参数空间。例如，我们可以让短期均线在20到60天之间变化，长期均线在180到280天之间变化。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_36.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_38.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_40.png)

以下是定义参数空间的代码：

![](img/9340fe6305a450ba06ea8b17b12b7ee0_42.png)

```python
# 定义短期均线参数空间：从20到60，步长为4
short_window_range = range(20, 61, 4)
# 定义长期均线参数空间：从180到280，步长为10
long_window_range = range(180, 281, 10)
```

![](img/9340fe6305a450ba06ea8b17b12b7ee0_44.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_46.png)

`itertools.product` 会生成这两个范围的所有组合，例如 `(20, 180)`、`(20, 190)`、`(24, 180)` 等。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_48.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_50.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_52.png)

## 构建参数调优循环

![](img/9340fe6305a450ba06ea8b17b12b7ee0_54.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_56.png)

我们将把之前实现的双均线策略逻辑封装到一个循环中，对每一组参数 `(SMA1, SMA2)` 都执行一次完整的策略回测。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_58.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_60.png)

以下是构建循环的框架代码：

![](img/9340fe6305a450ba06ea8b17b12b7ee0_62.png)

```python
# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/9340fe6305a450ba06ea8b17b12b7ee0_64.png)

# 遍历所有参数组合
for sma1, sma2 in product(short_window_range, long_window_range):
    # 在这里执行策略逻辑
    # ...
    # 将本次循环的结果添加到results中
```

![](img/9340fe6305a450ba06ea8b17b12b7ee0_66.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_68.png)

## 完整的策略回测与结果收集

在循环内部，我们需要为每一组参数重新计算均线、生成交易信号并计算策略收益。同时，我们需要将关键结果（如参数值、基准收益、策略收益、超额收益）记录下来。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_70.png)

以下是循环内部的主要步骤：

![](img/9340fe6305a450ba06ea8b17b12b7ee0_72.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_74.png)

1.  **数据准备**：重新加载并清洗股票价格数据。
2.  **计算指标**：根据当前的 `sma1` 和 `sma2` 参数计算短期和长期移动平均线。
3.  **生成信号**：根据双均线交叉规则生成交易仓位信号。
4.  **计算收益**：计算基准收益（持有不动）和策略收益。
5.  **记录结果**：将本次运行的参数和收益结果保存到一个字典或DataFrame行中。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_76.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_78.png)

以下是核心计算步骤的代码示例：

![](img/9340fe6305a450ba06ea8b17b12b7ee0_80.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_82.png)

```python
# 1. 数据准备（假设`raw_data`是包含‘AAPL’列的原始数据）
data = pd.DataFrame(raw_data[‘AAPL’])
data = data.dropna()

![](img/9340fe6305a450ba06ea8b17b12b7ee0_84.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_86.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_88.png)

# 2. 计算指标
data[‘returns’] = np.log(data[‘AAPL’] / data[‘AAPL’].shift(1))
data[‘SMA1’] = data[‘AAPL’].rolling(window=sma1).mean()
data[‘SMA2’] = data[‘AAPL’].rolling(window=sma2).mean()

![](img/9340fe6305a450ba06ea8b17b12b7ee0_90.png)

# 3. 生成信号
data[‘position’] = np.where(data[‘SMA1’] > data[‘SMA2’], 1, -1)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_92.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_94.png)

# 4. 计算收益
data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
data = data.dropna()

![](img/9340fe6305a450ba06ea8b17b12b7ee0_96.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_98.png)

# 5. 计算总收益并记录
total_return = data[[‘returns’, ‘strategy’]].sum()
outperformance = total_return[‘strategy’] - total_return[‘returns’]

![](img/9340fe6305a450ba06ea8b17b12b7ee0_100.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_102.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_104.png)

# 创建一行结果记录
result_row = pd.DataFrame({
    ‘SMA1’: [sma1],
    ‘SMA2’: [sma2],
    ‘Benchmark_Return’: [total_return[‘returns’]],
    ‘Strategy_Return’: [total_return[‘strategy’]],
    ‘Outperformance’: [outperformance]
})

![](img/9340fe6305a450ba06ea8b17b12b7ee0_106.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_108.png)

# 6. 将本次结果追加到总结果表中
results = pd.concat([results, result_row], ignore_index=True)
```

![](img/9340fe6305a450ba06ea8b17b12b7ee0_110.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_112.png)

## 结果分析与解读

运行完所有参数组合后，`results` DataFrame 中将包含每一组参数的策略表现。我们可以通过排序来快速找到表现最好的参数组合。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_114.png)

以下是对结果进行分析的示例：

![](img/9340fe6305a450ba06ea8b17b12b7ee0_116.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_118.png)

```python
# 按超额收益降序排列，找到表现最好的参数
best_results = results.sort_values(by=‘Outperformance’, ascending=False)
print(best_results.head(10))
```

![](img/9340fe6305a450ba06ea8b17b12b7ee0_120.png)

通过观察结果，我们可以发现：
*   某些参数组合下，策略收益可能低于基准收益（`Outperformance` 为负），说明参数选择不当。
*   大部分参数组合下，策略能产生正向的超额收益。
*   通过排序，我们可以识别出在当前历史数据上表现最优的短期和长期均线窗口。

![](img/9340fe6305a450ba06ea8b17b12b7ee0_122.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_124.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_126.png)

## 总结

![](img/9340fe6305a450ba06ea8b17b12b7ee0_128.png)

![](img/9340fe6305a450ba06ea8b17b12b7ee0_130.png)

本节课中我们一起学习了双均线策略的参数调优方法。我们首先理解了参数选择对策略效果的重要性，然后利用 `itertools.product` 工具实现了对参数空间的系统遍历。通过将策略逻辑嵌入循环，我们能够自动测试大量参数组合，并收集其表现结果。最后，通过对结果进行分析和排序，我们可以初步筛选出表现更优的策略参数，这是量化策略开发中优化和验证的关键一步。