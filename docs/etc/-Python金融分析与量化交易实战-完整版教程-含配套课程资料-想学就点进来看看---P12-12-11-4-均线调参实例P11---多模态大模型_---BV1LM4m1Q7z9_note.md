# Python金融分析与量化交易实战：P12：12.11.4-均线调参实例 📈

![](img/3e5da57da740000d13251015ee522fd8_0.png)

![](img/3e5da57da740000d13251015ee522fd8_2.png)

![](img/3e5da57da740000d13251015ee522fd8_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

![](img/3e5da57da740000d13251015ee522fd8_6.png)

## 参数调优的必要性

![](img/3e5da57da740000d13251015ee522fd8_8.png)

上一节我们介绍了双均线策略的基本原理。本节中我们来看看如何优化这个策略。策略的最终收益与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天合适还是10天合适，长期窗口是20天合适还是40天合适。

![](img/3e5da57da740000d13251015ee522fd8_10.png)

![](img/3e5da57da740000d13251015ee522fd8_12.png)

## 导入迭代工具

![](img/3e5da57da740000d13251015ee522fd8_14.png)

![](img/3e5da57da740000d13251015ee522fd8_16.png)

![](img/3e5da57da740000d13251015ee522fd8_18.png)

为了系统地遍历所有可能的参数组合，我们需要使用一个迭代工具。以下是导入方法：

![](img/3e5da57da740000d13251015ee522fd8_20.png)

![](img/3e5da57da740000d13251015ee522fd8_22.png)

![](img/3e5da57da740000d13251015ee522fd8_24.png)

```python
from itertools import product
```

![](img/3e5da57da740000d13251015ee522fd8_26.png)

![](img/3e5da57da740000d13251015ee522fd8_28.png)

![](img/3e5da57da740000d13251015ee522fd8_30.png)

`product` 函数可以帮助我们生成两个参数空间的所有组合。

![](img/3e5da57da740000d13251015ee522fd8_32.png)

![](img/3e5da57da740000d13251015ee522fd8_34.png)

![](img/3e5da57da740000d13251015ee522fd8_36.png)

## 定义参数空间

![](img/3e5da57da740000d13251015ee522fd8_38.png)

![](img/3e5da57da740000d13251015ee522fd8_40.png)

接下来，我们需要定义短期和长期均线的参数取值范围。

![](img/3e5da57da740000d13251015ee522fd8_42.png)

![](img/3e5da57da740000d13251015ee522fd8_44.png)

```python
# 定义短期均线参数空间，例如从20到60，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线参数空间，例如从180到280，步长为10
sma2_range = range(180, 281, 10)
```

![](img/3e5da57da740000d13251015ee522fd8_46.png)

![](img/3e5da57da740000d13251015ee522fd8_48.png)

![](img/3e5da57da740000d13251015ee522fd8_50.png)

`product` 函数会计算这两个范围的所有组合，例如 (20, 180), (20, 190), (24, 180) 等。这个遍历操作能帮助我们把所有结果全部计算一遍。

![](img/3e5da57da740000d13251015ee522fd8_52.png)

![](img/3e5da57da740000d13251015ee522fd8_54.png)

## 构建参数遍历与策略回测循环

![](img/3e5da57da740000d13251015ee522fd8_56.png)

现在，我们在循环中遍历每一组参数，并计算对应的策略收益。以下是核心代码步骤：

![](img/3e5da57da740000d13251015ee522fd8_58.png)

首先，重新加载并准备数据。

```python
# 加载苹果公司股价数据
data = pd.DataFrame(apple_stock_price)  # 此处应为实际数据加载代码
# 处理缺失值
data = data.dropna()
```

![](img/3e5da57da740000d13251015ee522fd8_60.png)

![](img/3e5da57da740000d13251015ee522fd8_62.png)

然后，在循环中对每一组参数 (sma1, sma2) 执行以下操作：

![](img/3e5da57da740000d13251015ee522fd8_64.png)

![](img/3e5da57da740000d13251015ee522fd8_66.png)

1.  计算收益率、短期均线和长期均线。
2.  根据双均线策略生成交易信号。
3.  计算策略收益并与基准（买入持有）收益对比。

![](img/3e5da57da740000d13251015ee522fd8_68.png)

![](img/3e5da57da740000d13251015ee522fd8_70.png)

以下是循环内的关键代码：

![](img/3e5da57da740000d13251015ee522fd8_72.png)

![](img/3e5da57da740000d13251015ee522fd8_74.png)

![](img/3e5da57da740000d13251015ee522fd8_76.png)

```python
# 计算收益率
data[‘returns’] = np.log(data[‘close’] / data[‘close’].shift(1))
# 计算短期均线
data[‘SMA1’] = data[‘close’].rolling(window=sma1).mean()
# 计算长期均线
data[‘SMA2’] = data[‘close’].rolling(window=sma2).mean()
# 生成交易信号：短期均线上穿长期均线为1，否则为0
data[‘position’] = np.where(data[‘SMA1’] > data[‘SMA2’], 1, 0)
# 计算策略收益率
data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
# 删除计算产生的缺失值
data = data.dropna()
# 计算策略相对于基准的超额收益
out_performance = data[‘strategy’].sum() - data[‘returns’].sum()
```

![](img/3e5da57da740000d13251015ee522fd8_78.png)

![](img/3e5da57da740000d13251015ee522fd8_80.png)

## 收集与展示结果

![](img/3e5da57da740000d13251015ee522fd8_82.png)

![](img/3e5da57da740000d13251015ee522fd8_84.png)

![](img/3e5da57da740000d13251015ee522fd8_86.png)

为了比较不同参数组合的表现，我们需要将每次循环的结果保存下来。以下是收集结果的步骤：

![](img/3e5da57da740000d13251015ee522fd8_88.png)

![](img/3e5da57da740000d13251015ee522fd8_90.png)

![](img/3e5da57da740000d13251015ee522fd8_92.png)

首先，初始化一个空的DataFrame来存储所有结果。

![](img/3e5da57da740000d13251015ee522fd8_94.png)

![](img/3e5da57da740000d13251015ee522fd8_96.png)

```python
results = pd.DataFrame()
```

![](img/3e5da57da740000d13251015ee522fd8_98.png)

在每次循环结束时，将当前参数组合及其对应的收益结果添加到结果表中。

![](img/3e5da57da740000d13251015ee522fd8_100.png)

![](img/3e5da57da740000d13251015ee522fd8_102.png)

```python
# 将本次循环的结果构建为一个单行DataFrame
result_table = pd.DataFrame({
    ‘SMA1’: [sma1],
    ‘SMA2’: [sma2],
    ‘returns’: [data[‘returns’].sum()],
    ‘strategy’: [data[‘strategy’].sum()],
    ‘out’: [out_performance]
})
# 将本次结果追加到总结果表中，并忽略索引以保持整洁
results = pd.concat([results, result_table], ignore_index=True)
```

![](img/3e5da57da740000d13251015ee522fd8_104.png)

循环结束后，我们可以查看 `results` 这个DataFrame。它展示了不同参数组合下，基准收益、策略收益以及策略超额收益的情况。通过分析这个表格，我们可以观察出哪些参数组合可能带来更好的回报，哪些组合可能反而不如简单的买入持有策略。

![](img/3e5da57da740000d13251015ee522fd8_106.png)

![](img/3e5da57da740000d13251015ee522fd8_108.png)

![](img/3e5da57da740000d13251015ee522fd8_110.png)

## 总结

![](img/3e5da57da740000d13251015ee522fd8_112.png)

![](img/3e5da57da740000d13251015ee522fd8_114.png)

![](img/3e5da57da740000d13251015ee522fd8_116.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们通过 `itertools.product` 函数遍历了不同的短期和长期均线参数组合，并在历史数据上对每一组参数进行了策略回测。最后，我们将所有结果汇总成表格进行分析，从而为选择更优的策略参数提供了数据依据。这个过程是量化交易中策略优化的重要一步。