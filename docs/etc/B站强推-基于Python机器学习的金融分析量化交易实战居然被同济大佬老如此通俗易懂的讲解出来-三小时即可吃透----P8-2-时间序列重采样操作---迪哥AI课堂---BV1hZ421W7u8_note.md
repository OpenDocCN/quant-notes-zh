# 金融量化分析：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些技术对于将原始数据转换为不同时间尺度进行分析，以及计算滑动统计指标至关重要。

## 重采样操作

![](img/92bdde6c2166aa9d3a084d45c523c989_1.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_3.png)

上一节我们介绍了时间序列的基础概念，本节中我们来看看如何改变数据的时间频率，即重采样。

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天统计的，我们可以将其调整为按周、按月或按年进行统计。在Pandas中，这通过`resample`方法实现。

![](img/92bdde6c2166aa9d3a084d45c523c989_5.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_7.png)

以下是重采样的基本步骤：
1.  指定新的时间频率单位（如‘W’代表周，‘M’代表月）。
2.  指定聚合方法（如计算均值、求和、第一个值等）。

![](img/92bdde6c2166aa9d3a084d45c523c989_9.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_11.png)

```python
# 假设df是一个按天索引的DataFrame
# 按周进行重采样，并计算每周的均值
weekly_data = df.resample(‘W’).mean()
```

执行重采样后，我们得到按新频率聚合后的结果。例如，下图展示了将日数据重采样为周数据后的部分结果。

![](img/92bdde6c2166aa9d3a084d45c523c989_13.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_1.png)

我们不仅可以按周重采样，也可以轻松地切换到其他频率，例如按月重采样。

```python
# 按月进行重采样，并计算每月的第一个值
monthly_data = df.resample(‘M’).first()
```

![](img/92bdde6c2166aa9d3a084d45c523c989_15.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_7.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_17.png)

在重采样时，有一个重要的参数是`label`，它决定了聚合结果的标签（索引）使用时间窗口的哪一端。

![](img/92bdde6c2166aa9d3a084d45c523c989_19.png)

以下是`label`参数的两个主要选项：
*   **`label=‘right’`**：默认值。使用时间窗口的**结束日期**作为该窗口数据的标签。例如，一月份的数据聚合后，标签是1月31日。
*   **`label=‘left’`**：使用时间窗口的**开始日期**作为标签。例如，一月份的数据聚合后，标签是1月1日。

![](img/92bdde6c2166aa9d3a084d45c523c989_21.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_23.png)

```python
# 使用窗口起始日作为标签
monthly_data_left = df.resample(‘M’, label=‘left’).mean()
```

![](img/92bdde6c2166aa9d3a084d45c523c989_25.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_13.png)

对比`label=‘right’`和`label=‘left’`的结果可以发现，虽然数据值相同，但索引日期不同。选择`‘right’`通常更符合逻辑，因为它表示该统计值所涵盖的时间段已经结束。当然，具体选择需根据分析任务的需求而定。

## 时间窗口（滑动窗口）操作

了解了如何改变数据频率后，我们接下来学习如何在原有频率上创建滑动窗口进行计算，这是时间序列分析的另一个强大工具。

![](img/92bdde6c2166aa9d3a084d45c523c989_27.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_29.png)

时间窗口操作，也称为滑动窗口操作，其核心思想是：定义一个固定长度的窗口（例如10天），让这个窗口在时间序列上依次滑动。对于每一个窗口位置，我们都计算其内部数据的统计量（如均值、最小值、最大值等）。

例如，对于一个有100天的数据序列：
*   第一个窗口：第1天到第10天。
*   第二个窗口：第2天到第11天。
*   第三个窗口：第3天到第12天。
*   …依此类推。

在Pandas中，我们使用`rolling`方法来实现滑动窗口计算。

![](img/92bdde6c2166aa9d3a084d45c523c989_31.png)

以下是执行滑动窗口操作的基本步骤：
1.  指定窗口大小（例如`window=10`）。
2.  调用聚合函数（如`.min()`, `.max()`, `.mean()`, `.std()`）。

![](img/92bdde6c2166aa9d3a084d45c523c989_33.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_35.png)

```python
# 计算10天滑动窗口的最小值
rolling_min = df[‘column_name’].rolling(window=10).min()
```

![](img/92bdde6c2166aa9d3a084d45c523c989_31.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_37.png)

**一个重要提示**：在应用滑动窗口操作前，必须处理数据中的缺失值（NaN）。如果窗口内存在NaN，整个窗口的聚合结果通常也会是NaN。

![](img/92bdde6c2166aa9d3a084d45c523c989_39.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_41.png)

因此，我们需要先进行数据预处理：

![](img/92bdde6c2166aa9d3a084d45c523c989_43.png)

```python
# 删除缺失值
df_clean = df.dropna()
# 在清洗后的数据上计算滑动窗口统计量
rolling_min_clean = df_clean[‘column_name’].rolling(window=10).min()
```

![](img/92bdde6c2166aa9d3a084d45c523c989_45.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_47.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_39.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_49.png)

处理完缺失值后，我们可以为每个窗口计算多个统计指标，并将结果作为新列添加到数据集中。

![](img/92bdde6c2166aa9d3a084d45c523c989_51.png)

以下是为滑动窗口计算最小值、最大值、均值和标准差的示例：

![](img/92bdde6c2166aa9d3a084d45c523c989_53.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_55.png)

```python
df_clean[‘rolling_min’] = df_clean[‘column_name’].rolling(window=10).min()
df_clean[‘rolling_max’] = df_clean[‘column_name’].rolling(window=10).max()
df_clean[‘rolling_mean’] = df_clean[‘column_name’].rolling(window=10).mean()
df_clean[‘rolling_std’] = df_clean[‘column_name’].rolling(window=10).std()

![](img/92bdde6c2166aa9d3a084d45c523c989_57.png)

# 查看结果
print(df_clean.tail())
```

![](img/92bdde6c2166aa9d3a084d45c523c989_59.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_57.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_61.png)

这样，我们就得到了基于滑动窗口的丰富统计特征，这些特征对于捕捉时间序列的局部趋势和波动非常有用。

![](img/92bdde6c2166aa9d3a084d45c523c989_63.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_65.png)

## 总结

![](img/92bdde6c2166aa9d3a084d45c523c989_67.png)

本节课中我们一起学习了时间序列分析的两个关键操作：
1.  **重采样**：使用`df.resample()`改变数据的时间频率（如日变周、月），并通过`label`参数控制聚合结果的标签。
2.  **时间窗口操作**：使用`df.rolling()`创建滑动窗口，计算窗口内的统计量（最小值、最大值等）。**切记**，在执行此操作前，务必先使用`df.dropna()`等方法处理数据缺失值，以确保计算结果的准确性。

![](img/92bdde6c2166aa9d3a084d45c523c989_69.png)

![](img/92bdde6c2166aa9d3a084d45c523c989_71.png)

掌握这两种方法，能够帮助你灵活地从不同时间维度洞察金融数据的内在规律，为后续的量化策略构建打下坚实基础。