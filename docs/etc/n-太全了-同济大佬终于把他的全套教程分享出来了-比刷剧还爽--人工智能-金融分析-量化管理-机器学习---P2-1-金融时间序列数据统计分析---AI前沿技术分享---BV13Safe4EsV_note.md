# 金融时间序列分析：1：金融时间序列数据统计分析 📈

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_1.png)

在本节课中，我们将学习如何使用Python对金融时间序列数据进行基本的统计分析。我们将从导入和查看数据开始，然后学习如何绘制数据走势图，最后进行关键的统计指标计算。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_3.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_5.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_7.png)

## 数据导入与初步查看

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_9.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_11.png)

首先，我们需要导入必要的Python工具包，并加载我们的金融时间序列数据。这份数据包含了从2010年到2018年中旬多个金融指标（如不同公司的股价、黄金价格、汇率等）的每日记录。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_13.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_15.png)

以下是导入工具包和数据的代码：

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_17.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_19.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_21.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_23.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_25.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_27.png)

# 读取数据，并将‘Date’列设置为索引，同时解析日期格式
data = pd.read_csv(‘data.csv‘, index_col=‘Date‘, parse_dates=True)
```

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_29.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_31.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_33.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_35.png)

执行上述代码后，我们得到了一个以日期为索引的`DataFrame`。数据的第一列是日期，后续各列是不同的金融指标。我们可以使用`data.head()`来查看数据的前几行，确认数据已正确加载且时间格式为标准格式。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_37.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_39.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_41.png)

## 绘制指标走势图

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_43.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_45.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_47.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_49.png)

上一节我们成功导入了数据，本节中我们来看看如何将多个金融指标的走势可视化。我们的目标是清晰地展示每个指标随时间的变化趋势。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_51.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_53.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_55.png)

以下是绘制各指标走势子图的代码：

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_57.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_59.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_61.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_63.png)

```python
# 使用pandas内置的绘图功能，绘制子图以分别展示各个指标
data.plot(subplots=True, figsize=(12, 16))
plt.show()
```

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_65.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_67.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_69.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_71.png)

这段代码会为数据中的每一列（即每一个金融指标）生成一个独立的子图。通过子图，我们可以更清晰地观察和分析每个指标的独立走势。当然，你也可以选择不使用`subplots=True`参数，将所有指标绘制在同一张图上进行对比，具体方式取决于你的分析需求。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_73.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_75.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_77.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_79.png)

## 计算基本统计指标

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_81.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_83.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_85.png)

在直观地了解了数据走势后，接下来我们需要对数据进行量化的统计分析。这能帮助我们掌握数据的整体分布和特征。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_87.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_89.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_91.png)

以下是计算数据基本统计描述（并保留两位小数）的代码：

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_93.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_95.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_97.png)

```python
# 计算并打印数据的基本统计描述，包括计数、均值、标准差、最小值、四分位数等
print(data.describe().round(2))
```

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_99.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_101.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_103.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_105.png)

`describe()`函数提供了每个指标的样本数、均值、标准差、最小值、最大值以及各四分位数（25%， 50%， 75%）。使用`.round(2)`将结果保留两位小数，使输出更简洁易读。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_107.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_109.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_111.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_113.png)

## 使用聚合函数汇总特定统计量

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_115.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_117.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_119.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_121.png)

除了使用`describe()`获取标准统计摘要，有时我们可能只关心几个特定的统计量，并希望将它们汇总在一起展示。Pandas的聚合功能可以很好地实现这一点。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_123.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_125.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_127.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_129.png)

以下是使用`agg`函数汇总最小值、最大值、均值、标准差和中位数的代码：

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_131.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_133.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_135.png)

```python
# 使用聚合函数，自定义需要计算的统计指标
summary_stats = data.agg([‘min‘, ‘max‘, np.mean, np.std, ‘median‘])
print(summary_stats)
```

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_137.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_139.png)

通过`agg()`函数，我们可以自由指定一个统计函数列表（如`‘min‘`, `‘max‘`, `np.mean`），Pandas会为数据中的每一列计算这些指标，并将结果汇总到一个新的`DataFrame`中。这种方法比`describe()`更具灵活性。

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_141.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_143.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_145.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_147.png)

## 总结

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_149.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_151.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_153.png)

![](img/4cdb4bd98336ca5c780cb2b8e13f5e13_155.png)

本节课中我们一起学习了金融时间序列数据分析的入门操作。我们首先导入了数据并将其时间列设置为索引，然后绘制了各指标的走势图以进行直观观察。接着，我们使用`describe()`函数计算了全面的基本统计指标，并最后通过`agg()`聚合函数练习了如何自定义和汇总特定的统计量。这些是进行更深入金融数据分析的基础步骤。