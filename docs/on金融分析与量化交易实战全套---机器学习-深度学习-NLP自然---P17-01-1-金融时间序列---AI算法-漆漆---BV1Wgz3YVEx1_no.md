# 金融时间序列分析：P17：01-1-金融时间序列数据统计分析 📊

在本节课中，我们将学习如何使用Python对金融时间序列数据进行基本的统计分析。我们将从导入和查看数据开始，然后进行数据可视化，最后计算关键的统计指标。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_1.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_3.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_5.png)

## 导入数据与初步查看

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_7.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_9.png)

首先，我们需要导入必要的Python库并加载数据。数据包含了从2010年到2018年中旬多个金融指标（如股价、汇率、黄金价格等）的每日记录。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_11.png)

# 读取数据，并将‘Date’列设置为索引，同时解析日期格式
df = pd.read_csv('data.csv', index_col='Date', parse_dates=True)
```

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_13.png)

执行上述代码后，我们得到了一个以日期为索引的`DataFrame`。数据的第一列是日期，后续各列是不同的金融指标。即使你不熟悉某些指标的缩写（如‘AAPL’代表苹果股价），这也不影响后续的分析，我们将以几个股价指标为例进行演示。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_15.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_17.png)

为了更清晰地查看数据，我们可以使用`.head()`方法。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_19.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_21.png)

```python
print(df.head())
```

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_23.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_25.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_27.png)

## 可视化各指标走势 📈

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_29.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_31.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_33.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_35.png)

上一节我们导入了数据，本节中我们来看看如何将多个金融指标的走势可视化。这有助于我们直观地理解数据随时间的变化趋势。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_37.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_39.png)

以下是绘制所有指标走势图的两种方法：

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_41.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_43.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_45.png)

**方法一：所有指标绘制在同一张图中（便于对比）**
```python
df.plot(figsize=(15, 8))
plt.show()
```

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_47.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_49.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_51.png)

**方法二：每个指标绘制在独立的子图中（更清晰）**
```python
df.plot(subplots=True, figsize=(15, 10))
plt.show()
```

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_53.png)

你可以根据分析需求选择合适的方法。同一张图适合进行横向对比，而子图则能更清晰地展示每个指标的独立走势。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_55.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_57.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_59.png)

## 数据统计分析 🔍

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_61.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_63.png)

在初步查看了数据走势后，接下来我们需要对数据进行更精确的量化分析。统计分析能帮助我们理解数据的集中趋势、离散程度和分布形态。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_65.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_67.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_69.png)

以下是计算数据基本统计信息的步骤：

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_71.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_73.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_75.png)

**1. 使用`.describe()`获取概括性统计**
此方法会一次性计算并展示每个指标的样本数、均值、标准差、最小值、四分位数和最大值。
```python
# 计算基本统计量，并保留两位小数
summary_stats = df.describe().round(2)
print(summary_stats)
```

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_77.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_79.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_81.png)

**2. 使用聚合函数`.agg()`自定义统计指标**
如果你只需要特定的几个统计量，或者想使用自定义的函数，`.agg()`方法非常灵活。
```python
# 自定义需要计算的统计指标：最小值、最大值、均值、标准差、中位数
custom_stats = df.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```
通过这种方式，你可以自由组合任何你关心的统计指标，而不必受限于`.describe()`的默认输出。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_83.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_85.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_87.png)

## 总结

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_89.png)

本节课中我们一起学习了金融时间序列数据分析的基础流程。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_91.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_93.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_95.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_97.png)

1.  **数据导入与查看**：我们使用`pandas`库读取CSV文件，并将日期列设置为索引，这是处理时间序列数据的关键第一步。
2.  **数据可视化**：我们利用`DataFrame`内置的`.plot()`方法，快速绘制了所有指标的走势图，并学会了在同一图表和子图两种模式间进行选择。
3.  **统计分析**：我们掌握了两种计算统计指标的方法：使用`.describe()`快速获取全面概览，以及使用`.agg()`函数灵活地汇总自定义的统计量（如最小值、最大值、均值等）。

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_99.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_101.png)

![](img/cb72b4207bbf1bb60c5c68ab5ebe790b_103.png)

这些操作构成了金融数据分析的基石，后续更复杂的模型构建和预测都依赖于这些基础的数据处理和洞察。