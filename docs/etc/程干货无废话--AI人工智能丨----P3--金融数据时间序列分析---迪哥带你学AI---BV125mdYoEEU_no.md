# Python金融分析与量化交易实战：P3：金融数据时间序列分析 01-1-金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行基本的统计分析。我们将从导入和查看数据开始，然后学习如何绘制数据走势图，最后进行关键的统计指标计算。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_1.png)

## 数据导入与初步查看

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_3.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_5.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_7.png)

首先，我们需要导入必要的Python工具包，并加载我们的金融时间序列数据。这份数据包含了从2010年到2018年中旬多个金融指标的每日记录，例如苹果、微软、亚马逊等公司的股价，以及黄金价格、汇率等其他指标。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_9.png)

以下是导入工具包和数据的代码：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_11.png)

# 读取数据，并将‘Date’列设置为索引，同时解析日期格式
df = pd.read_csv(‘data.csv‘, index_col=‘Date‘, parse_dates=True)
```

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_13.png)

执行上述代码后，我们得到了一个以日期为索引的`DataFrame`。数据中可能包含一些缺失值，这在金融数据中是常见现象。我们可以使用`df.head()`来查看数据的前几行，以确认数据已正确加载。



![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_15.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_17.png)

## 绘制数据走势图

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_19.png)

上一节我们成功导入了数据，本节中我们来看看如何将多个金融指标的走势可视化。这对于直观理解数据随时间的变化趋势至关重要。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_21.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_23.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_25.png)

以下是绘制所有指标走势图的两种方法。第一种方法是将所有指标绘制在同一张图中，便于整体对比。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_27.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_29.png)

```python
# 方法一：所有指标绘制在同一张图中
df.plot(figsize=(15, 8))
plt.show()
```

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_31.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_33.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_35.png)

第二种方法是使用子图，将每个指标单独绘制在一个小图中，这样可以更清晰地观察每个指标的细节。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_37.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_39.png)

```python
# 方法二：使用子图分别展示各个指标
df.plot(subplots=True, figsize=(15, 10))
plt.tight_layout()
plt.show()
```

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_41.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_43.png)

两种方法各有优劣，你可以根据分析需求选择使用。子图方式能更清晰地展示每个指标的独立走势。



![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_45.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_47.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_49.png)

## 基础统计分析

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_51.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_53.png)

在初步观察了数据走势后，我们需要对数据进行更精确的量化分析。基础统计分析可以帮助我们快速了解数据的整体分布和关键特征。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_55.png)

以下是使用`describe()`方法快速获取数据概览的代码。该方法会计算每个数值列的非空值数量、均值、标准差、最小值、四分位数和最大值。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_57.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_59.png)

```python
# 获取数据的基本统计描述，并保留两位小数
summary = df.describe().round(2)
print(summary)
```

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_61.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_63.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_65.png)

通过这个汇总表，我们可以快速发现数据的范围、中心趋势和离散程度。例如，可以比较不同公司股价的平均水平或波动性大小。



![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_67.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_69.png)

## 使用聚合函数自定义统计

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_71.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_73.png)

虽然`describe()`方法非常方便，但有时我们可能只需要关注特定的几个统计量。这时，可以使用`agg()`聚合函数来自定义需要计算的指标。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_75.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_77.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_79.png)

以下是使用`agg()`函数同时计算最小值、最大值、均值、标准差和中位数的示例：

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_81.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_83.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_85.png)

```python
# 使用聚合函数计算自定义的统计指标
custom_stats = df.agg([‘min‘, ‘max‘, np.mean, np.std, np.median])
print(custom_stats)
```

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_87.png)

通过这种方式，我们可以灵活地组合任何我们关心的统计函数，例如`np.var`（方差）、`np.percentile`（百分位数）等，从而得到一份量身定制的数据统计报告。



![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_89.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_91.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_93.png)

## 课程总结

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_95.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_97.png)

本节课中我们一起学习了金融时间序列数据分析的入门操作。我们首先导入了数据并将其时间列设置为索引，然后学习了两种可视化数据走势的方法。接着，我们使用`describe()`函数对数据进行了快速的统计分析，最后通过`agg()`聚合函数实现了自定义统计指标的计算。

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_99.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_101.png)

![](img/1ed0043ffc1a7c722842a6f7b0b7d1b7_103.png)

这些步骤构成了金融数据分析的基础，掌握了它们，你就能够对一份新的时间序列数据进行有效的初步探索和洞察。