# 量化交易教程：P9：10 金融量化分析-numpy-array基础

## 概述
在本节课中，我们将要学习金融量化分析的核心工具之一：NumPy。我们将重点介绍NumPy的基础——`ndarray`数组对象，了解它是什么、为什么比Python原生列表更高效，以及如何创建和使用它进行基础的数值计算。

![](img/213cd4069264f6fa9a3b04fbe721b84a_1.png)

---

![](img/213cd4069264f6fa9a3b04fbe721b84a_3.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_5.png)

## 为什么要使用NumPy？

![](img/213cd4069264f6fa9a3b04fbe721b84a_7.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_9.png)

上一节我们提到了NumPy是量化分析的重要工具。本节中我们来看看为什么需要它。

NumPy是一个用于高性能科学计算和数据分析的基础包。它是后续学习Pandas等库的基石。其核心优势在于提供了高效的多维数组结构，能够进行快速的批量数据运算，而无需编写循环。

为了直观感受NumPy的便利性，我们来看两个例子。

**示例一：货币转换**
假设我们有一个包含50家公司市值（单位：万美元）的列表，需要将所有市值转换为人民币（假设汇率为6.8）。

![](img/213cd4069264f6fa9a3b04fbe721b84a_11.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_13.png)

使用Python原生列表的方法可能需要循环或列表推导式：
```python
# 假设A是一个包含50个市值的列表
A = [random.uniform(100, 200) for _ in range(50)]
# 转换为人民币
B = [value * 6.8 for value in A]
```

![](img/213cd4069264f6fa9a3b04fbe721b84a_15.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_17.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_19.png)

而使用NumPy则非常简单：
```python
import numpy as np
A_np = np.array(A)  # 将列表转换为NumPy数组
B_np = A_np * 6.8   # 直接进行数组与标量的乘法
```
可以看到，NumPy允许我们直接对整个数组进行运算，代码简洁且执行效率更高。

![](img/213cd4069264f6fa9a3b04fbe721b84a_21.png)

**示例二：计算购物车总金额**
假设我们有两个列表，分别存储商品单价和对应数量，需要计算总金额。

![](img/213cd4069264f6fa9a3b04fbe721b84a_23.png)

使用NumPy的方法：
```python
# 单价列表和数量列表
prices = np.array([10, 20, 15])
quantities = np.array([2, 1, 3])
# 计算总金额
total = (prices * quantities).sum()
```
同样，我们无需循环，直接对两个数组进行逐元素相乘，然后求和。

![](img/213cd4069264f6fa9a3b04fbe721b84a_25.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_27.png)

通过以上例子，我们可以直观感受到NumPy在批量数值计算上的强大与便捷。接下来，我们将深入探讨其核心对象——`ndarray`。

![](img/213cd4069264f6fa9a3b04fbe721b84a_29.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_31.png)

---

![](img/213cd4069264f6fa9a3b04fbe721b84a_33.png)

## NumPy的核心：ndarray数组

上一节我们通过例子感受了NumPy的便捷，本节中我们来正式认识其核心数据结构。

![](img/213cd4069264f6fa9a3b04fbe721b84a_35.png)

`ndarray`是NumPy提供的一个N维数组对象。我们通常使用`np.array()`函数来创建它。

![](img/213cd4069264f6fa9a3b04fbe721b84a_37.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_39.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_41.png)

```python
import numpy as np
# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
# 从范围创建
arr2 = np.array(range(10))
```

![](img/213cd4069264f6fa9a3b04fbe721b84a_43.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_45.png)

### 数组与列表的区别
理解数组与Python列表的区别至关重要，主要有两点：

![](img/213cd4069264f6fa9a3b04fbe721b84a_47.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_49.png)

1.  **元素类型必须相同**：Python列表可以容纳不同类型的元素（如整数、字符串混合）。而`ndarray`数组要求所有元素必须是同一种数据类型（通常是数值类型）。
2.  **大小不可变**：Python列表可以动态地添加或删除元素（如使用`append`方法）。而`ndarray`数组在创建后，其形状（shape）是固定的，不能直接添加或删除元素。这种设计与其底层高效的内存布局有关。

---

## ndarray的常用属性

![](img/213cd4069264f6fa9a3b04fbe721b84a_51.png)

认识了数组的基本概念后，我们来学习如何查看和操作数组的属性。

![](img/213cd4069264f6fa9a3b04fbe721b84a_53.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_55.png)

以下是一些最常用的数组属性：

![](img/213cd4069264f6fa9a3b04fbe721b84a_57.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_59.png)

```python
import numpy as np
# 创建一个二维数组示例
arr = np.array([[1, 2, 3], [4, 5, 6]])
```

**以下是`ndarray`的核心属性列表：**

*   **`dtype`**：查看数组元素的数据类型。
    ```python
    print(arr.dtype)  # 输出：int64 (在64位系统上)
    ```
    NumPy有丰富的数据类型，如`int8`, `int16`, `int32`, `int64`, `float32`, `float64`等，用于精确控制内存使用和计算精度。

*   **`size`**：查看数组元素的总个数。
    ```python
    print(arr.size)   # 输出：6
    ```

*   **`shape`**：查看数组的维度形状，返回一个元组。
    ```python
    print(arr.shape)  # 输出：(2, 3) 表示2行3列
    ```

*   **`ndim`**：查看数组的维数。
    ```python
    print(arr.ndim)   # 输出：2
    ```

![](img/213cd4069264f6fa9a3b04fbe721b84a_61.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_63.png)

*   **`T`**：获取数组的转置。对于二维数组，行和列互换。
    ```python
    print(arr.T)
    # 输出：
    # [[1 4]
    #  [2 5]
    #  [3 6]]
    ```

---

![](img/213cd4069264f6fa9a3b04fbe721b84a_65.png)

![](img/213cd4069264f6fa9a3b04fbe721b84a_67.png)

## 总结
本节课中我们一起学习了NumPy库的基础知识。我们首先通过实例了解了NumPy在批量数值计算上的高效与便捷。然后，我们深入探讨了NumPy的核心——`ndarray`数组对象，理解了它与Python列表在**元素类型一致性**和**大小不可变性**上的关键区别。最后，我们掌握了数组的几个核心属性：`dtype`、`size`、`shape`、`ndim`和`T`，这些是后续进行数组操作和金融数据分析的基础。掌握这些概念，将为学习更复杂的量化分析技巧打下坚实的根基。