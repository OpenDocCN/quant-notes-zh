# Python金融分析与量化交易实战课程：P64：2-Pandas数据重采样 📊

![](img/a5d13b281cf7fc1545e30f109435d87b_0.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_2.png)

在本节课中，我们将要学习时间序列分析中一个非常实用的技术——数据重采样。我们将了解如何改变数据的时间频率，例如将每日数据转换为每月数据，或者反过来，并学习在转换过程中如何处理数据。

## 概述

数据重采样是处理时间序列数据时调整观测频率的过程。例如，你可能有一份按天统计的数据，但希望从更宏观的月度视角观察整体趋势，这时就需要进行降采样。反之，如果你有月度数据，想将其转换为更精细的每日数据，则需要进行升采样。本节将详细介绍如何使用Pandas库的`resample`方法来实现这两种操作。

![](img/a5d13b281cf7fc1545e30f109435d87b_4.png)

## 数据重采样的概念

![](img/a5d13b281cf7fc1545e30f109435d87b_6.png)

上一节我们介绍了时间序列的基础，本节中我们来看看如何改变其观测频率。

假设你有一份从2017年6月到2018年6月的每日数据。如果你不仅想观察每日变化，还想了解数据在每个月的整体变化程度，就需要将数据的频率从“天”转换为“月”。这个过程就是数据重采样。

在数据重采样中，我们主要处理两种场景：
*   **降采样**：将高频数据（如每日）聚合为低频数据（如每月）。例如，将365个每日数据点汇总为12个每月数据点，数据量减少。
*   **升采样**：将低频数据（如每月）转换为高频数据（如每日）。例如，将12个每月数据点扩展为更多的每日数据点，数据量增加。

![](img/a5d13b281cf7fc1545e30f109435d87b_8.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_10.png)

接下来，我们将通过代码演示如何使用Pandas完成这两种操作。

![](img/a5d13b281cf7fc1545e30f109435d87b_12.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_14.png)

## 实战：使用Pandas进行重采样

![](img/a5d13b281cf7fc1545e30f109435d87b_16.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_18.png)

首先，我们需要创建一份示例时间序列数据。

![](img/a5d13b281cf7fc1545e30f109435d87b_20.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_22.png)

```python
import pandas as pd
import numpy as np

![](img/a5d13b281cf7fc1545e30f109435d87b_24.png)

# 生成示例数据：从2017-01-01开始的90天随机数据
date_rng = pd.date_range(start='2017-01-01', periods=90, freq='D')
time_series = pd.Series(np.random.randn(len(date_rng)), index=date_rng)
print(time_series.head())
```

![](img/a5d13b281cf7fc1545e30f109435d87b_26.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_28.png)

### 1. 降采样操作

![](img/a5d13b281cf7fc1545e30f109435d87b_30.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_32.png)

降采样通常涉及数据的聚合。我们使用`resample()`方法，并指定目标频率（如‘M’代表月）和聚合函数（如求和、求均值）。

![](img/a5d13b281cf7fc1545e30f109435d87b_34.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_36.png)

**将每日数据转换为每月数据并求和：**

![](img/a5d13b281cf7fc1545e30f109435d87b_38.png)

```python
# 按‘M’（月）重采样，并计算每月的总和
monthly_sum = time_series.resample('M').sum()
print(monthly_sum)
```
**代码解释**：`resample('M')`将数据按月份分组，随后的`.sum()`对每个月内的所有值进行求和。原始90天的数据被聚合成了3个月（1月、2月、3月）的数据。

![](img/a5d13b281cf7fc1545e30f109435d87b_40.png)

**将每日数据转换为每3日数据并求和：**

![](img/a5d13b281cf7fc1545e30f109435d87b_42.png)

```python
# 按‘3D’（每3天）重采样，并计算每3天的总和
three_day_sum = time_series.resample('3D').sum()
print(three_day_sum.head())
```
**代码解释**：`resample('3D')`将数据按每3天一个窗口进行分组。结果中，1月1日的数据代表1-3日的总和，1月4日的数据代表4-6日的总和，依此类推。

![](img/a5d13b281cf7fc1545e30f109435d87b_44.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_46.png)

以下是降采样中常用的聚合方法：
*   `.sum()`: 求和
*   `.mean()`: 求平均值
*   `.apply()`: 应用自定义函数

![](img/a5d13b281cf7fc1545e30f109435d87b_48.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_50.png)

### 2. 升采样操作

![](img/a5d13b281cf7fc1545e30f109435d87b_52.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_54.png)

升采样会将低频数据转换为高频数据，但会产生缺失值（NaN），因为新频率下的许多数据点是未知的。例如，将每3天的汇总数据转换为每日数据时，第2天和第3天的具体值并不知道。

**直接升采样会产生缺失值：**

![](img/a5d13b281cf7fc1545e30f109435d87b_56.png)

```python
# 假设我们有一个每3天的汇总序列
three_day_series = time_series.resample('3D').mean()
print(three_day_series.head())

![](img/a5d13b281cf7fc1545e30f109435d87b_58.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_60.png)

# 尝试升采样回每日频率
daily_upsampled = three_day_series.resample('D').asfreq()
print(daily_upsampled.head())
```
**代码解释**：`asfreq()`方法仅改变频率而不进行插值。结果中，只有每3天的第一天有原始值，其余两天均为NaN。

![](img/a5d13b281cf7fc1545e30f109435d87b_62.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_64.png)

为了解决缺失值问题，我们需要进行插值填充。以下是三种常用的插值方法：

![](img/a5d13b281cf7fc1545e30f109435d87b_66.png)

**前向填充：**
```python
# 使用前一个有效值向后填充
ffill_result = daily_upsampled.ffill(limit=1)
print(ffill_result.head())
```
**代码解释**：`ffill()`或`fillna(method=‘ffill’)`用缺失值前面的那个有效值来填充。`limit=1`参数表示只填充紧随其后的一个缺失值。

![](img/a5d13b281cf7fc1545e30f109435d87b_68.png)

**后向填充：**
```python
# 使用后一个有效值向前填充
bfill_result = daily_upsampled.bfill(limit=1)
print(bfill_result.head())
```
**代码解释**：`bfill()`或`fillna(method=‘bfill’)`用缺失值后面的那个有效值来填充。

![](img/a5d13b281cf7fc1545e30f109435d87b_70.png)

**线性插值：**
```python
# 在已知数据点之间进行线性插值
linear_result = daily_upsampled.interpolate(method='linear')
print(linear_result.head())
```
**代码解释**：`interpolate(method=‘linear’)`方法将已知数据点连接成线，然后在线性曲线上计算缺失位置对应的值。这种方法假设数据变化是线性的。

![](img/a5d13b281cf7fc1545e30f109435d87b_72.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_74.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_76.png)

## 总结

![](img/a5d13b281cf7fc1545e30f109435d87b_78.png)

![](img/a5d13b281cf7fc1545e30f109435d87b_80.png)

本节课中我们一起学习了Pandas数据重采样的核心操作。
*   **降采样**：通过`resample(freq).agg_func()`（如`resample('M').sum()`）将高频数据聚合为低频数据，是信息浓缩的过程。
*   **升采样**：通过`resample(freq).asfreq()`将低频数据转换为高频数据，但会产生缺失值，常需配合`ffill()`、`bfill()`或`interpolate()`等插值方法进行填充。

![](img/a5d13b281cf7fc1545e30f109435d87b_82.png)

数据重采样是金融时间序列分析中的基础且重要的技能，它允许我们从不同时间尺度观察和分析数据，为后续的建模和策略制定提供灵活的数据视角。`resample`函数是实现这一过程的强大工具。