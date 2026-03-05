# 金融量化分析：P4：金融时间序列数据统计分析 📊

在本节课中，我们将学习如何使用Python对金融时间序列数据进行基本的统计分析。我们将从导入和查看数据开始，逐步学习如何计算关键统计指标，并使用图表直观展示数据走势。

## 数据导入与初步查看

首先，我们需要导入必要的工具包并加载数据。上一节我们介绍了课程的整体目标，本节中我们来看看如何具体操作。

![](img/c210d133a7bff27ccca393e88772c410_1.png)

![](img/c210d133a7bff27ccca393e88772c410_2.png)

![](img/c210d133a7bff27ccca393e88772c410_3.png)

![](img/c210d133a7bff27ccca393e88772c410_4.png)

以下是导入工具包和数据的步骤：

![](img/c210d133a7bff27ccca393e88772c410_5.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

加载数据时，有两个关键要求：以时间为索引，并将时间列转换为标准格式。我们可以通过`read_csv`函数的参数一次性完成。

![](img/c210d133a7bff27ccca393e88772c410_7.png)

```python
# 读取数据，指定第一列为索引，并解析日期格式
data = pd.read_csv(‘data.csv‘, index_col=0, parse_dates=True)
# 查看前几行数据
print(data.head())
```

执行上述代码后，数据框的索引将变为`datetime`类型，数据按时间顺序排列，便于后续的时间序列分析。

![](img/c210d133a7bff27ccca393e88772c410_9.png)

![](img/c210d133a7bff27ccca393e88772c410_10.png)

## 数据可视化

![](img/c210d133a7bff27ccca393e88772c410_12.png)

![](img/c210d133a7bff27ccca393e88772c410_13.png)

有了数据之后，我们可以通过绘图直观地观察各个指标（如不同公司的股价）随时间变化的走势。

以下是绘制走势图的两种方法：

![](img/c210d133a7bff27ccca393e88772c410_15.png)

![](img/c210d133a7bff27ccca393e88772c410_16.png)

![](img/c210d133a7bff27ccca393e88772c410_17.png)

1.  **单图展示（所有指标在一张图中）**:
    ```python
    data.plot(figsize=(12, 8))
    plt.show()
    ```

![](img/c210d133a7bff27ccca393e88772c410_18.png)

![](img/c210d133a7bff27ccca393e88772c410_19.png)

2.  **子图展示（每个指标单独一个子图）**:
    ```python
    data.plot(subplots=True, figsize=(12, 16))
    plt.show()
    ```

第一种方法适合对比不同指标在同一时间段的相对变化；第二种方法能更清晰地观察每个指标的独立走势。选择哪种方式取决于你的分析需求。

![](img/c210d133a7bff27ccca393e88772c410_21.png)

![](img/c210d133a7bff27ccca393e88772c410_22.png)

![](img/c210d133a7bff27ccca393e88772c410_23.png)

![](img/c210d133a7bff27ccca393e88772c410_24.png)

## 基础统计分析

![](img/c210d133a7bff27ccca393e88772c410_25.png)

![](img/c210d133a7bff27ccca393e88772c410_26.png)

![](img/c210d133a7bff27ccca393e88772c410_27.png)

![](img/c210d133a7bff27ccca393e88772c410_28.png)

在直观了解数据走势后，我们需要进行量化的统计分析。这包括查看数据的基本信息和计算核心统计指标。

首先，我们可以使用`.info()`方法快速了解数据的整体情况，如样本数量、时间范围、数据类型和缺失值数量。

![](img/c210d133a7bff27ccca393e88772c410_30.png)

![](img/c210d133a7bff27ccca393e88772c410_31.png)

![](img/c210d133a7bff27ccca393e88772c410_32.png)

![](img/c210d133a7bff27ccca393e88772c410_33.png)

```python
print(data.info())
```

接下来，计算各项指标的统计摘要。`describe()`函数可以快速生成计数、均值、标准差、最小值、四分位数和最大值。

![](img/c210d133a7bff27ccca393e88772c410_35.png)

```python
# 计算基本统计量，并保留两位小数
stats_summary = data.describe().round(2)
print(stats_summary)
```

![](img/c210d133a7bff27ccca393e88772c410_36.png)

![](img/c210d133a7bff27ccca393e88772c410_37.png)

![](img/c210d133a7bff27ccca393e88772c410_38.png)

![](img/c210d133a7bff27ccca393e88772c410_39.png)

## 使用聚合函数汇总统计指标

![](img/c210d133a7bff27ccca393e88772c410_41.png)

![](img/c210d133a7bff27ccca393e88772c410_42.png)

![](img/c210d133a7bff27ccca393e88772c410_43.png)

除了使用`describe()`，我们还可以使用`agg()`（或`aggregate()`）函数，自定义需要汇总的统计指标，实现更灵活的统计分析。

上一节我们介绍了基础的统计描述，本节中我们来看看如何自定义聚合。

![](img/c210d133a7bff27ccca393e88772c410_45.png)

![](img/c210d133a7bff27ccca393e88772c410_46.png)

![](img/c210d133a7bff27ccca393e88772c410_47.png)

![](img/c210d133a7bff27ccca393e88772c410_48.png)

以下是如何使用`agg`函数计算最小值、最大值、均值、标准差和中位数的示例：

```python
# 定义需要计算的统计指标
custom_stats = data.agg([‘min‘, ‘max‘, ‘mean‘, ‘std‘, ‘median‘])
print(custom_stats)
```

![](img/c210d133a7bff27ccca393e88772c410_50.png)

![](img/c210d133a7bff27ccca393e88772c410_51.png)

![](img/c210d133a7bff27ccca393e88772c410_52.png)

通过`agg`函数，你可以自由组合`numpy`或`pandas`支持的任意统计函数，从而聚焦于你关心的特定指标。

![](img/c210d133a7bff27ccca393e88772c410_53.png)

![](img/c210d133a7bff27ccca393e88772c410_54.png)

## 总结

本节课中我们一起学习了金融时间序列数据分析的完整流程。

![](img/c210d133a7bff27ccca393e88772c410_56.png)

![](img/c210d133a7bff27ccca393e88772c410_57.png)

![](img/c210d133a7bff27ccca393e88772c410_58.png)

![](img/c210d133a7bff27ccca393e88772c410_59.png)

1.  **数据准备**：我们使用`pandas`正确导入数据，并将时间列设置为索引，这是处理时间序列数据的基础。
2.  **数据可视化**：我们使用`plot`方法绘制了时间序列图，通过单图和子图两种形式直观展示了数据走势。
3.  **统计分析**：我们利用`describe()`函数快速获取了数据集的统计摘要，并通过`round`方法控制了结果的精度。
4.  **自定义聚合**：我们掌握了`agg()`函数的使用，能够灵活计算和汇总自定义的统计指标。

![](img/c210d133a7bff27ccca393e88772c410_60.png)

![](img/c210d133a7bff27ccca393e88772c410_61.png)

![](img/c210d133a7bff27ccca393e88772c410_62.png)

![](img/c210d133a7bff27ccca393e88772c410_63.png)

这些操作构成了金融时间序列分析的基石，熟练掌握后，你便能对一份新的金融数据集进行有效的初步探索和洞察。