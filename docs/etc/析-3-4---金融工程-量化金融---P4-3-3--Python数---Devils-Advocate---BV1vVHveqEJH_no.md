# 量化交易：Python入门之数据分析【3/4】：3-3. Python数据分析：数据透视表 📊

在本节课中，我们将学习如何使用Pandas库中的`pivot_table`功能来创建数据透视表。数据透视表是一种强大的数据汇总工具，能够帮助我们快速从不同维度（如日期、城市）分析数据（如销售量、收入）。我们将通过一个简单的销售数据集，演示如何构建和定制数据透视表。

## 概述

![](img/4d3f5a0c634152bb370de751d742a8ae_1.png)

数据透视表允许我们以交互方式重新排列、汇总和呈现数据。它类似于Excel中的数据透视表功能，但在Python中我们可以通过编程方式实现更灵活的操作。本节将介绍其基本语法和常见应用。

![](img/4d3f5a0c634152bb370de751d742a8ae_3.png)

## 创建示例数据

![](img/4d3f5a0c634152bb370de751d742a8ae_5.png)

首先，我们需要一个包含日期、城市、销售量和收入的数据集。以下是我们的示例DataFrame：

![](img/4d3f5a0c634152bb370de751d742a8ae_7.png)

```python
import pandas as pd

![](img/4d3f5a0c634152bb370de751d742a8ae_9.png)

data = {
    '日期': ['1号', '2号', '3号', '1号', '2号', '3号'],
    '城市': ['上海', '上海', '上海', '北京', '北京', '北京'],
    '销售量': [100, 150, 200, 120, 130, 180],
    '收入': [5000, 7500, 10000, 6000, 6500, 9000]
}
df = pd.DataFrame(data)
print(df)
```

## 构建基础数据透视表

![](img/4d3f5a0c634152bb370de751d742a8ae_11.png)

上一节我们介绍了数据集的结构，本节中我们来看看如何构建一个基础的数据透视表。假设我们需要总结不同城市在不同日期的销售量。

![](img/4d3f5a0c634152bb370de751d742a8ae_13.png)

以下是构建该透视表的代码：

![](img/4d3f5a0c634152bb370de751d742a8ae_15.png)

```python
pivot_df = pd.pivot_table(df, index='日期', columns='城市', values='销售量')
print(pivot_df)
```

这段代码以“日期”为行索引，“城市”为列，并汇总“销售量”的值。默认情况下，`pivot_table`会对数值进行平均计算。

![](img/4d3f5a0c634152bb370de751d742a8ae_17.png)

## 对多个值进行聚合计算

有时我们需要对多个指标（如销售量和收入）同时进行汇总。以下是如何同时对这两个字段进行求和：

![](img/4d3f5a0c634152bb370de751d742a8ae_19.png)

```python
pivot_df_multi = pd.pivot_table(df, index='日期', columns='城市', values=['销售量', '收入'], aggfunc='sum')
print(pivot_df_multi)
```

## 应用不同的聚合函数

![](img/4d3f5a0c634152bb370de751d742a8ae_21.png)

我们可以为不同的数值列指定不同的聚合函数。例如，计算销售量的总和，同时计算收入的平均值：

![](img/4d3f5a0c634152bb370de751d742a8ae_22.png)

```python
pivot_df_custom = pd.pivot_table(df, index='日期', columns='城市', values=['销售量', '收入'], aggfunc={'销售量': 'sum', '收入': 'mean'})
print(pivot_df_custom)
```

![](img/4d3f5a0c634152bb370de751d742a8ae_24.png)

## 处理缺失值

![](img/4d3f5a0c634152bb370de751d742a8ae_26.png)

生成的数据透视表中可能出现`NaN`（空值）。我们可以使用`fill_value`参数来填充这些缺失值，例如用0填充：

![](img/4d3f5a0c634152bb370de751d742a8ae_28.png)

```python
pivot_df_filled = pd.pivot_table(df, index='日期', columns='城市', values=['销售量', '收入'], aggfunc={'销售量': 'sum', '收入': 'mean'}, fill_value=0)
print(pivot_df_filled)
```

## 总结

![](img/4d3f5a0c634152bb370de751d742a8ae_30.png)

本节课中我们一起学习了Pandas数据透视表的核心功能。我们掌握了如何：
1.  使用`pd.pivot_table()`创建基础透视表。
2.  通过`index`和`columns`参数设置数据的行与列。
3.  使用`values`参数选择要汇总的数值列。
4.  利用`aggfunc`参数对数据进行求和、求平均等聚合计算。
5.  使用`fill_value`参数处理结果中的缺失值。

![](img/4d3f5a0c634152bb370de751d742a8ae_32.png)

数据透视表是数据分析中用于快速总结和探索多维数据的强大工具，在金融数据分析等领域应用广泛。