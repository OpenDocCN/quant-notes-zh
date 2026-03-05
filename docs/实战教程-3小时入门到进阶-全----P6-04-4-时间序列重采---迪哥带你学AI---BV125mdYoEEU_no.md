# Python金融分析与量化交易实战教程：P6：04-4-时间序列重采样操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。你将学会如何改变数据的时间频率，以及如何通过滑动窗口计算移动统计指标，这是金融数据分析中处理时间序列数据的基础技能。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_1.png)

---

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_3.png)

## 重采样操作 🔄

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间粒度，这个过程称为重采样。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_5.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_7.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_9.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或按年进行统计。在Pandas中，这通过`resample`方法实现。

以下是重采样的基本步骤：
1.  指定新的时间频率（如‘W’代表周，‘M’代表月）。
2.  指定聚合方法（如计算均值、求和、第一个值等）。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_11.png)

```python
# 假设df是一个以天为索引的DataFrame
# 按周进行重采样，并计算每周的均值
weekly_data = df.resample(‘W’).mean()
```

执行上述代码后，数据将从按天统计汇总为按周统计。需要注意的是，原始数据中如果某些周的前几天没有数据，重采样后该周的结果可能从有数据的那一天开始计算。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_13.png)

除了按周重采样，也可以轻松切换到其他频率。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_15.png)

```python
# 按月进行重采样，并计算每月的第一个值
monthly_data = df.resample(‘M’).first()
```

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_17.png)

在重采样时，还有一个重要参数是`label`，它决定了新时间段的标签使用起始点（‘left’）还是结束点（‘right’）。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_19.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_21.png)

```python
# 使用每月最后一天作为标签（默认行为，符合观察逻辑）
monthly_data_right = df.resample(‘M’, label=‘right’).mean()

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_23.png)

# 使用每月第一天作为标签
monthly_data_left = df.resample(‘M’, label=‘left’).mean()
```

通常，在金融数据分析中，使用结束点（‘right’）作为标签更为常见和直观，因为它表示该时间段（如一个月）结束时汇总的结果。

---

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_25.png)

## 时间窗口（滑动窗口）操作 🪟

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_27.png)

上一节我们介绍了如何改变数据的时间频率，本节中我们来看看如何在固定的时间窗口内进行滑动计算，这是分析数据趋势和模式的有力工具。

时间窗口操作，也称为滑动窗口，是指定义一个固定长度的窗口（例如10天），让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都可以计算其内部的统计指标（如最小值、最大值、均值等）。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_29.png)

以下是实现滑动窗口操作的基本步骤：
1.  定义窗口大小（例如 `window=10`）。
2.  对目标数据列使用`.rolling()`方法创建窗口对象。
3.  对窗口对象应用聚合函数（如`.min()`, `.max()`, `.mean()`）。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_31.png)

```python
# 定义窗口大小为10
window_size = 10

# 对‘price’列创建滑动窗口，并计算每个窗口的最小值
rolling_min = df[‘price’].rolling(window=window_size).min()
```

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_33.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_35.png)

在执行窗口操作前，**必须处理数据中的缺失值**。如果窗口内存在缺失值（NaN），那么该窗口的统计结果也会是NaN。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_37.png)

```python
# 首先，剔除缺失值
df_clean = df.dropna()

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_39.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_41.png)

# 然后，在清洗后的数据上进行窗口计算
rolling_min_clean = df_clean[‘price’].rolling(window=window_size).min()
```

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_43.png)

初始的几个窗口（索引小于窗口大小）结果会是NaN，因为数据不足以填满一个完整的窗口，这是正常现象。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_45.png)

我们可以一次性为数据添加多个基于窗口的统计列。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_47.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_49.png)

```python
# 为清洗后的数据添加多个窗口统计指标
df_clean[‘min_10’] = df_clean[‘price’].rolling(window=10).min()
df_clean[‘max_10’] = df_clean[‘price’].rolling(window=10).max()
df_clean[‘mean_10’] = df_clean[‘price’].rolling(window=10).mean()
df_clean[‘std_10’] = df_clean[‘price’].rolling(window=10).std()

# 查看添加了统计列后的数据尾部
print(df_clean.tail())
```

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_51.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_53.png)

通过以上操作，我们就得到了每个滑动窗口内的最小值、最大值、均值和标准差，这有助于我们更细致地分析数据在短期内的波动与趋势。

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_55.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_57.png)

---

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_59.png)

![](img/9b5e3f826d0263ae530bb45cfc02d7ee_61.png)

本节课中我们一起学习了时间序列分析的两个关键操作：**重采样**与**时间窗口**。重采样帮助我们转换数据的时间频率以适应不同分析需求，而时间窗口操作则允许我们在滑动的数据片段上计算各种统计量，是分析局部趋势和模式的基石。记住，在进行窗口计算前，务必做好数据清洗，处理缺失值。