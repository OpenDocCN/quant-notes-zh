# Python金融分析与量化交易实战：4.1：金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行基本的统计分析。我们将从导入和查看数据开始，然后学习如何绘制数据走势图，最后进行关键的统计指标计算。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_1.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_3.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_5.png)

## 导入数据与初步查看

![](img/839a07f4fcadfe6af9ecc6a348c4974f_7.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_9.png)

首先，我们需要导入必要的Python工具包，并加载我们的金融时间序列数据。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/839a07f4fcadfe6af9ecc6a348c4974f_11.png)

上一节我们介绍了课程目标，本节中我们来看看如何加载数据。以下是加载数据的关键步骤：

![](img/839a07f4fcadfe6af9ecc6a348c4974f_13.png)

1.  **以时间为索引读取数据**：使用`pandas`的`read_csv`函数，并通过`index_col`参数指定日期列作为数据框的索引。
2.  **转换时间格式**：在读取数据时，使用`parse_dates`参数将日期字符串自动转换为标准的`datetime`格式。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_15.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_17.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_19.png)

```python
# 读取数据，指定第一列为索引，并解析日期格式
data = pd.read_csv(‘data.csv‘, index_col=0, parse_dates=True)
```

![](img/839a07f4fcadfe6af9ecc6a348c4974f_21.png)

执行上述代码后，数据框的索引将变为时间格式，方便后续基于时间的操作。我们可以使用`data.head()`快速查看数据的前几行，确认数据加载正确。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_23.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_25.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_27.png)

## 可视化数据走势

![](img/839a07f4fcadfe6af9ecc6a348c4974f_29.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_31.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_33.png)

成功导入数据后，一个直观的了解数据的方式是将其可视化。我们可以绘制各个金融指标（如不同公司的股价）随时间变化的走势图。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_35.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_37.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_39.png)

pandas内置了便捷的绘图功能。我们可以使用`.plot()`方法快速绘制所有列的走势。为了使不同指标的趋势更清晰，我们可以使用`subplots=True`参数将它们分别绘制在独立的子图中。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_41.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_43.png)

```python
# 绘制所有指标的走势图，使用子图形式展示
data.plot(subplots=True, figsize=(12, 10))
plt.show()
```

![](img/839a07f4fcadfe6af9ecc6a348c4974f_45.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_47.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_49.png)

通过图表，我们可以直观地观察到苹果、微软、亚马逊等公司股价以及其他金融指标在2010年至2018年间的历史变化趋势。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_51.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_53.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_55.png)

## 计算基本统计指标

![](img/839a07f4fcadfe6af9ecc6a348c4974f_57.png)

在初步观察数据后，我们需要进行定量分析，即计算数据的统计特征。这对于理解数据的整体分布和规模至关重要。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_59.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_61.png)

最快速的方法是使用`.describe()`函数，它可以一次性计算出计数、均值、标准差、最小值、四分位数和最大值等关键统计量。为了使结果更简洁，我们可以用`.round()`方法控制显示的小数位数。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_63.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_65.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_67.png)

```python
# 计算并打印所有列的基本统计信息，结果保留两位小数
print(data.describe().round(2))
```

![](img/839a07f4fcadfe6af9ecc6a348c4974f_69.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_71.png)

## 使用聚合函数自定义统计

![](img/839a07f4fcadfe6af9ecc6a348c4974f_73.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_75.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_77.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_79.png)

虽然`.describe()`很方便，但有时我们可能只需要关注特定的几个统计量，或者希望以自定义的方式汇总它们。这时，可以使用`.agg()`聚合函数。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_81.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_83.png)

以下是使用聚合函数计算最小值、最大值、均值、标准差和中位数的示例：

![](img/839a07f4fcadfe6af9ecc6a348c4974f_85.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_87.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_89.png)

```python
# 使用聚合函数计算指定的统计指标
summary = data.agg([‘min‘, ‘max‘, np.mean, np.std, np.median])
print(summary)
```

![](img/839a07f4fcadfe6af9ecc6a348c4974f_91.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_93.png)

通过`.agg()`函数，我们可以自由组合`numpy`或自定义的统计函数，灵活地生成所需的统计摘要。

![](img/839a07f4fcadfe6af9ecc6a348c4974f_95.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_97.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_99.png)

## 课程总结

![](img/839a07f4fcadfe6af9ecc6a348c4974f_101.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_103.png)

![](img/839a07f4fcadfe6af9ecc6a348c4974f_105.png)

本节课中我们一起学习了金融时间序列数据分析的基础操作。我们首先使用`pandas`正确导入了以时间为索引的数据，并利用其绘图功能可视化了多个金融指标的走势。接着，我们掌握了两种计算统计指标的方法：使用`.describe()`快速获取全面概览，以及使用`.agg()`函数自定义聚合计算，从而能够对数据的集中趋势、离散程度和分布范围有一个量化的认识。这些技能是进行更深入金融数据分析与建模的基石。