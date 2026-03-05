# Python金融量化：P13：NumPy数组的布尔型索引 🔍

![](img/a567eef9781b34a3e410f72df6b20d02_1.png)

![](img/a567eef9781b34a3e410f72df6b20d02_3.png)

![](img/a567eef9781b34a3e410f72df6b20d02_5.png)

![](img/a567eef9781b34a3e410f72df6b20d02_6.png)

![](img/a567eef9781b34a3e410f72df6b20d02_8.png)

在本节课中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。它允许我们根据条件快速筛选数组中的元素，比传统的Python方法简洁得多。

![](img/a567eef9781b34a3e410f72df6b20d02_10.png)

![](img/a567eef9781b34a3e410f72df6b20d02_12.png)

![](img/a567eef9781b34a3e410f72df6b20d02_14.png)

![](img/a567eef9781b34a3e410f72df6b20d02_16.png)

![](img/a567eef9781b34a3e410f72df6b20d02_18.png)

## 概述 📋

![](img/a567eef9781b34a3e410f72df6b20d02_20.png)

![](img/a567eef9781b34a3e410f72df6b20d02_22.png)

上一节我们介绍了NumPy数组的常规索引和切片。本节中，我们来看看如何使用布尔型索引来根据条件筛选数组元素。其核心思想是：传入一个与数组长度相同的布尔值数组，NumPy会返回所有对应位置为`True`的元素。

![](img/a567eef9781b34a3e410f72df6b20d02_24.png)

![](img/a567eef9781b34a3e410f72df6b20d02_26.png)

## 布尔型索引的原理 🧠

![](img/a567eef9781b34a3e410f72df6b20d02_28.png)

![](img/a567eef9781b34a3e410f72df6b20d02_30.png)

![](img/a567eef9781b34a3e410f72df6b20d02_31.png)

布尔型索引的实现分为两步，理解这两步至关重要。

![](img/a567eef9781b34a3e410f72df6b20d02_33.png)

![](img/a567eef9781b34a3e410f72df6b20d02_35.png)

![](img/a567eef9781b34a3e410f72df6b20d02_37.png)

**第一步：生成布尔数组**
这是通过数组与标量（单个数值）进行比较运算实现的。运算会逐元素进行，并返回一个由`True`和`False`组成的布尔数组。
```python
import numpy as np
A = np.array([8, 3, 6, 2, 9])
bool_arr = A > 5  # 返回 array([ True, False,  True, False,  True])
```

![](img/a567eef9781b34a3e410f72df6b20d02_39.png)

![](img/a567eef9781b34a3e410f72df6b20d02_41.png)

![](img/a567eef9781b34a3e410f72df6b20d02_43.png)

**第二步：应用布尔索引**
将第一步生成的布尔数组放入原数组的方括号`[]`内，NumPy会自动选取所有`True`位置对应的元素。
```python
result = A[bool_arr]  # 返回 array([8, 6, 9])
```
通常，我们会将两步合并为一行简洁的代码：`A[A > 5]`。

![](img/a567eef9781b34a3e410f72df6b20d02_45.png)

![](img/a567eef9781b34a3e410f72df6b20d02_47.png)

![](img/a567eef9781b34a3e410f72df6b20d02_49.png)

## 组合条件筛选 ⚙️

![](img/a567eef9781b34a3e410f72df6b20d02_51.png)

![](img/a567eef9781b34a3e410f72df6b20d02_53.png)

![](img/a567eef9781b34a3e410f72df6b20d02_55.png)

![](img/a567eef9781b34a3e410f72df6b20d02_56.png)

有时我们需要根据多个条件筛选数据。以下是实现组合筛选的方法。

![](img/a567eef9781b34a3e410f72df6b20d02_58.png)

![](img/a567eef9781b34a3e410f72df6b20d02_60.png)

**“与”条件（AND）**
筛选同时满足多个条件的元素，例如“大于5的偶数”。需要使用位运算符 `&`，并且每个条件必须用括号括起来。
```python
# 筛选大于5的偶数
A = np.array([8, 3, 6, 2, 9])
result = A[(A > 5) & (A % 2 == 0)]  # 返回 array([8, 6])
```
**注意**：不能使用Python关键字`and`，必须使用`&`。

![](img/a567eef9781b34a3e410f72df6b20d02_62.png)

**“或”条件（OR）**
筛选满足任意一个条件的元素，例如“大于5的数或所有的偶数”。需要使用位运算符 `|`。
```python
# 筛选大于5的数或所有的偶数
result = A[(A > 5) | (A % 2 == 0)]  # 返回 array([8, 3, 6, 2, 9])
```

![](img/a567eef9781b34a3e410f72df6b20d02_64.png)

## 总结 🎯

![](img/a567eef9781b34a3e410f72df6b20d02_66.png)

本节课中我们一起学习了NumPy的布尔型索引。
*   **核心功能**：通过条件表达式（如 `A > 5`）生成布尔掩码，并用其快速筛选数组。
*   **组合条件**：使用 `&` 表示“与”，使用 `|` 表示“或”，每个独立条件需用括号包裹。
*   **优势**：相比Python内置的`filter()`函数，NumPy的布尔型索引语法更简洁，执行效率更高，是数据处理中的利器。

![](img/a567eef9781b34a3e410f72df6b20d02_68.png)

![](img/a567eef9781b34a3e410f72df6b20d02_70.png)

![](img/a567eef9781b34a3e410f72df6b20d02_72.png)

掌握布尔型索引，能让你在金融量化分析中高效地筛选股票数据、执行条件计算，极大地提升代码的简洁性和性能。