# Python金融分析与量化交易实战教程：P5：4-时间序列重采样操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**操作。这些技术对于将数据聚合到不同时间尺度（如从日数据到周数据）以及计算滑动统计指标至关重要。

![](img/493fe8cc0b6621a5142b40c54402a869_1.png)

## 重采样操作

![](img/493fe8cc0b6621a5142b40c54402a869_3.png)

上一节我们介绍了时间序列的基础操作，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/493fe8cc0b6621a5142b40c54402a869_5.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或按年进行统计。在Pandas中，这通过`resample`方法实现。重采样后，我们需要指定一个聚合函数（如均值、第一个值、中位数等）来计算新频率下的数据值。

![](img/493fe8cc0b6621a5142b40c54402a869_7.png)

![](img/493fe8cc0b6621a5142b40c54402a869_9.png)

以下是重采样的基本语法：
```python
# 假设df是一个以日期为索引的DataFrame
# 以‘W’（周）为频率进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

![](img/493fe8cc0b6621a5142b40c54402a869_11.png)

### 重采样频率与标签

在重采样时，我们可以指定不同的频率和标签对齐方式。

*   **频率参数**：在`resample()`方法中传入字符串指定频率，例如 `'W'`（周）、`'M'`（月）、`'Y'`（年）。
*   **标签参数 (`label`)**：此参数决定重采样后时间戳的标签是使用时间区间的**起始点 (`label='left'`)** 还是**结束点 (`label='right'`)**。默认是`'right'`。

![](img/493fe8cc0b6621a5142b40c54402a869_13.png)

![](img/493fe8cc0b6621a5142b40c54402a869_15.png)

```python
# 以月为单位重采样，并指定使用区间起始点作为标签
monthly_data_left = df.resample('M', label='left').mean()
# 以月为单位重采样，使用默认的区间结束点作为标签
monthly_data_right = df.resample('M').mean() # 等价于 label='right'
```

![](img/493fe8cc0b6621a5142b40c54402a869_17.png)

通常，使用结束点 (`label='right'`) 作为标签更符合逻辑，因为它表示该时间段（如一个月）结束时汇总的数据。但具体选择需根据分析任务的需求而定。

![](img/493fe8cc0b6621a5142b40c54402a869_19.png)

![](img/493fe8cc0b6621a5142b40c54402a869_21.png)

## 时间窗口（滑动窗口）操作

![](img/493fe8cc0b6621a5142b40c54402a869_23.png)

接下来，我们学习时间窗口操作，也称为滑动窗口操作。这与重采样不同，它是在一个固定大小的“窗口”上滑动，并计算窗口内数据的统计量。

其核心思想是：假设有100天的数据，我们设置一个窗口大小为10天。第一个窗口包含第1到第10天的数据，第二个窗口包含第2到第11天的数据，第三个窗口包含第3到第12天的数据，以此类推。窗口像滑块一样在时间轴上移动。

### 实现滑动窗口计算

![](img/493fe8cc0b6621a5142b40c54402a869_25.png)

在Pandas中，我们使用`rolling()`方法来创建滑动窗口对象，然后在其上应用聚合函数。

![](img/493fe8cc0b6621a5142b40c54402a869_27.png)

以下是实现步骤：
1.  **数据预处理**：在进行窗口计算前，**必须处理缺失值**，否则窗口内包含NaN会导致整个窗口的统计结果也为NaN。
2.  **创建滚动窗口对象**：使用`.rolling(window=窗口大小)`。
3.  **应用聚合函数**：例如`.min()`, `.max()`, `.mean()`, `.std()`。

![](img/493fe8cc0b6621a5142b40c54402a869_29.png)

```python
# 1. 预处理：删除缺失值
data_clean = df.dropna()

![](img/493fe8cc0b6621a5142b40c54402a869_31.png)

# 2. & 3. 创建窗口大小为10的滚动对象，并计算每个窗口的最小值
window_size = 10
rolling_min = data_clean['column_name'].rolling(window=window_size).min()
```

**注意**：对于前 `window_size - 1` 个数据点，由于窗口未填满，其计算结果会是NaN，这是正常现象。

![](img/493fe8cc0b6621a5142b40c54402a869_33.png)

### 计算多个窗口统计量

![](img/493fe8cc0b6621a5142b40c54402a869_35.png)

![](img/493fe8cc0b6621a5142b40c54402a869_37.png)

我们可以一次性为数据添加多个基于滑动窗口的统计列。

![](img/493fe8cc0b6621a5142b40c54402a869_39.png)

![](img/493fe8cc0b6621a5142b40c54402a869_41.png)

以下是如何同时计算最小值、最大值、均值和标准差：

```python
window_size = 10
data_clean = df.dropna()

![](img/493fe8cc0b6621a5142b40c54402a869_43.png)

data_clean['min'] = data_clean['target_column'].rolling(window=window_size).min()
data_clean['max'] = data_clean['target_column'].rolling(window=window_size).max()
data_clean['mean'] = data_clean['target_column'].rolling(window=window_size).mean()
data_clean['std'] = data_clean['target_column'].rolling(window=window_size).std()

![](img/493fe8cc0b6621a5142b40c54402a869_45.png)

# 查看结果（例如查看最后几行）
print(data_clean.tail())
```

![](img/493fe8cc0b6621a5142b40c54402a869_47.png)

![](img/493fe8cc0b6621a5142b40c54402a869_49.png)

## 总结

![](img/493fe8cc0b6621a5142b40c54402a869_51.png)

![](img/493fe8cc0b6621a5142b40c54402a869_53.png)

本节课中我们一起学习了时间序列分析的两个重要操作：
1.  **重采样 (`resample`)**：用于改变数据的时间频率（如日→周→月），需要配合聚合函数使用，并可通过`label`参数控制输出标签。
2.  **时间窗口/滑动窗口 (`rolling`)**：用于在固定长度的滑动窗口上计算统计指标（如移动平均、移动标准差）。**关键点**是操作前务必进行数据清洗，处理缺失值，否则会影响计算结果。

![](img/493fe8cc0b6621a5142b40c54402a869_55.png)

![](img/493fe8cc0b6621a5142b40c54402a869_57.png)

掌握这两种方法，能帮助你灵活地从不同时间维度观察和分析金融时间序列数据的趋势与波动。