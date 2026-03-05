# Python金融量化+股票交易：P2：01-1-金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行统计分析。我们将从导入和查看数据开始，逐步学习如何计算基本的统计指标，并使用图表展示数据走势。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_1.png)

## 导入数据与初步观察

![](img/bdb163dc7ab11b81f9909067cf2dbab3_3.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_5.png)

首先，我们需要导入必要的Python库并加载数据。金融时间序列数据是指随着时间变化而记录的数据，例如每日股价。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_7.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_9.png)

以下是导入工具包和数据的代码：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 读取数据，并将‘Date’列设置为索引，同时解析日期格式
data = pd.read_csv('data.csv', index_col='Date', parse_dates=True)
```

![](img/bdb163dc7ab11b81f9909067cf2dbab3_11.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_13.png)

执行上述代码后，我们得到的数据框`data`将以日期作为索引，并且日期格式已被标准化。我们可以使用`data.head()`查看数据的前几行，以确认数据加载正确。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_15.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_17.png)

数据中包含多个列，例如苹果（AAPL）、微软（MSFT）、亚马逊（AMZN）等公司的股价，以及其他金融指标如黄金价格和汇率。对于初学者，我们主要关注前几列股价数据即可。

## 可视化数据走势

![](img/bdb163dc7ab11b81f9909067cf2dbab3_19.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_21.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_23.png)

在初步了解数据后，一个直观的方法是将其可视化。我们可以绘制各个指标随时间变化的走势图。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_25.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_27.png)

以下是绘制走势图的代码：

![](img/bdb163dc7ab11b81f9909067cf2dbab3_29.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_31.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_33.png)

```python
# 使用pandas内置的绘图功能，绘制所有列的子图
data.plot(subplots=True, figsize=(10, 12))
plt.show()
```

![](img/bdb163dc7ab11b81f9909067cf2dbab3_35.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_37.png)

这段代码会为数据中的每一列生成一个独立的子图，清晰地展示每个金融指标的历史走势。你可以选择将所有曲线画在同一张图上进行对比，也可以像上面这样分开展示，具体取决于分析需求。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_39.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_41.png)

## 计算基本统计指标

![](img/bdb163dc7ab11b81f9909067cf2dbab3_43.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_45.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_47.png)

上一节我们介绍了如何可视化数据，本节中我们来看看如何用数值描述数据的整体情况。基本的统计分析可以帮助我们理解数据的中心趋势和离散程度。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_49.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_51.png)

对于DataFrame，最快捷的方法是使用`.describe()`函数。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_53.png)

以下是计算并格式化统计结果的代码：

![](img/bdb163dc7ab11b81f9909067cf2dbab3_55.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_57.png)

```python
# 计算所有数值列的基本统计信息，并保留两位小数
summary_stats = data.describe().round(2)
print(summary_stats)
```

![](img/bdb163dc7ab11b81f9909067cf2dbab3_59.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_61.png)

`.describe()`方法会自动计算计数、均值、标准差、最小值、四分位数和最大值。通过`.round(2)`，我们将结果保留两位小数，使输出更清晰易读。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_63.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_65.png)

## 使用聚合函数自定义统计

![](img/bdb163dc7ab11b81f9909067cf2dbab3_67.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_69.png)

虽然`.describe()`很方便，但有时我们只需要特定的几个统计量。这时，可以使用聚合函数`agg()`来自定义需要汇总的指标。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_71.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_73.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_75.png)

以下是使用`agg()`函数汇总特定统计指标的代码：

![](img/bdb163dc7ab11b81f9909067cf2dbab3_77.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_79.png)

```python
# 自定义需要计算的统计指标：最小值、最大值、均值、标准差和中位数
custom_stats = data.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```

![](img/bdb163dc7ab11b81f9909067cf2dbab3_81.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_83.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_85.png)

通过这个操作，我们可以灵活地选择关心的统计量（如`min`, `max`, `np.mean`, `np.std`, `np.median`）并将它们汇总在一张表中，这比`.describe()`的输出更具针对性。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_87.png)

## 总结

![](img/bdb163dc7ab11b81f9909067cf2dbab3_89.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_91.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_93.png)

本节课中我们一起学习了金融时间序列数据分析的基础操作。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_95.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_97.png)

我们首先导入了数据，并将日期列设置为索引。接着，我们通过绘制走势图直观地观察了各个金融指标的变化趋势。然后，我们使用`.describe()`方法快速计算了数据集的基本统计信息。最后，我们学习了如何使用`agg()`聚合函数来自定义和汇总特定的统计指标，如均值、标准差等。

![](img/bdb163dc7ab11b81f9909067cf2dbab3_99.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_101.png)

![](img/bdb163dc7ab11b81f9909067cf2dbab3_103.png)

这些技能是进行更深入金融数据分析的基石，熟练掌握它们将为后续学习量化交易策略打下坚实基础。