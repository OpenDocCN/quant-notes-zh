# Python金融分析与量化交易实战教程：P2：1-金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行统计分析。我们将从导入和查看数据开始，逐步学习如何计算关键统计指标，并使用图表直观展示数据走势。

![](img/3235cfc1633452c1aacbc0446f21e456_1.png)

![](img/3235cfc1633452c1aacbc0446f21e456_3.png)

## 数据导入与初步观察

![](img/3235cfc1633452c1aacbc0446f21e456_5.png)

![](img/3235cfc1633452c1aacbc0446f21e456_7.png)

首先，我们需要导入必要的Python库并加载数据。金融时间序列数据是指随着时间变化而记录的数据，例如每日股价、汇率等。

![](img/3235cfc1633452c1aacbc0446f21e456_9.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，我们读取数据文件。关键步骤是指定“日期”列作为数据的索引，并将其转换为标准的时间格式，以便后续进行时间序列分析。

![](img/3235cfc1633452c1aacbc0446f21e456_11.png)

```python
# 读取CSV文件，指定第一列（日期）为索引，并解析日期格式
df = pd.read_csv(‘data.csv‘, index_col=0, parse_dates=True)
```

![](img/3235cfc1633452c1aacbc0446f21e456_13.png)

执行上述代码后，数据框`df`的索引将变为`DatetimeIndex`类型，数据按时间顺序排列。我们可以使用`df.head()`查看数据的前几行，以确认数据加载正确。

![](img/3235cfc1633452c1aacbc0446f21e456_15.png)

![](img/3235cfc1633452c1aacbc0446f21e456_17.png)

## 数据可视化：展示指标走势

![](img/3235cfc1633452c1aacbc0446f21e456_19.png)

上一节我们成功导入了数据，本节中我们来看看如何将数据可视化，以观察各个金融指标随时间变化的趋势。

![](img/3235cfc1633452c1aacbc0446f21e456_21.png)

![](img/3235cfc1633452c1aacbc0446f21e456_23.png)

![](img/3235cfc1633452c1aacbc0446f21e456_25.png)

Pandas内置了便捷的绘图功能。我们可以一次性绘制所有指标的走势图进行对比，也可以将它们分别绘制在子图中以便更清晰地观察。

![](img/3235cfc1633452c1aacbc0446f21e456_27.png)

![](img/3235cfc1633452c1aacbc0446f21e456_29.png)

以下是两种绘图方式的示例代码：

![](img/3235cfc1633452c1aacbc0446f21e456_31.png)

![](img/3235cfc1633452c1aacbc0446f21e456_33.png)

![](img/3235cfc1633452c1aacbc0446f21e456_35.png)

```python
# 方法一：所有指标绘制在同一张图中
df.plot(figsize=(15, 10))
plt.show()

![](img/3235cfc1633452c1aacbc0446f21e456_37.png)

![](img/3235cfc1633452c1aacbc0446f21e456_39.png)

# 方法二：每个指标绘制在独立的子图中
df.plot(subplots=True, figsize=(15, 20))
plt.show()
```

![](img/3235cfc1633452c1aacbc0446f21e456_41.png)

![](img/3235cfc1633452c1aacbc0446f21e456_43.png)

![](img/3235cfc1633452c1aacbc0446f21e456_45.png)

第一种方法将所有曲线放在同一坐标系，便于直接比较不同指标在同一时间点的相对高低。第二种方法为每个指标生成独立的子图，能更清晰地观察每个指标自身的波动细节。您可以根据分析目的选择合适的方式。

![](img/3235cfc1633452c1aacbc0446f21e456_47.png)

![](img/3235cfc1633452c1aacbc0446f21e456_49.png)

## 基础统计分析

![](img/3235cfc1633452c1aacbc0446f21e456_51.png)

![](img/3235cfc1633452c1aacbc0446f21e456_53.png)

在直观了解了数据走势后，我们需要对数据进行量化的统计分析。这能帮助我们掌握数据的整体分布和关键特征。

![](img/3235cfc1633452c1aacbc0446f21e456_55.png)

最快速的方法是使用Pandas的`.describe()`函数，它可以一次性计算出所有数值型列的常用统计量。

![](img/3235cfc1633452c1aacbc0446f21e456_57.png)

![](img/3235cfc1633452c1aacbc0446f21e456_59.png)

```python
# 计算并打印基础统计信息，结果保留两位小数
summary = df.describe().round(2)
print(summary)
```

![](img/3235cfc1633452c1aacbc0446f21e456_61.png)

![](img/3235cfc1633452c1aacbc0446f21e456_63.png)

![](img/3235cfc1633452c1aacbc0446f21e456_65.png)

`.describe()`函数输出的统计量包括：
*   **count**: 非空值的数量
*   **mean**: 平均值
*   **std**: 标准差
*   **min**: 最小值
*   **25%**: 第一四分位数
*   **50%**: 中位数
*   **75%**: 第三四分位数
*   **max**: 最大值

![](img/3235cfc1633452c1aacbc0446f21e456_67.png)

![](img/3235cfc1633452c1aacbc0446f21e456_69.png)

## 使用聚合函数自定义统计

![](img/3235cfc1633452c1aacbc0446f21e456_71.png)

![](img/3235cfc1633452c1aacbc0446f21e456_73.png)

虽然`.describe()`很方便，但有时我们只需要其中几个特定的统计量，或者想使用自定义的聚合函数。这时，可以使用`.agg()`方法。

![](img/3235cfc1633452c1aacbc0446f21e456_75.png)

![](img/3235cfc1633452c1aacbc0446f21e456_77.png)

![](img/3235cfc1633452c1aacbc0446f21e456_79.png)

以下是使用`.agg()`方法汇总最小值、最大值、均值、标准差和中位数的示例：

![](img/3235cfc1633452c1aacbc0446f21e456_81.png)

![](img/3235cfc1633452c1aacbc0446f21e456_83.png)

![](img/3235cfc1633452c1aacbc0446f21e456_85.png)

```python
# 使用agg函数自定义需要计算的统计指标
custom_stats = df.agg([‘min‘, ‘max‘, ‘mean‘, ‘std‘, ‘median‘])
print(custom_stats)
```

![](img/3235cfc1633452c1aacbc0446f21e456_87.png)

通过传递一个包含函数名的列表给`.agg()`，我们可以灵活地组合任何所需的统计计算，例如`np.sum`（求和）、`np.var`（方差）等。

![](img/3235cfc1633452c1aacbc0446f21e456_89.png)

![](img/3235cfc1633452c1aacbc0446f21e456_91.png)

![](img/3235cfc1633452c1aacbc0446f21e456_93.png)

## 课程总结

![](img/3235cfc1633452c1aacbc0446f21e456_95.png)

![](img/3235cfc1633452c1aacbc0446f21e456_97.png)

本节课中我们一起学习了金融时间序列数据分析的基础操作。我们首先导入了数据并将其索引设置为时间格式，然后通过绘图直观展示了多个金融指标的走势。接着，我们使用`.describe()`函数快速获取了数据的整体统计概况，并进一步通过`.agg()`方法实现了自定义的统计指标汇总。

![](img/3235cfc1633452c1aacbc0446f21e456_99.png)

![](img/3235cfc1633452c1aacbc0446f21e456_101.png)

![](img/3235cfc1633452c1aacbc0446f21e456_103.png)

这些技能是进行更深入的金融数据分析，如计算收益率、波动率以及构建量化交易策略的基石。