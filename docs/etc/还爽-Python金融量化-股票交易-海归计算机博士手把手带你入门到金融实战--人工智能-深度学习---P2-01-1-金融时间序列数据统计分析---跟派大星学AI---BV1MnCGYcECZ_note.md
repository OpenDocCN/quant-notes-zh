# 量化交易全教程：P2：01-1-金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行统计分析。我们将从导入和查看数据开始，逐步学习如何计算关键统计指标，并使用图表直观展示数据走势。

![](img/96f5cbd1217fa8565571cc14b72c6818_1.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_3.png)

## 数据导入与初步查看

![](img/96f5cbd1217fa8565571cc14b72c6818_5.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_7.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_9.png)

首先，我们需要导入必要的Python库并加载数据。金融时间序列数据是指随着时间变化而记录的一系列数据点，例如每日股价。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，我们读取数据文件。为了便于后续的时间序列分析，我们需要将“日期”列设置为数据索引，并确保其格式被正确识别。

![](img/96f5cbd1217fa8565571cc14b72c6818_11.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_13.png)

```python
# 读取数据，并将‘Date’列作为索引，同时解析日期格式
df = pd.read_csv('data.csv', index_col='Date', parse_dates=True)
```

![](img/96f5cbd1217fa8565571cc14b72c6818_15.png)

执行上述代码后，数据框`df`的索引将变为日期时间类型，数据内容将按时间顺序排列。我们可以使用`df.head()`查看前几行数据，确认数据已正确加载。

![](img/96f5cbd1217fa8565571cc14b72c6818_17.png)

## 数据可视化

![](img/96f5cbd1217fa8565571cc14b72c6818_19.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_21.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_23.png)

在上一节我们导入了数据，本节中我们来看看如何将数据的走势可视化。图表能帮助我们直观理解不同指标随时间的变化趋势。

![](img/96f5cbd1217fa8565571cc14b72c6818_25.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_27.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_29.png)

以下是绘制所有指标走势图的代码：

![](img/96f5cbd1217fa8565571cc14b72c6818_31.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_33.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_35.png)

```python
# 使用pandas内置的绘图功能，将所有指标绘制在同一张图中
df.plot(figsize=(15, 10))
plt.show()
```

![](img/96f5cbd1217fa8565571cc14b72c6818_37.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_39.png)

如果你想将每个指标分开显示在子图中，可以添加`subplots`参数：

![](img/96f5cbd1217fa8565571cc14b72c6818_41.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_43.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_45.png)

```python
# 将每个指标绘制在独立的子图中
df.plot(subplots=True, figsize=(15, 25))
plt.tight_layout()
plt.show()
```

![](img/96f5cbd1217fa8565571cc14b72c6818_47.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_49.png)

通过图表，我们可以清晰地观察到例如苹果（AAPL）、微软（MSFT）等公司股价以及其他金融指标（如黄金价格、汇率）的历史波动情况。

![](img/96f5cbd1217fa8565571cc14b72c6818_51.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_53.png)

## 基础统计分析

![](img/96f5cbd1217fa8565571cc14b72c6818_55.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_57.png)

在初步查看了数据走势后，接下来我们对数据进行基础的统计分析，以获取数据的整体概貌，例如平均值、波动范围等。

![](img/96f5cbd1217fa8565571cc14b72c6818_59.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_61.png)

最快速的方法是使用`describe()`函数，它可以一次性计算并展示多个关键统计量。

![](img/96f5cbd1217fa8565571cc14b72c6818_63.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_65.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_67.png)

```python
# 计算并展示所有数值列的描述性统计信息，结果保留两位小数
summary_stats = df.describe().round(2)
print(summary_stats)
```

![](img/96f5cbd1217fa8565571cc14b72c6818_69.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_71.png)

`describe()`函数输出的表格包含以下核心统计指标：
*   **count**: 非空值的数量
*   **mean**: 平均值
*   **std**: 标准差
*   **min**: 最小值
*   **25%**: 第一四分位数
*   **50%**: 中位数
*   **75%**: 第三四分位数
*   **max**: 最大值

![](img/96f5cbd1217fa8565571cc14b72c6818_73.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_75.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_77.png)

## 使用聚合函数进行自定义统计

![](img/96f5cbd1217fa8565571cc14b72c6818_79.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_81.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_83.png)

虽然`describe()`函数很方便，但有时我们只需要其中几个特定的统计量。这时，可以使用`agg()`聚合函数来自定义需要计算的指标。

![](img/96f5cbd1217fa8565571cc14b72c6818_85.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_87.png)

以下是使用`agg()`函数汇总特定统计指标的示例：

![](img/96f5cbd1217fa8565571cc14b72c6818_89.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_91.png)

```python
# 自定义需要计算的统计指标：最小值、最大值、均值、标准差和中位数
custom_stats = df.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```

![](img/96f5cbd1217fa8565571cc14b72c6818_93.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_95.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_97.png)

在这段代码中，我们传递了一个包含统计函数名的列表给`agg()`方法。`np.mean`、`np.std`等来自NumPy库的函数可以直接用于计算。这样，我们就得到了一个只包含我们关心的统计指标（最小值、最大值、均值、标准差、中位数）的汇总表格。

![](img/96f5cbd1217fa8565571cc14b72c6818_99.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_101.png)

![](img/96f5cbd1217fa8565571cc14b72c6818_103.png)

本节课中我们一起学习了金融时间序列数据分析的基础流程。我们首先导入数据并将其索引设置为时间，然后通过绘图直观展示了数据走势。接着，我们使用`describe()`函数进行了快速的描述性统计，并掌握了使用`agg()`函数进行自定义指标汇总的方法。这些是进行更深入金融数据分析的重要第一步。