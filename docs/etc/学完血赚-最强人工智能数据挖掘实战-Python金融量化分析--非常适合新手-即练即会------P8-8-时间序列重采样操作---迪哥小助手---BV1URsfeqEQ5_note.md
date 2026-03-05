# Python金融量化分析：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。重采样用于改变数据的时间频率，而窗口操作则用于在滑动的时间段内进行统计分析。掌握这两个技巧对于金融数据分析和特征工程至关重要。

## 重采样操作 🔄

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_1.png)

上一节我们介绍了时间序列的基础知识，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_3.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据可能是按天统计的，我们可以将其调整为按周、按月或按年进行统计。

在重采样过程中，你需要指定至少一个参数，即新的时间单位。同时，你还需要指定一个聚合函数，用于决定如何合并新时间单位内的数据，例如计算均值、第一个值或总和等。

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_5.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_6.png)

以下是重采样的基本代码示例：

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_7.png)

```python
# 假设 df 是一个按天索引的 DataFrame
# 按周进行重采样，并计算每周的均值
weekly_resampled = df.resample('W').mean()

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_9.png)

# 按月进行重采样，并计算每月的总和
monthly_resampled = df.resample('M').sum()
```

### 标签参数：`label`

在重采样时，还有一个重要的参数是 `label`，它决定了新时间段的标签是使用该时间段的起始点（`left`）还是结束点（`right`）。

以下是 `label` 参数的使用示例：

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_11.png)

```python
# 使用时间段的最后一天作为标签（默认）
resampled_right = df.resample('M', label='right').mean()

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_12.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_13.png)

# 使用时间段的第一天作为标签
resampled_left = df.resample('M', label='left').mean()
```

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_15.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_16.png)

选择 `left` 或 `right` 取决于你的分析逻辑。通常，使用 `label='right'`（即用时间段的最后一天代表该时间段）更符合观察习惯，因为它描述的是该时间段结束时的汇总情况。

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_17.png)

## 时间窗口操作 ⏱️

了解了如何改变数据频率后，我们来看看如何在时间序列上应用滑动窗口进行统计分析。

时间窗口操作，也称为滑动窗口，是指定义一个固定长度的时间窗口（例如10天），然后让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都可以计算其内部的统计指标（如最小值、最大值、均值等）。

以下是创建滑动窗口并计算统计值的基本步骤：

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_19.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_20.png)

1.  **定义窗口大小**：例如，`window_size = 10`。
2.  **应用滚动方法**：使用 `.rolling()` 方法。
3.  **计算统计量**：对每个窗口应用聚合函数。

核心代码如下：

```python
import pandas as pd

# 假设 data 是包含时间序列的 DataFrame
# 定义窗口大小
window_size = 10

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_22.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_23.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_24.png)

# 对某一列数据应用滚动窗口，并计算每个窗口的最小值
rolling_min = data['column_name'].rolling(window=window_size).min()
```

### 数据预处理的重要性

在进行窗口操作前，**必须处理缺失值**。如果窗口内存在缺失值（NaN），许多统计计算的结果也会是NaN。

以下是处理缺失值的示例：

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_26.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_27.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_28.png)

```python
# 删除缺失值
data_cleaned = data.dropna()

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_29.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_30.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_31.png)

# 然后对清理后的数据进行窗口操作
rolling_min_cleaned = data_cleaned['column_name'].rolling(window=window_size).min()
```

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_33.png)

### 计算多个窗口统计量

我们可以一次性为每个窗口计算多个统计指标，并将其作为新列添加到数据中。

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_35.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_37.png)

以下是同时计算最小值、最大值、均值和标准差的示例：

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_38.png)

```python
# 在清理后的数据上操作
data_cleaned['min'] = data_cleaned['column_name'].rolling(window=window_size).min()
data_cleaned['max'] = data_cleaned['column_name'].rolling(window=window_size).max()
data_cleaned['mean'] = data_cleaned['column_name'].rolling(window=window_size).mean()
data_cleaned['std'] = data_cleaned['column_name'].rolling(window=window_size).std()

# 查看结果
print(data_cleaned[['min', 'max', 'mean', 'std']].tail())
```

这样，我们就得到了每个滑动窗口内的多种统计特征，这些特征可以用于更深入的数据分析或机器学习模型。

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_40.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_41.png)

## 总结 📝

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_42.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_43.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_44.png)

本节课中我们一起学习了时间序列分析的两个重要操作：
1.  **重采样**：通过 `df.resample()` 方法改变数据的时间频率（如从天到周、月），并可使用 `label` 参数控制时间标签的显示方式。
2.  **时间窗口操作**：通过 `df.rolling()` 方法创建滑动窗口，以计算窗口内的统计指标（如最小值、最大值、均值等）。**切记，在进行窗口操作前，务必先处理数据中的缺失值**。

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_45.png)

![](img/ab1c094ec7d40a6cd01d8635e7043e9e_47.png)

这两个工具能帮助你从不同时间维度理解和分析金融时间序列数据，是构建有效量化模型的基础。