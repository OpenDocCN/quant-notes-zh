# Python金融分析与量化交易实战教程：P2：1-金融时间序列数据统计分析 📈

在本节课中，我们将学习如何使用Python对金融时间序列数据进行统计分析。我们将从导入和查看数据开始，逐步学习如何计算关键统计指标，并使用图表直观展示数据走势。

## 数据导入与初步观察

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_1.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_3.png)

首先，我们需要导入必要的Python库并加载数据。金融时间序列数据是指随着时间变化而记录的数据，例如每日股价、汇率等。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_5.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_7.png)

以下是导入库和数据的代码：

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_9.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 读取数据，并将第一列‘Date’设置为索引，同时解析日期格式
df = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

执行上述代码后，我们得到了一个以日期为索引的`DataFrame`。数据包含了从2010年到2018年中期多个金融指标的历史记录，例如苹果、微软、亚马逊等公司的股价，以及黄金价格、汇率等其他指标。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_11.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_13.png)

为了更清晰地查看数据，我们可以使用`.head()`方法：

```python
df.head()
```

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_15.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_17.png)

## 可视化数据走势

在初步了解数据结构后，我们可以通过绘图来直观展示各个指标随时间变化的趋势。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_19.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_21.png)

以下是绘制所有指标走势图的代码：

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_23.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_25.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_27.png)

```python
# 使用pandas内置的绘图功能，绘制所有列的时间序列图
df.plot(figsize=(15, 10))
plt.show()
```

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_29.png)

上述代码会将所有指标绘制在同一张图上。如果我们希望每个指标单独显示在一个子图中以便更清晰地对比，可以添加`subplots`参数：

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_31.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_33.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_35.png)

```python
# 将每个指标绘制在独立的子图中
df.plot(subplots=True, figsize=(15, 20))
plt.tight_layout()
plt.show()
```

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_37.png)

选择哪种绘图方式取决于你的分析需求。在同一张图中可以方便地进行横向对比，而子图则能更清晰地展示每个指标的独立走势。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_39.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_41.png)

## 基础统计分析

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_43.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_45.png)

上一节我们介绍了如何导入和可视化数据，本节中我们来看看如何对数据进行基础的统计分析。这是理解数据分布和特征的关键步骤。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_47.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_49.png)

首先，我们可以使用`.info()`方法快速查看数据的整体情况，包括样本数量、数据类型和缺失值信息。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_51.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_53.png)

```python
df.info()
```

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_55.png)

接下来，我们将计算数据集中各项指标的核心统计量。Pandas提供了非常便捷的`.describe()`方法。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_57.png)

以下是计算并格式化输出统计结果的代码：

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_59.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_61.png)

```python
# 计算描述性统计，并使用round(2)将结果保留两位小数
summary_stats = df.describe().round(2)
print(summary_stats)
```

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_63.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_65.png)

`.describe()`方法默认返回的统计量包括：**计数（count）**、**均值（mean）**、**标准差（std）**、**最小值（min）**、**四分位数（25%， 50%， 75%）**和**最大值（max）**。通过`.round(2)`，我们让结果更简洁易读。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_67.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_69.png)

## 使用聚合函数自定义统计

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_71.png)

虽然`.describe()`方法很全面，但有时我们可能只需要关注特定的几个统计指标。这时，可以使用聚合函数`agg()`来自定义汇总。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_73.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_75.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_77.png)

以下是使用`agg()`函数计算最小值、最大值、均值、标准差和中位数的示例：

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_79.png)

```python
# 定义需要计算的统计函数列表
stat_functions = [‘min‘, ‘max‘, ‘mean‘, ‘std‘, ‘median‘]

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_81.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_83.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_85.png)

# 应用聚合函数
custom_stats = df.agg(stat_functions).round(2)
print(custom_stats)
```

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_87.png)

通过`agg()`函数，我们可以自由组合`numpy`或`pandas`支持的任意统计函数，例如`np.mean`、`np.std`等，从而得到一份量身定制的统计摘要。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_89.png)

## 总结

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_91.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_93.png)

本节课中我们一起学习了金融时间序列数据分析的基础操作。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_95.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_97.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_99.png)

1.  **数据导入与观察**：我们使用`pandas.read_csv`函数加载数据，并将日期列设置为索引，这是处理时间序列数据的标准起点。
2.  **数据可视化**：我们利用`DataFrame.plot()`方法绘制了指标的趋势图，并学会了通过`subplots`参数创建子图来分别展示各个指标。
3.  **描述性统计**：我们掌握了使用`.describe()`方法快速获取数据集的整体统计概况，包括中心趋势和离散程度。
4.  **自定义聚合统计**：我们学习了使用`.agg()`函数灵活地计算一组自定义的统计指标，这能满足更具体的分析需求。

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_101.png)

![](img/bb0e4844cbac56dd94de0abb9b72c7fe_103.png)

这些技能是进行更深入的金融数据分析（如计算收益率、波动率或构建交易策略）的基石。在接下来的课程中，我们将基于这些基础，探索更复杂的分析技术。