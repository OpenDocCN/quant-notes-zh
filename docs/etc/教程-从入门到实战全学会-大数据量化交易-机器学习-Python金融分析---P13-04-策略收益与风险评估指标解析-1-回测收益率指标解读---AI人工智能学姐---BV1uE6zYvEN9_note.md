# Python金融量化+股票交易：P13：04-策略收益与风险评估指标解析-1-回测收益率指标解读 📈

在本节课中，我们将要学习量化交易策略评估中最基础且重要的指标——回测收益率。我们将理解其含义、计算公式，并通过Python代码演示如何计算和可视化它。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_1.png)

## 概述

当我们完成一个交易策略后，需要在历史数据中进行测试，以评估其表现。评估不仅仅是判断策略是盈利还是亏损，更需要量化其具体收益、与市场基准的对比、以及所承担的风险。策略评估指标就是用于完成这些分析的工具。

以下是本节课将要介绍的核心评估指标之一。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_3.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_5.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_7.png)

## 回测收益率

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_9.png)

回测收益率是最基本的策略评估指标。它衡量了在回测周期内，策略相对于初始本金所获得的收益比例。

**公式**如下：
`回测收益率 = (期末资产价值 - 期初资产价值) / 期初资产价值`

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_11.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_13.png)

这个公式可以简化为：
`回测收益率 = (Pt / P0) - 1`
其中，`P0`代表期初资产价值，`Pt`代表期末资产价值。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_15.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_17.png)

它直观地告诉我们，经过一段时间的策略运行，我们的本金增长（或亏损）了多少百分比。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_19.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_21.png)

上一节我们介绍了回测收益率的概念，本节中我们来看看如何用Python代码进行计算和展示。

## 代码实现与解析

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_23.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_25.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体，方便后续图表显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_27.png)

# 读取数据，假设数据文件为‘stock_data.csv’，并将第一列解析为日期索引
data = pd.read_csv('stock_data.csv', index_col=0, parse_dates=True)
# 查看数据前几行
print(data.head())
```

数据加载后，我们假设其中的收盘价列（例如‘Close’）代表了我们策略运行后每日的资产净值。

接下来，我们计算每日资产相对于期初的比值，并最终得到整个回测周期的收益率。

```python
# 计算每日资产净值相对于期初的比值
# 假设我们使用‘Close’列作为每日资产净值
price_series = data['Close']
relative_value = price_series / price_series.iloc[0]

# 可视化资产净值变化曲线
plt.figure(figsize=(10, 6))
relative_value.plot()
plt.title('资产净值相对变化曲线')
plt.xlabel('日期')
plt.ylabel('相对净值 (期初=1)')
plt.grid(True)
plt.show()
```

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_29.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_31.png)

通过上图，我们可以清晰地看到资产净值随时间的变化趋势。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_33.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_35.png)

最后，我们计算整个回测周期的总收益率。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_37.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_39.png)

```python
# 计算总回测收益率：期末净值 / 期初净值 - 1
total_return = (price_series.iloc[-1] / price_series.iloc[0]) - 1

# 将结果整理成DataFrame以便展示
result_df = pd.DataFrame({
    ‘回测收益率’: [total_return]
}, index=[‘策略总收益’])
print(result_df)
```

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_41.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_43.png)

执行以上代码，我们便得到了该策略在回测周期内的总收益率。一个正数代表盈利，负数代表亏损。

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_45.png)

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_47.png)

## 总结

![](img/ddcbed8f60fbe7b2be9f11e98fdd33a0_49.png)

本节课中我们一起学习了量化策略评估的第一个核心指标——回测收益率。我们理解了它的定义是衡量策略相对于初始本金的收益比例，掌握了其计算公式 `(Pt / P0) - 1`，并通过Python代码实践了从数据加载、计算相对净值到得出最终收益率的过程。这个指标是评估策略盈利能力的起点，在后续课程中，我们将结合更多指标进行综合评估。