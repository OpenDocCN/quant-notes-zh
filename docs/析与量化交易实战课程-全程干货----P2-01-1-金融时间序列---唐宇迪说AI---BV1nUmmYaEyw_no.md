# Python金融分析与量化交易实战：01-1：金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行统计分析。我们将从导入数据开始，逐步完成数据可视化、基本统计指标计算以及聚合分析等任务，帮助初学者掌握处理时间序列数据的基本方法。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_1.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_3.png)

## 导入数据与设置索引

![](img/94babc60c9e6a122301fdc2bb6eb5eab_5.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_7.png)

首先，我们需要导入必要的Python工具包，并加载我们的金融时间序列数据。数据中包含多个指标，如不同公司的股价、黄金价格、汇率等，这些数据都随时间变化。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_9.png)

以下是导入工具包和数据的代码：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/94babc60c9e6a122301fdc2bb6eb5eab_11.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_13.png)

# 读取数据，并将第一列（日期）设置为索引
df = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

执行上述代码后，数据框`df`的索引将变为标准的日期时间格式，方便后续基于时间的操作。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_15.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_17.png)

## 数据可视化：展示指标走势

![](img/94babc60c9e6a122301fdc2bb6eb5eab_19.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_21.png)

上一节我们成功导入了数据，本节中我们来看看如何将数据可视化，以直观地观察各个指标随时间变化的趋势。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_23.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_25.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_27.png)

以下是绘制各指标走势图的代码。我们可以选择将所有指标画在一张图中进行对比，也可以使用子图将它们分开展示。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_29.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_31.png)

```python
# 方法一：所有指标绘制在同一张图中
df.plot(figsize=(15, 10))
plt.show()

![](img/94babc60c9e6a122301fdc2bb6eb5eab_33.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_35.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_37.png)

# 方法二：使用子图分开展示各个指标
df.plot(subplots=True, figsize=(15, 20))
plt.show()
```

![](img/94babc60c9e6a122301fdc2bb6eb5eab_39.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_41.png)

两种方法各有优势。同一张图便于对比不同指标的趋势关系，而子图则能更清晰地展示每个指标的独立走势。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_43.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_45.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_47.png)

## 数据基本统计分析

![](img/94babc60c9e6a122301fdc2bb6eb5eab_49.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_51.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_53.png)

在完成了数据可视化后，我们需要对数据进行更深入的量化分析。统计分析可以帮助我们理解数据的整体分布和特征。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_55.png)

首先，我们可以使用`describe`方法快速获取数据的基本统计信息，包括样本数、均值、标准差、最小值、最大值和各分位数。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_57.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_59.png)

```python
# 获取数据的基本统计描述，并保留两位小数
basic_stats = df.describe().round(2)
print(basic_stats)
```

![](img/94babc60c9e6a122301fdc2bb6eb5eab_61.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_63.png)

## 使用聚合函数汇总统计指标

![](img/94babc60c9e6a122301fdc2bb6eb5eab_65.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_67.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_69.png)

`describe`方法提供了全面的统计信息，但有时我们可能只关心特定的几个统计量。这时，可以使用聚合函数`agg`来自定义需要计算的指标。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_71.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_73.png)

以下是使用`agg`函数汇总最小值、最大值、均值、标准差和中位数的示例：

![](img/94babc60c9e6a122301fdc2bb6eb5eab_75.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_77.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_79.png)

```python
# 自定义聚合统计指标
custom_stats = df.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```

![](img/94babc60c9e6a122301fdc2bb6eb5eab_81.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_83.png)

通过`agg`函数，我们可以灵活地组合任何我们感兴趣的统计函数，从而得到定制化的分析结果。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_85.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_87.png)

## 总结

![](img/94babc60c9e6a122301fdc2bb6eb5eab_89.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_91.png)

本节课中我们一起学习了金融时间序列数据处理的基本流程。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_93.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_95.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_97.png)

我们首先导入了数据，并将日期列设置为索引，使其成为标准的时间序列格式。接着，我们使用绘图功能直观地展示了各个金融指标的走势。然后，我们通过`describe`方法对数据进行了基本的统计分析。最后，我们介绍了如何使用`agg`聚合函数来自定义和汇总特定的统计指标。

![](img/94babc60c9e6a122301fdc2bb6eb5eab_99.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_101.png)

![](img/94babc60c9e6a122301fdc2bb6eb5eab_103.png)

这些步骤构成了金融时间序列数据分析的基础，为后续更复杂的建模和量化交易策略开发做好了准备。