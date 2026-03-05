# Python金融量化交易：8.8.2：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。重采样允许我们改变数据的时间频率，而时间窗口操作则用于计算滑动窗口内的统计指标。掌握这些技巧对于金融数据的分析和建模至关重要。

## 重采样操作

上一节我们介绍了时间序列的基础概念，本节中我们来看看如何改变数据的时间频率，即重采样。

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或以年为单位的统计数据。在Pandas中，这通过`resample`方法实现。

以下是重采样的基本语法：
```python
# 假设df是一个以日期为索引的DataFrame
# 以周为单位进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

![](img/4c7ca283a33c5037ac8d0cd2d0006975_1.png)

在重采样过程中，你需要指定新的时间频率单位。同时，你需要决定如何聚合每个新时间窗口内的数据，例如计算均值、第一个值、中位数或其他统计指标。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_3.png)

让我们看一个具体的例子。假设我们有一份按天统计的数据，现在想转换为按周统计：

![](img/4c7ca283a33c5037ac8d0cd2d0006975_1.png)

执行重采样后，我们得到按周汇总的结果。注意，由于原始数据可能在某些日期缺失，重采样后的起始日期可能会调整。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_3.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_5.png)

我们也可以轻松地更改为其他频率，例如按月进行重采样：

![](img/4c7ca283a33c5037ac8d0cd2d0006975_7.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_5.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_9.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_7.png)

在重采样时，还有一个重要的参数是`label`，它决定了每个聚合窗口的标签使用哪一天的日期。

以下是`label`参数的作用：
*   **`label=‘right’` (默认)**：使用窗口的**最后一天**作为该窗口数据的标签。
*   **`label=‘left’`**：使用窗口的**第一天**作为该窗口数据的标签。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_11.png)

例如，按月重采样时：
```python
# 使用窗口最后一天作为标签（默认）
monthly_data_right = df.resample(‘M’, label=‘right’).mean()
# 使用窗口第一天作为标签
monthly_data_left = df.resample(‘M’, label=‘left’).mean()
```

![](img/4c7ca283a33c5037ac8d0cd2d0006975_9.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_11.png)

对比结果可以发现，虽然统计的数值相同，但索引日期不同。`label=‘left’`时，标签是每个月的第一天（如12月31日代表12月的数据）；`label=‘right’`时，标签是每个月的最后一天（如1月31日代表1月的数据）。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_13.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_15.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_13.png)

在金融分析中，通常使用`label=‘right’`更为直观，因为它表示该统计值是基于截至标签日期的窗口数据计算得出的。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_15.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_17.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_19.png)

重采样操作非常灵活，可以根据分析需求将数据转换到任意时间频率。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_17.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_21.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_19.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_23.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_21.png)

## 时间窗口（滑动窗口）操作

![](img/4c7ca283a33c5037ac8d0cd2d0006975_23.png)

了解了如何改变数据频率后，我们接下来学习另一个强大的工具：时间窗口操作，也称为滑动窗口操作。

时间窗口操作不是改变数据频率，而是在一个固定长度的窗口上滑动，并计算每个窗口内的统计量。例如，我们有一个100天的序列，可以：
1.  第一个窗口：第1天到第10天。
2.  第二个窗口：第2天到第11天（窗口向后滑动1天）。
3.  第三个窗口：第3天到第12天。
以此类推。这在计算移动平均线、滚动标准差等技术指标时非常有用。

在Pandas中，我们使用`rolling`方法来实现滑动窗口计算。

以下是创建和计算滑动窗口的基本步骤：
```python
# 定义窗口大小
window_size = 10
# 对某一列数据应用滚动窗口，并计算每个窗口的最小值
rolling_min = df[‘column_name’].rolling(window=window_size).min()
```

让我们通过代码来演示。首先，我们指定一个窗口大小，然后对数据列应用`rolling`操作。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_25.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_27.png)

直接展示滚动对象可能不直观，我们可以立即计算每个窗口的统计量，例如最小值：

![](img/4c7ca283a33c5037ac8d0cd2d0006975_25.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_29.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_27.png)

结果的前几个值会是`NaN`，因为需要累积足够的数据（10个）才能形成第一个完整的窗口。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_31.png)

这里有一个关键点：如果原始数据本身存在缺失值（`NaN`），那么包含该缺失值的任何窗口的计算结果也会是`NaN`。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_33.png)

因此，在进行窗口操作前，**数据预处理**，特别是处理缺失值，至关重要。我们可以使用`dropna()`方法移除缺失值。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_29.png)

```python
# 处理缺失值
df_clean = df.dropna()
# 在清洗后的数据上进行窗口操作
rolling_min_clean = df_clean[‘column_name’].rolling(window=window_size).min()
```

![](img/4c7ca283a33c5037ac8d0cd2d0006975_35.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_31.png)

处理之后，前几个`NaN`是由于窗口未满造成的，这是正常的；而之后窗口内的计算结果就不再受原始缺失值影响了。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_33.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_37.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_39.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_41.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_35.png)

我们可以方便地为一个数据集计算多个滚动统计指标，并将它们作为新列添加到DataFrame中。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_37.png)

以下是同时计算多个窗口统计量的示例：
```python
window_size = 10
df[‘rolling_min’] = df[‘column_name’].rolling(window=window_size).min()
df[‘rolling_max’] = df[‘column_name’].rolling(window=window_size).max()
df[‘rolling_mean’] = df[‘column_name’].rolling(window=window_size).mean()
df[‘rolling_std’] = df[‘column_name’].rolling(window=window_size).std()
```

![](img/4c7ca283a33c5037ac8d0cd2d0006975_43.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_39.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_45.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_41.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_47.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_43.png)

现在，让我们将这些统计量添加到清洗后的数据中：

![](img/4c7ca283a33c5037ac8d0cd2d0006975_45.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_49.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_51.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_47.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_53.png)

查看数据的尾部，可以看到我们成功计算出了每个窗口的最小值、最大值、均值和标准差。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_49.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_55.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_51.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_57.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_59.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_53.png)

这就是时间窗口操作的核心：在一个滑动的数据范围上计算各种统计特征。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_55.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_61.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_57.png)

最后，请务必记住执行窗口操作前的关键步骤。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_63.png)

**预处理数据，处理缺失值**。否则，原始数据中的`NaN`会污染整个窗口的计算结果。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_59.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_65.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_61.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_67.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_63.png)

这是所有数据分析任务中都需要注意的基础环节。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_65.png)

![](img/4c7ca283a33c5037ac8d0cd2d0006975_69.png)

## 总结

![](img/4c7ca283a33c5037ac8d0cd2d0006975_67.png)

本节课中我们一起学习了时间序列分析的两个重要操作：
1.  **重采样**：使用`resample()`方法改变数据的时间频率（如日→周、月），并可指定聚合方式（如`mean()`, `sum()`）和窗口标签位置（`label`参数）。
2.  **时间窗口操作**：使用`rolling()`方法创建滑动窗口，计算窗口内的统计指标（如最小值、最大值、均值、标准差）。在进行此操作前，必须对数据做好预处理，特别是使用`dropna()`等方法处理缺失值。

![](img/4c7ca283a33c5037ac8d0cd2d0006975_69.png)

掌握这些技能，你将能更灵活地处理和分析金融时间序列数据，为后续的量化策略开发打下坚实基础。