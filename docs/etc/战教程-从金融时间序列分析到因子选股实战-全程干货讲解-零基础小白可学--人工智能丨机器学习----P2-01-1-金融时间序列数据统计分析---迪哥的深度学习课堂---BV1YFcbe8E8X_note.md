# Python金融分析与量化交易实战教程：P2：01-1-金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行统计分析。我们将从导入和查看数据开始，逐步学习如何计算关键统计指标，并使用图表进行可视化展示。

![](img/ad4762bb65e465170bbdae039936edf7_1.png)

## 导入数据与初步查看

![](img/ad4762bb65e465170bbdae039936edf7_3.png)

![](img/ad4762bb65e465170bbdae039936edf7_5.png)

上一节我们介绍了课程的整体目标，本节中我们来看看如何加载和处理数据。

![](img/ad4762bb65e465170bbdae039936edf7_7.png)

![](img/ad4762bb65e465170bbdae039936edf7_9.png)

首先，我们需要导入必要的Python工具包。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

导入工具包后，下一步是加载数据。我们的数据是一个CSV文件，其中包含多个金融指标随时间变化的信息。

![](img/ad4762bb65e465170bbdae039936edf7_11.png)

![](img/ad4762bb65e465170bbdae039936edf7_13.png)

以下是加载数据的具体要求：
*   以时间列为索引。
*   将时间列转换为标准的时间格式。

![](img/ad4762bb65e465170bbdae039936edf7_15.png)

我们可以通过`pandas.read_csv`函数并指定参数来实现这些要求。

![](img/ad4762bb65e465170bbdae039936edf7_17.png)

```python
# 读取数据，指定第一列为索引，并解析日期格式
df = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/ad4762bb65e465170bbdae039936edf7_19.png)

![](img/ad4762bb65e465170bbdae039936edf7_21.png)

![](img/ad4762bb65e465170bbdae039936edf7_23.png)

执行上述代码后，数据框`df`的索引已变为时间格式。我们可以使用`df.head()`查看数据的前几行，以确认数据加载正确。

![](img/ad4762bb65e465170bbdae039936edf7_25.png)

![](img/ad4762bb65e465170bbdae039936edf7_27.png)

```python
print(df.head())
```

![](img/ad4762bb65e465170bbdae039936edf7_29.png)

![](img/ad4762bb65e465170bbdae039936edf7_31.png)

![](img/ad4762bb65e465170bbdae039936edf7_33.png)

## 数据可视化

![](img/ad4762bb65e465170bbdae039936edf7_35.png)

![](img/ad4762bb65e465170bbdae039936edf7_37.png)

在成功导入数据后，我们可以通过图表直观地观察各个金融指标随时间变化的趋势。

![](img/ad4762bb65e465170bbdae039936edf7_39.png)

![](img/ad4762bb65e465170bbdae039936edf7_41.png)

Pandas内置了便捷的绘图功能。我们可以使用`df.plot()`方法快速绘制所有指标的走势图。

![](img/ad4762bb65e465170bbdae039936edf7_43.png)

![](img/ad4762bb65e465170bbdae039936edf7_45.png)

![](img/ad4762bb65e465170bbdae039936edf7_47.png)

```python
# 绘制所有指标的走势图，设置图形大小
df.plot(figsize=(15, 10))
plt.show()
```

![](img/ad4762bb65e465170bbdae039936edf7_49.png)

![](img/ad4762bb65e465170bbdae039936edf7_51.png)

如果希望将每个指标单独绘制在子图中以便更清晰地观察，可以添加`subplots`参数。

![](img/ad4762bb65e465170bbdae039936edf7_53.png)

```python
# 以子图形式绘制各个指标的走势
df.plot(subplots=True, figsize=(15, 20))
plt.show()
```

![](img/ad4762bb65e465170bbdae039936edf7_55.png)

![](img/ad4762bb65e465170bbdae039936edf7_57.png)

## 基础统计分析

![](img/ad4762bb65e465170bbdae039936edf7_59.png)

![](img/ad4762bb65e465170bbdae039936edf7_61.png)

上一节我们展示了数据的走势，本节中我们来看看如何计算数据的统计特征。

![](img/ad4762bb65e465170bbdae039936edf7_63.png)

![](img/ad4762bb65e465170bbdae039936edf7_65.png)

拿到数据后，最直接的统计分析是查看数据的基本信息，例如样本数量、数据类型和缺失值情况。

![](img/ad4762bb65e465170bbdae039936edf7_67.png)

![](img/ad4762bb65e465170bbdae039936edf7_69.png)

```python
# 查看数据框的基本信息
print(df.info())
```

![](img/ad4762bb65e465170bbdae039936edf7_71.png)

![](img/ad4762bb65e465170bbdae039936edf7_73.png)

![](img/ad4762bb65e465170bbdae039936edf7_75.png)

`df.describe()`方法可以快速生成数据各列的核心统计指标，包括计数、均值、标准差、最小值、四分位数和最大值。

![](img/ad4762bb65e465170bbdae039936edf7_77.png)

![](img/ad4762bb65e465170bbdae039936edf7_79.png)

```python
# 生成描述性统计摘要，并保留两位小数
stat_summary = df.describe().round(2)
print(stat_summary)
```

![](img/ad4762bb65e465170bbdae039936edf7_81.png)

![](img/ad4762bb65e465170bbdae039936edf7_83.png)

![](img/ad4762bb65e465170bbdae039936edf7_85.png)

## 使用聚合函数汇总统计指标

![](img/ad4762bb65e465170bbdae039936edf7_87.png)

除了使用`describe()`，我们还可以使用聚合函数`agg()`来自定义需要计算的统计指标，并将它们汇总在一起。

![](img/ad4762bb65e465170bbdae039936edf7_89.png)

![](img/ad4762bb65e465170bbdae039936edf7_91.png)

![](img/ad4762bb65e465170bbdae039936edf7_93.png)

以下是使用`agg`函数计算指定统计指标的示例：

![](img/ad4762bb65e465170bbdae039936edf7_95.png)

![](img/ad4762bb65e465170bbdae039936edf7_97.png)

```python
# 自定义聚合统计：最小值、最大值、均值、标准差、中位数
custom_stats = df.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```

![](img/ad4762bb65e465170bbdae039936edf7_99.png)

![](img/ad4762bb65e465170bbdae039936edf7_101.png)

![](img/ad4762bb65e465170bbdae039936edf7_103.png)

本节课中我们一起学习了金融时间序列数据分析的基础操作。我们掌握了如何导入并以时间为索引处理数据，使用绘图功能可视化指标走势，以及通过`describe()`和`agg()`方法计算关键统计指标。这些技能是进行更深入金融数据分析的基石。