# 量化交易教程：P19：Series缺失值处理

![](img/1925c812c9a68be911fc0da12878aa8b_1.png)

在本节课中，我们将要学习如何处理Pandas Series数据结构中的缺失值。缺失值是数据分析中常见的问题，掌握其处理方法对于后续的数据清洗、分析和建模至关重要。

上一节我们介绍了Series的基本概念和操作，本节中我们来看看当数据中存在缺失值时，我们该如何应对。

## 缺失值的识别

在Series中，缺失数据通常以`NaN`（Not a Number）的形式出现。首先，我们需要能够识别出哪些位置存在缺失值。

![](img/1925c812c9a68be911fc0da12878aa8b_3.png)

Pandas提供了两个函数来判断缺失值：
*   `isnull()`: 用于判断每个元素是否为缺失值。
*   `notnull()`: 用于判断每个元素是否**不是**缺失值。

这两个函数都会返回一个布尔类型的Series。

![](img/1925c812c9a68be911fc0da12878aa8b_5.png)

以下是判断缺失值的代码示例：
```python
import pandas as pd
import numpy as np

# 创建一个包含NaN值的Series
sr = pd.Series([33, np.nan, 32, np.nan])
print("原始Series:")
print(sr)

![](img/1925c812c9a68be911fc0da12878aa8b_7.png)

# 使用isnull()判断缺失值
print("\n使用isnull()判断:")
print(sr.isnull())

![](img/1925c812c9a68be911fc0da12878aa8b_9.png)

# 使用notnull()判断非缺失值
print("\n使用notnull()判断:")
print(sr.notnull())
```
执行上述代码，`isnull()`会在`NaN`的位置返回`True`，`notnull()`则会在非`NaN`的位置返回`True`。

## 缺失值的处理方法

![](img/1925c812c9a68be911fc0da12878aa8b_11.png)

识别出缺失值后，我们主要有两种处理思路：删除缺失值或填充缺失值。以下是具体的操作方法。

![](img/1925c812c9a68be911fc0da12878aa8b_13.png)

### 方法一：删除缺失值

如果缺失值数量不多，或者缺失行对整体分析影响不大，我们可以选择直接删除包含缺失值的行。

![](img/1925c812c9a68be911fc0da12878aa8b_15.png)

Pandas提供了`dropna()`函数来实现这个功能。

以下是删除缺失值的代码示例：
```python
# 使用dropna()删除缺失值所在行
sr_dropped = sr.dropna()
print("删除缺失值后的Series:")
print(sr_dropped)
```
执行后，新的`sr_dropped`中将只包含原始数据中非`NaN`的值。

**注意**：`dropna()`默认会返回一个新的Series，不会修改原始数据。

### 方法二：填充缺失值

如果直接删除数据会导致信息损失过多，或者我们希望保持数据集的完整性，可以选择填充缺失值。最常用的方法是使用`fillna()`函数。

![](img/1925c812c9a68be911fc0da12878aa8b_17.png)

以下是填充缺失值的几种常见方式：

![](img/1925c812c9a68be911fc0da12878aa8b_19.png)

1.  **填充为固定值**：例如，将所有缺失值填充为0。
    ```python
    sr_filled_zero = sr.fillna(0)
    print("填充为0后的Series:")
    print(sr_filled_zero)
    ```

![](img/1925c812c9a68be911fc0da12878aa8b_21.png)

2.  **填充为统计值**：为了减少对数据分布的影响，常使用均值、中位数等统计量进行填充。
    ```python
    # 计算非缺失值的平均值
    mean_value = sr.mean()
    print(f"非缺失值的平均值为: {mean_value}")

    # 使用平均值填充缺失值
    sr_filled_mean = sr.fillna(mean_value)
    print("使用平均值填充后的Series:")
    print(sr_filled_mean)
    ```
    **注意**：Series的`mean()`、`median()`等统计函数在计算时会自动忽略`NaN`值，这为我们的填充操作提供了便利。

**重要提示**：无论是`dropna()`还是`fillna()`，默认都是返回处理后的**新对象**，而**不会**改变原始的Series数据。如果需要保存处理结果，必须将其赋值给一个新的变量。

## 总结

![](img/1925c812c9a68be911fc0da12878aa8b_23.png)

本节课中我们一起学习了Pandas Series缺失值的处理方法。我们首先介绍了如何使用`isnull()`和`notnull()`函数来识别缺失值。接着，我们探讨了两种核心的处理策略：一是使用`dropna()`函数直接删除包含缺失值的行；二是使用`fillna()`函数将缺失值填充为指定的固定值（如0）或更有意义的统计值（如该Series的均值）。理解并熟练运用这些方法，是进行高质量数据预处理的关键一步。