# Python金融分析与量化交易实战：2.1.1：金融时间序列数据统计分析 📈

![](img/3b67d7eb452d1a94a6c248e244fab107_0.png)

在本节课中，我们将学习如何使用Python对金融时间序列数据进行基本的统计分析。我们将从导入数据开始，逐步完成数据可视化、描述性统计以及聚合计算等任务。

![](img/3b67d7eb452d1a94a6c248e244fab107_2.png)

## 数据导入与初步观察

![](img/3b67d7eb452d1a94a6c248e244fab107_4.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_6.png)

首先，我们需要导入必要的工具包，并加载我们的金融时间序列数据。这份数据包含了从2010年到2018年中旬多个金融指标（如股价、汇率等）的每日记录。

![](img/3b67d7eb452d1a94a6c248e244fab107_8.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_10.png)

以下是导入工具包和数据的代码：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/3b67d7eb452d1a94a6c248e244fab107_12.png)

# 读取数据，指定第一列（日期）为索引，并解析日期格式
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/3b67d7eb452d1a94a6c248e244fab107_14.png)

执行上述代码后，数据框的索引已转换为标准的时间戳格式。我们可以使用 `data.head()` 来查看数据的前几行，确认数据已正确加载，并且日期列已成为索引。

![](img/3b67d7eb452d1a94a6c248e244fab107_16.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_18.png)

## 数据可视化：绘制指标走势图

![](img/3b67d7eb452d1a94a6c248e244fab107_20.png)

上一节我们成功导入了数据，本节中我们来看看如何将多个金融指标的走势可视化。这有助于我们直观地理解数据随时间变化的趋势。

![](img/3b67d7eb452d1a94a6c248e244fab107_22.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_24.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_26.png)

以下是绘制走势图的两种方法：

![](img/3b67d7eb452d1a94a6c248e244fab107_28.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_30.png)

*   **方法一：所有指标绘制在同一张图中**。这种方法便于进行整体趋势的对比。
    ```python
    data.plot(figsize=(12, 8))
    plt.show()
    ```

![](img/3b67d7eb452d1a94a6c248e244fab107_32.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_34.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_36.png)

*   **方法二：每个指标绘制在独立的子图中**。这种方法可以更清晰地观察每个指标的细节。
    ```python
    data.plot(subplots=True, figsize=(12, 10))
    plt.show()
    ```

![](img/3b67d7eb452d1a94a6c248e244fab107_38.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_40.png)

两种方法各有优势，你可以根据分析需求选择使用。图中展示了包括苹果、微软、亚马逊等公司的股价在内的多个金融指标走势。

![](img/3b67d7eb452d1a94a6c248e244fab107_42.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_44.png)

## 基本统计分析

![](img/3b67d7eb452d1a94a6c248e244fab107_46.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_48.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_50.png)

在完成了数据可视化后，接下来我们对数据进行基础的统计分析，以获取数据的整体概貌，例如样本数量、数据范围以及是否存在缺失值等。

![](img/3b67d7eb452d1a94a6c248e244fab107_52.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_54.png)

我们可以使用 `data.info()` 来快速查看数据的基本信息，包括行数、列数、数据类型以及非空值数量。从输出可知，我们的数据集包含2216个样本，时间范围从2010年1月1日到2018年6月29日，大部分指标存在少量缺失值。

![](img/3b67d7eb452d1a94a6c248e244fab107_56.png)

## 计算描述性统计指标

![](img/3b67d7eb452d1a94a6c248e244fab107_58.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_60.png)

为了更精确地了解数据的分布情况，我们需要计算一系列描述性统计指标，如均值、标准差、最小值、最大值等。

![](img/3b67d7eb452d1a94a6c248e244fab107_62.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_64.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_66.png)

最直接的方法是使用Pandas内置的 `describe()` 函数。为了结果更清晰，我们可以限制输出结果的小数位数。

![](img/3b67d7eb452d1a94a6c248e244fab107_68.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_70.png)

```python
# 计算描述性统计，并保留两位小数
summary_stats = data.describe().round(2)
print(summary_stats)
```

![](img/3b67d7eb452d1a94a6c248e244fab107_72.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_74.png)

执行上述代码后，我们将得到一个表格，其中包含了每个指标的计数（count）、均值（mean）、标准差（std）、最小值（min）、各分位数（25%， 50%， 75%）以及最大值（max）。

![](img/3b67d7eb452d1a94a6c248e244fab107_76.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_78.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_80.png)

## 使用聚合函数自定义统计汇总

![](img/3b67d7eb452d1a94a6c248e244fab107_82.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_84.png)

上一节我们使用 `describe()` 得到了全面的统计摘要。但有时我们可能只需要关注特定的几个统计量。本节中我们来看看如何使用聚合函数 `agg()` 来自定义需要汇总的统计指标。

![](img/3b67d7eb452d1a94a6c248e244fab107_86.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_88.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_90.png)

以下是使用 `agg()` 函数汇总最小值、最大值、均值、标准差和中位数的示例：

![](img/3b67d7eb452d1a94a6c248e244fab107_92.png)

```python
# 自定义聚合统计：最小值、最大值、均值、标准差、中位数
custom_stats = data.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```

![](img/3b67d7eb452d1a94a6c248e244fab107_94.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_96.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_98.png)

通过这种方式，我们可以灵活地选择并组合任何我们关心的统计函数，将结果汇总在一张表中，使得分析更具针对性。

![](img/3b67d7eb452d1a94a6c248e244fab107_100.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_102.png)

## 总结

![](img/3b67d7eb452d1a94a6c248e244fab107_104.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_106.png)

![](img/3b67d7eb452d1a94a6c248e244fab107_108.png)

本节课中我们一起学习了金融时间序列数据分析的基础流程。我们从导入和观察数据开始，然后通过绘图直观展示了多个指标的走势。接着，我们使用 `describe()` 函数进行了全面的描述性统计分析，并最后利用 `agg()` 聚合函数实现了自定义的统计指标汇总。这些是进行更深入金融数据分析前必不可少的基本步骤。