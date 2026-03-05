# Python金融分析与量化交易实战课程：1：金融数据时间序列分析 📈

在本节课中，我们将学习如何使用Python进行金融时间序列分析。我们将从导入和查看数据开始，逐步学习如何对数据进行可视化、基本统计和聚合分析。课程内容设计简单直白，适合初学者上手。

![](img/b338caae80e39f6dadfeeb032d7690f7_1.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_3.png)

## 数据导入与查看

![](img/b338caae80e39f6dadfeeb032d7690f7_5.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_7.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_9.png)

首先，我们需要导入必要的Python工具包，并加载我们的金融时间序列数据。时间序列数据是指随着时间变化而记录的数据点集合，例如每日股价。

以下是导入工具包和数据的代码：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/b338caae80e39f6dadfeeb032d7690f7_11.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_13.png)

# 读取数据，并将‘Date’列设置为索引，同时解析日期格式
data = pd.read_csv(‘data.csv‘, index_col=‘Date‘, parse_dates=True)
```

![](img/b338caae80e39f6dadfeeb032d7690f7_15.png)

执行上述代码后，数据被加载到`DataFrame`中，日期列`Date`成为索引。我们可以使用`data.head()`查看数据的前几行，确认数据已正确加载，格式为标准的时间序列。

![](img/b338caae80e39f6dadfeeb032d7690f7_17.png)

## 数据可视化

![](img/b338caae80e39f6dadfeeb032d7690f7_19.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_21.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_23.png)

上一节我们成功导入了数据，本节中我们来看看如何将数据可视化，以观察各个金融指标随时间变化的走势。

![](img/b338caae80e39f6dadfeeb032d7690f7_25.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_27.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_29.png)

以下是绘制所有指标走势图的代码。我们可以选择将所有曲线画在同一张图中进行对比，也可以使用子图将它们分开展示。

![](img/b338caae80e39f6dadfeeb032d7690f7_31.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_33.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_35.png)

```python
# 方法一：所有指标绘制在同一张图中
data.plot(figsize=(12, 8))
plt.show()

![](img/b338caae80e39f6dadfeeb032d7690f7_37.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_39.png)

# 方法二：使用子图分开展示各个指标
data.plot(subplots=True, figsize=(12, 10))
plt.show()
```

![](img/b338caae80e39f6dadfeeb032d7690f7_41.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_43.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_45.png)

两种方法各有优势。同一张图便于对比不同指标的趋势关系，而子图则能更清晰地展示每个指标的独立走势。数据中包含了苹果、微软、亚马逊等公司的股价，以及其他如黄金价格、汇率等金融指标。

![](img/b338caae80e39f6dadfeeb032d7690f7_47.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_49.png)

## 基本统计分析

![](img/b338caae80e39f6dadfeeb032d7690f7_51.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_53.png)

在完成数据可视化后，我们需要对数据进行一些基本的统计分析，以了解数据的整体情况，例如数据的范围、中心趋势和离散程度。

![](img/b338caae80e39f6dadfeeb032d7690f7_55.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_57.png)

以下是使用`describe`方法快速获取数据概览的代码：

![](img/b338caae80e39f6dadfeeb032d7690f7_59.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_61.png)

```python
# 获取数据的基本统计信息，并保留两位小数
summary = data.describe().round(2)
print(summary)
```

![](img/b338caae80e39f6dadfeeb032d7690f7_63.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_65.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_67.png)

`describe()`方法会输出每个数值列的非空值数量、均值、标准差、最小值、各分位数和最大值。通过`.round(2)`，我们将结果保留两位小数，使输出更加清晰易读。

![](img/b338caae80e39f6dadfeeb032d7690f7_69.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_71.png)

## 使用聚合函数汇总统计指标

![](img/b338caae80e39f6dadfeeb032d7690f7_73.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_75.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_77.png)

除了使用`describe`，我们还可以使用聚合函数`agg()`来自定义需要计算的统计指标，并将它们汇总在一起。这提供了更大的灵活性。

![](img/b338caae80e39f6dadfeeb032d7690f7_79.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_81.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_83.png)

以下是使用`agg`函数计算最小值、最大值、均值、标准差和中位数的代码：

![](img/b338caae80e39f6dadfeeb032d7690f7_85.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_87.png)

```python
# 使用聚合函数计算指定的统计指标
aggregated_stats = data.agg([‘min‘, ‘max‘, np.mean, np.std, np.median])
print(aggregated_stats)
```

![](img/b338caae80e39f6dadfeeb032d7690f7_89.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_91.png)

通过`agg()`函数，我们可以自由组合`numpy`或`pandas`提供的任何统计函数。例如，这里我们计算了`min`（最小值）、`max`（最大值）、`np.mean`（均值）、`np.std`（标准差）和`np.median`（中位数）。这种方法允许我们只关注感兴趣的统计量。

![](img/b338caae80e39f6dadfeeb032d7690f7_93.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_95.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_97.png)

## 总结

![](img/b338caae80e39f6dadfeeb032d7690f7_99.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_101.png)

![](img/b338caae80e39f6dadfeeb032d7690f7_103.png)

本节课中我们一起学习了金融时间序列分析的基础操作。我们从导入和设置时间索引数据开始，然后学习了如何用图表直观展示多个金融指标的走势。接着，我们使用`describe()`方法对数据进行了快速的统计分析，并进一步通过`agg()`聚合函数实现了自定义指标的汇总计算。这些是进行更深入金融数据分析的重要基础步骤。