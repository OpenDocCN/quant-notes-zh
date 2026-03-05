# 机器学习与量化交易：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。你将学会如何改变数据的时间频率，以及如何通过滑动窗口计算数据的统计特征，这些是金融数据分析中处理时间序列数据的基础技能。

![](img/7dd7d392adde28319b2f1a3e4683697b_1.png)

---

![](img/7dd7d392adde28319b2f1a3e4683697b_3.png)

## 重采样操作 🔄

![](img/7dd7d392adde28319b2f1a3e4683697b_5.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间粒度，即重采样。

![](img/7dd7d392adde28319b2f1a3e4683697b_7.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_9.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或以年为统计单位。在Pandas中，这通过`resample`方法实现。

![](img/7dd7d392adde28319b2f1a3e4683697b_11.png)

以下是重采样的基本步骤：

![](img/7dd7d392adde28319b2f1a3e4683697b_13.png)

1.  指定新的时间频率。
2.  指定聚合数据时使用的统计方法（如均值、第一个值、中位数等）。

例如，将日数据重采样为周数据，并计算每周的均值：
```python
# 假设df是一个以日期为索引的DataFrame
weekly_data = df.resample('W').mean()
```

### 重采样的标签设置

![](img/7dd7d392adde28319b2f1a3e4683697b_15.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_17.png)

在重采样时，我们还可以指定如何标记聚合后的数据点。默认情况下，标签使用时间窗口的右边界（`label='right'`），但也可以设置为左边界（`label='left'`）。

*   **`label='right'`**：使用时间窗口的结束日期作为标签。例如，汇总1月1日至1月7日的数据，标签显示为1月7日。
*   **`label='left'`**：使用时间窗口的开始日期作为标签。例如，汇总1月1日至1月7日的数据，标签显示为1月1日。

![](img/7dd7d392adde28319b2f1a3e4683697b_19.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_21.png)

选择哪种标签取决于分析逻辑。通常，使用右边界（`label='right'`）更符合观察习惯，因为它表示截至该日期为止的统计结果。

![](img/7dd7d392adde28319b2f1a3e4683697b_23.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_25.png)

---

## 时间窗口操作 🪟

了解了如何改变数据频率后，我们来看看如何通过滑动窗口来分析数据的局部特征。

时间窗口操作，也称为滑动窗口，是指在一个序列上，以一个固定长度的窗口（如10天）进行滑动，并对每个窗口内的数据执行计算。例如，第一个窗口包含第1天到第10天的数据，第二个窗口包含第2天到第11天的数据，依此类推。

![](img/7dd7d392adde28319b2f1a3e4683697b_27.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_29.png)

在Pandas中，这通过`rolling`方法实现。

以下是执行时间窗口操作的基本步骤：

![](img/7dd7d392adde28319b2f1a3e4683697b_31.png)

1.  定义窗口大小（例如 `window=10`）。
2.  对目标数据列应用`rolling`方法。
3.  指定要在每个窗口上执行的聚合函数（如`.min()`, `.max()`, `.mean()`, `.std()`）。

![](img/7dd7d392adde28319b2f1a3e4683697b_33.png)

```python
# 计算10天窗口内的最小值
rolling_min = df['column_name'].rolling(window=10).min()
```

![](img/7dd7d392adde28319b2f1a3e4683697b_35.png)

### 处理缺失值的重要提示

![](img/7dd7d392adde28319b2f1a3e4683697b_37.png)

在进行窗口计算前，**必须处理原始数据中的缺失值（NaN）**。因为如果窗口内存在NaN，许多聚合计算的结果也会是NaN。

![](img/7dd7d392adde28319b2f1a3e4683697b_39.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_41.png)

以下是处理缺失值的步骤：
```python
# 1. 删除含有缺失值的行
df_clean = df.dropna()

![](img/7dd7d392adde28319b2f1a3e4683697b_43.png)

# 2. 在清洗后的数据上进行窗口计算
rolling_min_clean = df_clean['column_name'].rolling(window=10).min()
```

![](img/7dd7d392adde28319b2f1a3e4683697b_45.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_47.png)

### 计算多个窗口统计量

![](img/7dd7d392adde28319b2f1a3e4683697b_49.png)

我们可以一次性为数据添加多个基于窗口的统计列，以便进行综合分析。

![](img/7dd7d392adde28319b2f1a3e4683697b_51.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_53.png)

以下是为数据添加最小值、最大值、均值和标准差的示例：
```python
df_clean['min_10'] = df_clean['column_name'].rolling(window=10).min()
df_clean['max_10'] = df_clean['column_name'].rolling(window=10).max()
df_clean['mean_10'] = df_clean['column_name'].rolling(window=10).mean()
df_clean['std_10'] = df_clean['column_name'].rolling(window=10).std()
```

![](img/7dd7d392adde28319b2f1a3e4683697b_55.png)

执行上述代码后，`df_clean` DataFrame 将新增四列，分别代表每个时间点其前10天（包含当前点）窗口内的各项统计指标。

![](img/7dd7d392adde28319b2f1a3e4683697b_57.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_59.png)

---

## 总结 📝

![](img/7dd7d392adde28319b2f1a3e4683697b_61.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_63.png)

本节课中我们一起学习了时间序列数据处理的两个关键技术：

![](img/7dd7d392adde28319b2f1a3e4683697b_65.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_67.png)

1.  **重采样**：使用`df.resample(freq).agg_func()`来改变数据的时间频率（如日变周、月），并可通过`label`参数控制聚合结果的标签。
2.  **时间窗口操作**：使用`df[‘col’].rolling(window=n).agg_func()`来创建滑动窗口，计算数据在局部窗口内的统计特征（如滚动均值、标准差）。**切记，在进行窗口计算前，务必先使用`df.dropna()`等方法处理数据中的缺失值。**

![](img/7dd7d392adde28319b2f1a3e4683697b_69.png)

![](img/7dd7d392adde28319b2f1a3e4683697b_71.png)

掌握重采样和窗口操作，能帮助你从不同时间维度和局部视角深入分析金融时间序列数据，为后续的建模和策略开发打下坚实基础。