# Python金融分析与量化交易实战：P5：5.4.4-时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。这些操作能帮助我们将数据转换到不同的时间尺度，并进行滑动统计分析，是金融数据分析中非常实用的技巧。

![](img/c5b73f547aff7f3722b6158e614c43c5_1.png)

---

![](img/c5b73f547aff7f3722b6158e614c43c5_3.png)

## 重采样操作

![](img/c5b73f547aff7f3722b6158e614c43c5_5.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_7.png)

上一节我们介绍了时间序列的基础处理，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/c5b73f547aff7f3722b6158e614c43c5_9.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或按年统计。

![](img/c5b73f547aff7f3722b6158e614c43c5_11.png)

以下是重采样的基本步骤：

![](img/c5b73f547aff7f3722b6158e614c43c5_13.png)

1.  使用 `resample()` 方法，并指定目标频率（如 `‘W’` 表示周，`‘M’` 表示月）。
2.  指定聚合函数，如 `mean()`（均值）、`first()`（第一个值）、`median()`（中位数）等，以确定如何合并新时间单位内的数据。

核心操作代码如下：
```python
# 假设 df 是一个包含日期索引的 DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

![](img/c5b73f547aff7f3722b6158e614c43c5_15.png)

### 重采样的标签设置

在重采样时，我们还可以指定如何标记聚合后的数据点。这通过 `label` 参数控制。

![](img/c5b73f547aff7f3722b6158e614c43c5_17.png)

以下是 `label` 参数的两个主要选项：

![](img/c5b73f547aff7f3722b6158e614c43c5_19.png)

*   **`label=‘right’`**：默认值。使用时间窗口的**结束日期**作为该窗口数据的标签。例如，一个月的窗口（1月1日至1月31日）会被标记为 “1月31日”。
*   **`label=‘left’`**：使用时间窗口的**开始日期**作为标签。同一个月的窗口会被标记为 “1月1日”。

选择哪种标签取决于分析逻辑。通常，使用 `label=‘right’` 更符合观察习惯，因为它表示截至该日期为止的统计结果。

![](img/c5b73f547aff7f3722b6158e614c43c5_21.png)

---

![](img/c5b73f547aff7f3722b6158e614c43c5_23.png)

## 时间窗口（滑动窗口）操作

![](img/c5b73f547aff7f3722b6158e614c43c5_25.png)

接下来，我们学习时间窗口操作。如果说重采样是改变观测的“尺子”，那么时间窗口就是一把“滑动的尺子”，用于计算一系列连续时间段内的统计指标。

![](img/c5b73f547aff7f3722b6158e614c43c5_27.png)

时间窗口操作是指定义一个固定长度的窗口（例如10天），让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都计算其内部数据的统计值（如最小值、最大值、均值等）。

![](img/c5b73f547aff7f3722b6158e614c43c5_29.png)

以下是实现滑动窗口操作的步骤：

![](img/c5b73f547aff7f3722b6158e614c43c5_31.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_33.png)

1.  使用 `rolling()` 方法，并指定窗口大小（如 `window=10`）。
2.  对生成的 `Rolling` 对象应用聚合函数（如 `.min()`, `.max()`, `.mean()`, `.std()`）。

![](img/c5b73f547aff7f3722b6158e614c43c5_35.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_37.png)

核心操作代码如下：
```python
# 对 ‘price’ 列计算10天的滑动窗口最小值
rolling_min = df[‘price’].rolling(window=10).min()
```

### 处理缺失值的重要提示

![](img/c5b73f547aff7f3722b6158e614c43c5_39.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_41.png)

在进行窗口计算前，**必须处理原始数据中的缺失值**。因为如果窗口内包含缺失值（NaN），那么该窗口的统计结果通常也会是 NaN。

![](img/c5b73f547aff7f3722b6158e614c43c5_43.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_45.png)

一个常见的预处理步骤是删除或填充缺失值：
```python
# 删除包含缺失值的行
df_clean = df.dropna()
# 然后对 df_clean 进行窗口计算
rolling_result = df_clean[‘price’].rolling(window=10).mean()
```

![](img/c5b73f547aff7f3722b6158e614c43c5_47.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_49.png)

### 创建多指标窗口统计

![](img/c5b73f547aff7f3722b6158e614c43c5_51.png)

我们可以方便地为一个数据集计算多个窗口统计指标，并将结果作为新列添加到数据框中。

![](img/c5b73f547aff7f3722b6158e614c43c5_53.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_55.png)

以下是创建包含最小值、最大值、均值和标准差的窗口统计列的示例：
```python
window_size = 10
df_clean[‘rolling_min’] = df_clean[‘price’].rolling(window=window_size).min()
df_clean[‘rolling_max’] = df_clean[‘price’].rolling(window=window_size).max()
df_clean[‘rolling_mean’] = df_clean[‘price’].rolling(window=window_size).mean()
df_clean[‘rolling_std’] = df_clean[‘price’].rolling(window=window_size).std()
```

![](img/c5b73f547aff7f3722b6158e614c43c5_57.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_59.png)

---

## 总结

![](img/c5b73f547aff7f3722b6158e614c43c5_61.png)

本节课中我们一起学习了时间序列分析的两个强大工具：

![](img/c5b73f547aff7f3722b6158e614c43c5_63.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_65.png)

1.  **重采样**：通过 `resample()` 方法改变数据的时间频率（如日变周、月），并使用聚合函数（如 `mean()`）来概括每个新时间段内的数据。需要注意 `label` 参数决定了聚合结果的标签日期。
2.  **时间窗口（滑动窗口）**：通过 `rolling()` 方法在数据上创建一个固定长度的滑动窗口，用于计算连续的局部统计量（如滚动均值、标准差）。**关键前提**是必须预先处理好数据中的缺失值，否则会导致计算结果无效。

![](img/c5b73f547aff7f3722b6158e614c43c5_67.png)

![](img/c5b73f547aff7f3722b6158e614c43c5_69.png)

掌握这两种操作，能够帮助你从不同时间维度和局部视角更深入地分析金融时间序列数据。