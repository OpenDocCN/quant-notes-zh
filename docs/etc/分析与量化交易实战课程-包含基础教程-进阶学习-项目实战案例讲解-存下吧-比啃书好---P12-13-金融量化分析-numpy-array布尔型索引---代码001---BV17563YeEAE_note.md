# 量化交易教程：P12：13：NumPy数组的布尔型索引 🔍

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_1.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_3.png)

在本节课中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。通过它，我们可以轻松地根据条件筛选数组中的元素，而无需编写复杂的循环或使用`filter`函数。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_5.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_6.png)

上一节我们介绍了数组的常规索引和切片，本节中我们来看看如何使用布尔值作为索引来筛选数据。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_8.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_10.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_12.png)

## 概述：什么是布尔型索引？

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_14.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_16.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_18.png)

布尔型索引允许我们使用一个由布尔值（True/False）构成的数组或列表，来从原数组中选取对应位置为True的元素。其核心原理分为两步：
1.  通过数组与标量的比较运算，生成一个布尔数组。
2.  将这个布尔数组作为索引传入原数组，即可提取出所有对应True位置的元素。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_20.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_22.png)

## 从基础方法到NumPy方法

以下是筛选数组中大于5的元素的两种方法对比。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_24.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_26.png)

**传统Python方法（使用`filter`）**
对于熟悉Python基础的同学，可能会想到使用`filter`函数和`lambda`表达式。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_28.png)

```python
import numpy as np

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_30.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_31.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_33.png)

# 创建一个示例列表
a_list = [8, 3, 6, 1, 10]
# 使用filter筛选大于5的元素
result = list(filter(lambda x: x > 5, a_list))
print(result)  # 输出: [8, 6, 10]
```

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_35.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_37.png)

**NumPy布尔型索引方法**
使用NumPy数组，筛选操作可以变得极其简洁。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_39.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_41.png)

```python
# 将列表转换为NumPy数组
a = np.array([8, 3, 6, 1, 10])
# 使用布尔型索引筛选大于5的元素
result = a[a > 5]
print(result)  # 输出: [8 6 10]
```
代码 `a[a > 5]` 就是布尔型索引的典型应用，它比传统方法简洁高效得多。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_43.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_45.png)

## 深入理解原理

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_47.png)

为了更好地理解 `a[a > 5]` 是如何工作的，我们可以将其拆解为两步：

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_49.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_51.png)

1.  **生成布尔数组**：`a > 5` 是一个数组与标量的比较运算，它会返回一个与原数组`a`形状相同的布尔数组。
    ```python
    bool_mask = a > 5
    print(bool_mask)  # 输出: [ True False  True False  True]
    ```
2.  **应用布尔索引**：将上一步得到的布尔数组 `bool_mask` 放入原数组的索引位置 `a[bool_mask]`，NumPy会自动取出所有对应`True`位置的元素。
    ```python
    result = a[bool_mask]
    print(result)  # 输出: [8 6 10]
    ```
这就是布尔型索引的底层逻辑：**传入的布尔数组长度必须与原数组一致，True的位置将被选中，False的位置将被过滤掉**。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_53.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_55.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_56.png)

## 组合条件筛选

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_58.png)

布尔型索引的强大之处在于可以方便地组合多个筛选条件。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_60.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_62.png)

**示例1：筛选大于5的偶数（“与”关系）**
我们需要同时满足“大于5”和“是偶数”两个条件。

```python
a = np.array([8, 3, 6, 1, 10])
# 使用 & 运算符组合条件，注意每个条件要用括号括起来
result = a[(a > 5) & (a % 2 == 0)]
print(result)  # 输出: [8 6 10]
```
**重要提示**：在NumPy中组合条件时，必须使用位运算符 `&`（与）、`|`（或），而不是Python关键字 `and`、`or`。同时，每个独立的条件必须用括号 `()` 包裹，以确保运算优先级正确。

**示例2：筛选大于5的数或所有的偶数（“或”关系）**
我们需要满足“大于5”或“是偶数”其中任意一个条件。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_64.png)

```python
a = np.array([8, 3, 6, 1, 10])
# 使用 | 运算符组合“或”关系条件
result = a[(a > 5) | (a % 2 == 0)]
print(result)  # 输出: [8 3 6 10]
```
注意，数字3虽然不大于5，但它是奇数，不满足“偶数”条件，因此未被选中。数字1既不大于5也不是偶数，因此被过滤。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_66.png)

## 总结

本节课中我们一起学习了NumPy的布尔型索引。我们首先对比了传统`filter`方法与NumPy方法的差异，领略了其简洁性。然后，我们深入剖析了`a[a > 5]`这种写法的两步实现原理：先通过比较运算生成布尔掩码，再利用掩码提取数据。最后，我们掌握了如何使用 `&` 和 `|` 运算符来组合多个筛选条件，并特别强调了使用它们时的括号语法规则。

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_68.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_70.png)

![](img/20639d0ce6cf2acbfd67f522e13b9cb4_72.png)

布尔型索引是NumPy数据处理的利器，用好后能极大提升数据筛选的效率和代码的可读性。在后续的金融数据分析中，我们将频繁使用它来快速定位符合特定条件（如价格高于均线、收益率超过阈值等）的股票或交易日数据。