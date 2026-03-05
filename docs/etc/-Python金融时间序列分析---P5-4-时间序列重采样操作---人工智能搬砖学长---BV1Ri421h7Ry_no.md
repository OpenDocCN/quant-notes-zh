# Python金融时间序列分析与量化交易实战教程：P5：4.时间序列重采样操作 📊

![](img/c650ae84a1dcfc04ca2e449d129ea406_0.png)

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些技术对于将数据转换到不同时间尺度（如从天到周）以及计算滑动统计指标至关重要。

![](img/c650ae84a1dcfc04ca2e449d129ea406_2.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_4.png)

## 重采样操作

![](img/c650ae84a1dcfc04ca2e449d129ea406_6.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_8.png)

上一节我们介绍了时间序列的基础处理，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/c650ae84a1dcfc04ca2e449d129ea406_10.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_12.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_14.png)

重采样是指将时间序列数据从一个时间频率转换到另一个时间频率的过程。例如，原始数据可能是按天记录的，我们可以将其重新聚合为按周、按月或按年统计的数据。

在Pandas中，我们可以使用`resample`方法来实现。该方法至少需要一个参数，即指定新的时间频率单位（如`'W'`表示周，`'M'`表示月）。同时，我们需要指定聚合数据时使用的统计方法，例如均值、第一个值或中位数。

以下是重采样的基本代码示例：
```python
# 假设df是一个按天索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
# 按月重采样，并取每月的第一个值
monthly_data = df.resample('M').first()
```

![](img/c650ae84a1dcfc04ca2e449d129ea406_16.png)

### 重采样的标签设置

![](img/c650ae84a1dcfc04ca2e449d129ea406_18.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_20.png)

在重采样时，还有一个重要的参数是`label`，它决定了聚合后数据点的标签（索引）使用窗口的起始点（`label='left'`）还是结束点（`label='right'`）。

![](img/c650ae84a1dcfc04ca2e449d129ea406_22.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_24.png)

以下是两种标签设置的对比：
```python
# 默认使用窗口结束点作为标签
monthly_right = df.resample('M', label='right').mean()
# 使用窗口起始点作为标签
monthly_left = df.resample('M', label='left').mean()
```
大部分情况下，使用`label='right'`（默认值）更符合逻辑，因为它表示该统计值所涵盖的时间段（例如，1月31日的值代表整个1月份的数据）。而`label='left'`则可能产生不符合实际日历的标签。

![](img/c650ae84a1dcfc04ca2e449d129ea406_26.png)

## 时间窗口（滑动窗口）操作

![](img/c650ae84a1dcfc04ca2e449d129ea406_28.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_30.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_32.png)

接下来，我们学习另一个强大的工具：时间窗口操作，也称为滑动窗口。

![](img/c650ae84a1dcfc04ca2e449d129ea406_34.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_36.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_38.png)

时间窗口操作不是对整个数据集进行聚合，而是在一个固定大小的“窗口”内进行滑动计算。例如，我们有一个包含100天数据的时间序列，可以设置一个大小为10天的窗口。第一个窗口包含第1到第10天的数据，第二个窗口包含第2到第11天的数据，以此类推，每次滑动一天。这样，我们可以为每个窗口计算各种统计指标（如最小值、最大值、标准差），从而观察指标随时间变化的趋势。

![](img/c650ae84a1dcfc04ca2e449d129ea406_40.png)

### 如何实现滑动窗口

![](img/c650ae84a1dcfc04ca2e449d129ea406_42.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_44.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_46.png)

在Pandas中，我们使用`rolling`方法创建滑动窗口对象。

![](img/c650ae84a1dcfc04ca2e449d129ea406_48.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_50.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_52.png)

以下是实现滑动窗口计算的基本步骤：
1.  处理缺失值，确保数据质量。
2.  使用`rolling`方法指定窗口大小。
3.  对窗口对象应用聚合函数（如`.min()`, `.max()`, `.mean()`, `.std()`）。

![](img/c650ae84a1dcfc04ca2e449d129ea406_54.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_56.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_58.png)

以下是核心代码示例：
```python
# 1. 处理缺失值（非常重要！）
df_clean = df.dropna()

![](img/c650ae84a1dcfc04ca2e449d129ea406_60.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_62.png)

# 2. 定义窗口大小，例如10天
window_size = 10

![](img/c650ae84a1dcfc04ca2e449d129ea406_64.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_66.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_68.png)

# 3. 创建滑动窗口并计算统计量
# 计算每个10天窗口的最小值
rolling_min = df_clean['column_name'].rolling(window=window_size).min()
# 计算每个10天窗口的最大值
rolling_max = df_clean['column_name'].rolling(window=window_size).max()
# 计算每个10天窗口的标准差
rolling_std = df_clean['column_name'].rolling(window=window_size).std()

![](img/c650ae84a1dcfc04ca2e449d129ea406_70.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_72.png)

# 可以将这些结果添加到原DataFrame中
df_clean['rolling_min'] = rolling_min
df_clean['rolling_max'] = rolling_max
df_clean['rolling_std'] = rolling_std

![](img/c650ae84a1dcfc04ca2e449d129ea406_74.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_76.png)

# 查看结果
print(df_clean.tail())
```

![](img/c650ae84a1dcfc04ca2e449d129ea406_78.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_80.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_82.png)

**关键注意事项**：在应用`rolling`操作前，务必先处理数据中的缺失值（NaN）。如果窗口内存在NaN，那么该窗口的聚合结果通常也会是NaN，这会影响后续分析。因此，使用`.dropna()`等方法进行数据清洗是必不可少的一步。

## 总结

![](img/c650ae84a1dcfc04ca2e449d129ea406_84.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_86.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_88.png)

本节课中我们一起学习了时间序列分析的两个关键操作：
1.  **重采样**：用于改变数据的时间频率（如日→周→月），通过`resample()`方法实现，并可配合`label`参数控制聚合标签。
2.  **时间窗口操作**：用于在滑动的数据窗口上计算动态统计指标，通过`rolling()`方法实现。**请务必记住，在执行此操作前需要先清洗数据，处理缺失值。**

![](img/c650ae84a1dcfc04ca2e449d129ea406_90.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_92.png)

![](img/c650ae84a1dcfc04ca2e449d129ea406_94.png)

掌握这两种方法，能够帮助你更灵活地从不同时间维度分析金融数据，捕捉趋势和波动特征。