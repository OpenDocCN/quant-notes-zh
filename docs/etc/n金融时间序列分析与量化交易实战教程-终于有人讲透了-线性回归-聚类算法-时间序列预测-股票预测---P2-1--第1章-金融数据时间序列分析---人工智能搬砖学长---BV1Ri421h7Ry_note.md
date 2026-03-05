# Python金融时间序列分析与量化交易实战教程：P2：1：金融数据时间序列分析 📈

在本节课中，我们将学习如何使用Python进行金融时间序列分析。我们将从导入和查看数据开始，逐步学习如何对时间序列数据进行可视化、基本统计和聚合分析。

![](img/6f7a940cc6bdef0199e7dcae99253731_1.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_3.png)

## 导入数据与理解时间序列

![](img/6f7a940cc6bdef0199e7dcae99253731_5.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_7.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_9.png)

首先，我们需要导入必要的工具包并加载数据。时间序列是指数据随着时间变化而变化的序列，例如每日股价、月度销售额等。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，我们读取数据文件，并将“日期”列设置为索引，同时将日期格式转换为标准格式。

![](img/6f7a940cc6bdef0199e7dcae99253731_11.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_13.png)

```python
# 读取CSV文件，指定第一列（日期）为索引，并解析日期格式
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/6f7a940cc6bdef0199e7dcae99253731_15.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_17.png)

执行上述代码后，数据将以日期为索引加载。例如，数据可能从2010年1月1日持续到2018年6月29日，包含苹果、微软、亚马逊等公司的股价以及其他金融指标（如黄金价格、汇率等）。数据中可能存在一些缺失值。

![](img/6f7a940cc6bdef0199e7dcae99253731_19.png)

## 可视化时间序列走势

![](img/6f7a940cc6bdef0199e7dcae99253731_21.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_23.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_25.png)

上一节我们导入了数据，本节中我们来看看如何将各个指标的走势可视化展示出来。

![](img/6f7a940cc6bdef0199e7dcae99253731_27.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_29.png)

最简单的方法是使用Pandas内置的绘图功能。我们可以将所有指标的走势画在一张图上，也可以分开展示为子图。

![](img/6f7a940cc6bdef0199e7dcae99253731_31.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_33.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_35.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_37.png)

```python
# 方法一：所有指标画在一张图上
data.plot(figsize=(12, 8))
plt.show()

![](img/6f7a940cc6bdef0199e7dcae99253731_39.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_41.png)

# 方法二：将各个指标以子图形式展示
data.plot(subplots=True, figsize=(12, 10))
plt.show()
```

![](img/6f7a940cc6bdef0199e7dcae99253731_43.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_45.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_47.png)

两种方法都可以清晰地展示出每个指标随时间变化的趋势。你可以根据分析需求选择合适的方式。

![](img/6f7a940cc6bdef0199e7dcae99253731_49.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_51.png)

## 进行基本统计分析

![](img/6f7a940cc6bdef0199e7dcae99253731_53.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_55.png)

在初步观察数据走势后，我们需要对数据进行更深入的统计分析。这有助于我们理解数据的整体分布和特征。

![](img/6f7a940cc6bdef0199e7dcae99253731_57.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_59.png)

首先，我们可以使用`describe`方法快速获取数据的基本统计信息，如样本数、均值、标准差、最小值、最大值和各分位数。

![](img/6f7a940cc6bdef0199e7dcae99253731_61.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_63.png)

```python
# 获取数据的基本统计描述，并保留两位小数
basic_stats = data.describe().round(2)
print(basic_stats)
```

![](img/6f7a940cc6bdef0199e7dcae99253731_65.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_67.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_69.png)

执行后，我们会得到一个包含计数、均值、标准差、最小值、25%分位数、中位数、75%分位数和最大值的表格。

![](img/6f7a940cc6bdef0199e7dcae99253731_71.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_73.png)

## 使用聚合函数汇总统计指标

![](img/6f7a940cc6bdef0199e7dcae99253731_75.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_77.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_79.png)

除了使用`describe`，我们还可以使用聚合函数`agg`来自定义需要汇总的统计指标。这提供了更大的灵活性。

![](img/6f7a940cc6bdef0199e7dcae99253731_81.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_83.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_85.png)

以下是使用`agg`函数汇总最小值、最大值、均值、标准差和中位数的示例。

![](img/6f7a940cc6bdef0199e7dcae99253731_87.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_89.png)

```python
# 使用agg函数自定义聚合统计指标
custom_stats = data.agg(['min', 'max', np.mean, np.std, np.median])
print(custom_stats)
```

![](img/6f7a940cc6bdef0199e7dcae99253731_91.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_93.png)

通过这种方式，我们可以选择性地关注那些对分析最有意义的统计量，并将它们集中展示在一个结果表中。

![](img/6f7a940cc6bdef0199e7dcae99253731_95.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_97.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_99.png)

## 总结

![](img/6f7a940cc6bdef0199e7dcae99253731_101.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_103.png)

![](img/6f7a940cc6bdef0199e7dcae99253731_105.png)

本节课中我们一起学习了金融时间序列分析的基础操作。我们首先导入了数据并将其时间列设置为索引。然后，我们使用绘图功能可视化了多个金融指标的走势。接着，我们通过`describe`方法对数据进行了基本的统计分析。最后，我们使用`agg`聚合函数自定义并汇总了关键的统计指标。这些步骤为后续更复杂的时间序列分析和建模打下了坚实的基础。