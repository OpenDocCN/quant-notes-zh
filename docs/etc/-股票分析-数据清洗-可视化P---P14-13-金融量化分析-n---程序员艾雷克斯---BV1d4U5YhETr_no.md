# Python金融量化投资分析与股票交易：P14：13 金融量化分析-numpy-array布尔型索引 📊

![](img/115c5468ded78665ead69d508461eead_1.png)

![](img/115c5468ded78665ead69d508461eead_3.png)

在本节课中，我们将要学习NumPy中一个非常强大且高效的功能——布尔型索引。通过它，我们可以轻松地根据条件筛选数组中的元素，这比使用Python内置的`filter`函数要简洁得多。

![](img/115c5468ded78665ead69d508461eead_5.png)

![](img/115c5468ded78665ead69d508461eead_6.png)

## 概述

![](img/115c5468ded78665ead69d508461eead_8.png)

上一节我们介绍了NumPy数组的常规索引和切片。本节中，我们来看看如何使用布尔型索引来快速、直观地筛选出数组中满足特定条件的元素。

![](img/115c5468ded78665ead69d508461eead_10.png)

![](img/115c5468ded78665ead69d508461eead_12.png)

![](img/115c5468ded78665ead69d508461eead_14.png)

## 布尔型索引的原理

![](img/115c5468ded78665ead69d508461eead_16.png)

![](img/115c5468ded78665ead69d508461eead_18.png)

布尔型索引的原理可以分为两步：
1.  首先，通过数组与标量的比较运算，得到一个与原数组形状相同的布尔数组。
2.  然后，将这个布尔数组作为索引传入原数组，即可提取出所有对应位置为`True`的元素。

![](img/115c5468ded78665ead69d508461eead_20.png)

以下是其核心概念的代码描述：

![](img/115c5468ded78665ead69d508461eead_22.png)

```python
import numpy as np

![](img/115c5468ded78665ead69d508461eead_24.png)

# 创建一个示例数组
a = np.array([8, 3, 6, 2, 9, 1])

![](img/115c5468ded78665ead69d508461eead_26.png)

# 第一步：比较运算，生成布尔数组
bool_mask = a > 5
# bool_mask 结果为：array([ True, False,  True, False,  True, False])

![](img/115c5468ded78665ead69d508461eead_28.png)

# 第二步：使用布尔数组进行索引
result = a[bool_mask]
# result 结果为：array([8, 6, 9])
```

![](img/115c5468ded78665ead69d508461eead_30.png)

![](img/115c5468ded78665ead69d508461eead_31.png)

![](img/115c5468ded78665ead69d508461eead_33.png)

实际上，我们通常会将这两步合并为一行简洁的代码：`a[a > 5]`。

![](img/115c5468ded78665ead69d508461eead_35.png)

![](img/115c5468ded78665ead69d508461eead_37.png)

## 与Python基础方法的对比

![](img/115c5468ded78665ead69d508461eead_39.png)

以下是使用Python内置`filter`函数和NumPy布尔型索引筛选大于5的数的对比。

![](img/115c5468ded78665ead69d508461eead_41.png)

![](img/115c5468ded78665ead69d508461eead_43.png)

**使用Python `filter`函数：**

![](img/115c5468ded78665ead69d508461eead_45.png)

```python
a_list = [8, 3, 6, 2, 9, 1]
result = list(filter(lambda x: x > 5, a_list))
# result 结果为：[8, 6, 9]
```

![](img/115c5468ded78665ead69d508461eead_47.png)

**使用NumPy布尔型索引：**

![](img/115c5468ded78665ead69d508461eead_49.png)

![](img/115c5468ded78665ead69d508461eead_51.png)

```python
a_array = np.array([8, 3, 6, 2, 9, 1])
result = a_array[a_array > 5]
# result 结果为：array([8, 6, 9])
```

![](img/115c5468ded78665ead69d508461eead_53.png)

![](img/115c5468ded78665ead69d508461eead_55.png)

可以看到，NumPy的写法更加直观和简洁。

![](img/115c5468ded78665ead69d508461eead_56.png)

![](img/115c5468ded78665ead69d508461eead_58.png)

## 组合条件筛选

![](img/115c5468ded78665ead69d508461eead_60.png)

有时我们需要根据多个条件来筛选数据。NumPy支持使用位运算符来组合多个布尔条件。

![](img/115c5468ded78665ead69d508461eead_62.png)

![](img/115c5468ded78665ead69d508461eead_64.png)

**注意**：在组合多个布尔数组时，应使用位运算符 `&` (与)、`|` (或)，而不是Python关键字 `and`、`or`，并且每个条件建议用括号括起来以确保运算顺序正确。

以下是组合条件筛选的示例。

**筛选大于5的偶数：**

![](img/115c5468ded78665ead69d508461eead_66.png)

```python
a = np.array([8, 3, 6, 2, 9, 1])
# 条件：大于5 且 是偶数
result = a[(a > 5) & (a % 2 == 0)]
# result 结果为：array([8, 6])
```

**筛选大于5的数 或 所有偶数：**

![](img/115c5468ded78665ead69d508461eead_68.png)

```python
a = np.array([8, 3, 6, 2, 9, 1])
# 条件：大于5 或 是偶数
result = a[(a > 5) | (a % 2 == 0)]
# result 结果为：array([8, 6, 2, 9])
```

![](img/115c5468ded78665ead69d508461eead_70.png)

## 总结

![](img/115c5468ded78665ead69d508461eead_72.png)

![](img/115c5468ded78665ead69d508461eead_74.png)

本节课中我们一起学习了NumPy的布尔型索引。我们了解了它的两步工作原理：先通过比较运算生成布尔掩码，再用该掩码提取数据。我们对比了其与Python传统方法的简洁性，并学习了如何使用位运算符 `&` 和 `|` 来组合多个筛选条件。掌握布尔型索引能极大地提升数据处理和分析的效率。