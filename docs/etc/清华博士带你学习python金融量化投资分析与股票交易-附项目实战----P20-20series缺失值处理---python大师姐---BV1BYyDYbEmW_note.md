# Python金融量化投资分析：P20：Series缺失值处理 📊

![](img/c9351dc8142d89fb757eec52285f8e19_1.png)

在本节课中，我们将要学习如何处理Pandas Series中的缺失数据。缺失数据在数据分析中很常见，如果不加以处理，可能会影响后续的计算和可视化。我们将介绍两种主要的处理方法：删除缺失值和填充缺失值。

上一节我们介绍了Series的基本操作，本节中我们来看看如何处理其中的缺失值。

## 判断缺失值

![](img/c9351dc8142d89fb757eec52285f8e19_3.png)

首先，我们需要能够识别Series中的缺失值。Pandas提供了两个函数来判断数据是否为缺失值。

以下是判断缺失值的函数：

![](img/c9351dc8142d89fb757eec52285f8e19_5.png)

*   **`SR.isnull()`**：此函数返回一个布尔型Series。如果原数据是缺失值（NaN），则对应位置返回`True`；否则返回`False`。
*   **`SR.notnull()`**：此函数与`isnull()`相反。如果原数据不是缺失值，则返回`True`；是缺失值则返回`False`。

```python
# 示例：判断缺失值
import pandas as pd
import numpy as np

![](img/c9351dc8142d89fb757eec52285f8e19_7.png)

SR = pd.Series([33, np.nan, 32, np.nan])
print(SR.isnull())
# 输出：0 False, 1 True, 2 False, 3 True
print(SR.notnull())
# 输出：0 True, 1 False, 2 True, 3 False
```

![](img/c9351dc8142d89fb757eec52285f8e19_9.png)

## 删除缺失值

![](img/c9351dc8142d89fb757eec52285f8e19_11.png)

第一种处理缺失值的方法是直接删除包含缺失数据的行。这种方法适用于缺失数据较少，或者缺失行对整体分析影响不大的情况。

![](img/c9351dc8142d89fb757eec52285f8e19_13.png)

以下是删除缺失值的方法：

*   **使用布尔索引**：结合`SR.notnull()`进行过滤，只保留非缺失值。
*   **使用`dropna()`方法**：`dropna()`方法会直接删除所有包含缺失值的行，更加便捷。

![](img/c9351dc8142d89fb757eec52285f8e19_15.png)

```python
# 方法一：使用布尔索引过滤
SR_filtered = SR[SR.notnull()]
print(SR_filtered)

# 方法二：使用dropna()方法
SR_dropped = SR.dropna()
print(SR_dropped)
```

## 填充缺失值

第二种处理缺失值的方法是填充。我们为缺失的位置赋予一个具体的值，而不是删除整行。这可以保持数据集的完整性，适用于需要连续数据进行分析的场景。

![](img/c9351dc8142d89fb757eec52285f8e19_17.png)

以下是填充缺失值的常用方法：

*   **填充固定值**：使用`fillna()`方法，将所有缺失值替换为一个指定的常数，例如0。
*   **填充统计值**：更常见的方法是使用数据的统计特征进行填充，例如使用该Series的**平均值（mean）**。`fillna()`方法可以接受一个标量值作为参数。

![](img/c9351dc8142d89fb757eec52285f8e19_19.png)

![](img/c9351dc8142d89fb757eec52285f8e19_21.png)

```python
# 填充固定值，例如0
SR_filled_zero = SR.fillna(0)
print(SR_filled_zero)

# 填充平均值
mean_value = SR.mean() # Pandas的mean()方法会自动忽略NaN进行计算
SR_filled_mean = SR.fillna(mean_value)
print(SR_filled_mean)
```

**重要提示**：Pandas中的数据处理方法（如`dropna()`, `fillna()`）默认**不会**修改原始Series对象，而是返回一个新的Series。如果需要保存处理后的结果，必须将其赋值给一个新的变量。

![](img/c9351dc8142d89fb757eec52285f8e19_23.png)

本节课中我们一起学习了如何处理Pandas Series中的缺失数据。我们掌握了两种核心方法：使用`dropna()`删除缺失值，以及使用`fillna()`并配合统计量（如平均值）来填充缺失值。理解并妥善处理缺失值是进行数据清洗、保证分析结果可靠性的关键一步。