# Python金融分析与量化交易实战：P5：04-4-时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。这些操作是金融数据分析的基础，能帮助我们以不同的时间粒度观察数据，并计算滑动窗口内的统计指标。

![](img/62dbd168af90c43cca1e2e78382e473d_1.png)

![](img/62dbd168af90c43cca1e2e78382e473d_3.png)

## 重采样操作 🔄

![](img/62dbd168af90c43cca1e2e78382e473d_5.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/62dbd168af90c43cca1e2e78382e473d_7.png)

![](img/62dbd168af90c43cca1e2e78382e473d_9.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或按年统计。在Pandas中，这通过`resample`方法实现。你需要指定新的时间频率，并选择聚合数据的方式（如均值、第一个值、中位数等）。

![](img/62dbd168af90c43cca1e2e78382e473d_11.png)

以下是重采样的基本代码示例：
```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

### 重采样的标签设置

![](img/62dbd168af90c43cca1e2e78382e473d_13.png)

![](img/62dbd168af90c43cca1e2e78382e473d_15.png)

在重采样时，我们还可以指定如何标记聚合后的数据点。默认情况下，标签使用时间窗口的右边界（`label='right'`），但也可以设置为左边界（`label='left'`）。

以下是两种标签设置的对比：
```python
# 使用右边界标签（默认）
resampled_right = df.resample('M', label='right').mean()
# 使用左边界标签
resampled_left = df.resample('M', label='left').mean()
```
`label='right'`意味着聚合后的数据点以其时间窗口的结束日期命名（例如，1月31日代表整个1月份的数据）。`label='left'`则使用时间窗口的开始日期命名。在金融分析中，通常使用`label='right'`，因为它更符合我们观察数据的逻辑。

![](img/62dbd168af90c43cca1e2e78382e473d_17.png)

![](img/62dbd168af90c43cca1e2e78382e473d_19.png)

![](img/62dbd168af90c43cca1e2e78382e473d_21.png)

## 时间窗口（滑动窗口）操作 ⏩

![](img/62dbd168af90c43cca1e2e78382e473d_23.png)

接下来，我们学习时间窗口操作，也称为滑动窗口操作。这不是改变数据频率，而是在一个固定长度的窗口上滑动，并计算窗口内数据的统计量。

例如，我们有一个100天的数据序列。我们可以定义一个长度为10天的窗口。第一个窗口包含第1到第10天的数据，第二个窗口包含第2到第11天的数据，以此类推，每次向后滑动一天。

![](img/62dbd168af90c43cca1e2e78382e473d_25.png)

### 执行滑动窗口计算

![](img/62dbd168af90c43cca1e2e78382e473d_27.png)

在Pandas中，我们使用`rolling`方法来实现滑动窗口计算。

![](img/62dbd168af90c43cca1e2e78382e473d_29.png)

以下是执行滑动窗口计算并统计最小值的步骤：
```python
# 定义窗口大小
window_size = 10
# 对指定列进行滚动计算，并求每个窗口的最小值
rolling_min = df['column_name'].rolling(window=window_size).min()
```

![](img/62dbd168af90c43cca1e2e78382e473d_31.png)

### 处理缺失值

![](img/62dbd168af90c43cca1e2e78382e473d_33.png)

在进行窗口操作前，**必须处理数据中的缺失值**。因为如果窗口内存在缺失值（NaN），许多统计计算的结果也会是NaN。

![](img/62dbd168af90c43cca1e2e78382e473d_35.png)

以下是处理缺失值的代码：
```python
# 删除含有缺失值的行
df_clean = df.dropna()
# 在清理后的数据上进行滚动计算
rolling_min_clean = df_clean['column_name'].rolling(window=window_size).min()
```

![](img/62dbd168af90c43cca1e2e78382e473d_37.png)

![](img/62dbd168af90c43cca1e2e78382e473d_39.png)

![](img/62dbd168af90c43cca1e2e78382e473d_41.png)

### 计算多个窗口统计量

我们可以同时对每个窗口计算多个统计指标，如最小值、最大值、均值和标准差。

![](img/62dbd168af90c43cca1e2e78382e473d_43.png)

![](img/62dbd168af90c43cca1e2e78382e473d_45.png)

以下是计算多个统计指标的示例：
```python
# 在清理后的数据上计算
df_clean['min'] = df_clean['column_name'].rolling(window=window_size).min()
df_clean['max'] = df_clean['column_name'].rolling(window=window_size).max()
df_clean['mean'] = df_clean['column_name'].rolling(window=window_size).mean()
df_clean['std'] = df_clean['column_name'].rolling(window=window_size).std()
# 查看结果
print(df_clean.tail())
```
这样，我们就得到了每个滑动窗口内的多个核心统计值。

![](img/62dbd168af90c43cca1e2e78382e473d_47.png)

## 总结 📝

![](img/62dbd168af90c43cca1e2e78382e473d_49.png)

![](img/62dbd168af90c43cca1e2e78382e473d_51.png)

![](img/62dbd168af90c43cca1e2e78382e473d_53.png)

本节课中我们一起学习了时间序列的两个重要操作：
1.  **重采样**：使用`df.resample()`方法改变数据的时间频率（如从天到周），并可选择`label`参数来设置聚合点的标签。
2.  **时间窗口（滑动窗口）**：使用`df.rolling()`方法在固定长度的窗口上滑动，计算窗口内的统计指标（如最小值、最大值等）。**关键点**是操作前务必使用`df.dropna()`清理缺失值。

![](img/62dbd168af90c43cca1e2e78382e473d_55.png)

![](img/62dbd168af90c43cca1e2e78382e473d_57.png)

掌握这些操作，能让你更灵活地从不同时间维度分析和理解金融数据序列。