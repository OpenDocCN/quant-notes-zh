# Python金融量化分析：P12：NumPy数组基础 🧮

![](img/8835ed43a954118e66f715ca77558be5_1.png)

在本节课中，我们将要学习金融量化分析中至关重要的工具之一：NumPy。我们将重点介绍其核心数据结构——数组（ndarray），并通过实例理解其高效性。

![](img/8835ed43a954118e66f715ca77558be5_3.png)

![](img/8835ed43a954118e66f715ca77558be5_5.png)

NumPy是一个用于高性能科学计算和数据分析的基础包。它是后续学习Pandas等高级库的基石。NumPy最主要的功能是提供了一个名为`ndarray`的多维数组对象，它能高效地处理批量数据运算，无需使用循环。此外，它还提供了线性代数、傅里叶变换等功能。

![](img/8835ed43a954118e66f715ca77558be5_7.png)

![](img/8835ed43a954118e66f715ca77558be5_9.png)

安装NumPy非常简单，使用`pip install numpy`命令即可。

引用NumPy的通用方式是：
```python
import numpy as np
```

## 为什么需要NumPy？⚡

![](img/8835ed43a954118e66f715ca77558be5_11.png)

为了直观感受NumPy的优势，我们来看两个例子。

![](img/8835ed43a954118e66f715ca77558be5_13.png)

![](img/8835ed43a954118e66f715ca77558be5_15.png)

上一节我们介绍了NumPy的基本概念，本节中我们来看看它如何简化批量运算。

![](img/8835ed43a954118e66f715ca77558be5_17.png)

![](img/8835ed43a954118e66f715ca77558be5_19.png)

![](img/8835ed43a954118e66f715ca77558be5_21.png)

**示例一：货币转换**
假设我们有一个包含50家公司市值的列表（单位：美元），需要将所有值转换为人民币（汇率假设为6.8）。

![](img/8835ed43a954118e66f715ca77558be5_23.png)

使用纯Python列表的传统方法需要循环或列表推导式：
```python
import random
A = [random.uniform(100, 200) for _ in range(50)]  # 生成50个100-200之间的随机数，模拟市值
B = [a * 6.8 for a in A]  # 使用列表推导式进行转换
```

![](img/8835ed43a954118e66f715ca77558be5_25.png)

![](img/8835ed43a954118e66f715ca77558be5_27.png)

而使用NumPy则异常简洁：
```python
import numpy as np
A_np = np.array(A)  # 将列表转换为NumPy数组
B_np = A_np * 6.8   # 数组直接与标量相乘
```
NumPy允许对整个数组执行标量运算，代码更简洁，执行效率也更高。

![](img/8835ed43a954118e66f715ca77558be5_29.png)

![](img/8835ed43a954118e66f715ca77558be5_31.png)

![](img/8835ed43a954118e66f715ca77558be5_33.png)

**示例二：计算购物车总金额**
假设我们有两个列表，一个存储商品单价，另一个存储对应商品数量，需要计算总金额。

以下是NumPy的解决方案：
```python
price = np.array([random.uniform(10, 20) for _ in range(50)])  # 单价数组
quantity = np.array([random.randint(1, 10) for _ in range(50)]) # 数量数组
total_per_item = price * quantity  # 对应位置元素相乘，得到每件商品总价
total_cost = total_per_item.sum()   # 对所有元素求和，得到总金额
```
NumPy数组支持对应位置的元素级运算，并提供了便捷的求和函数，极大地简化了计算。

通过以上例子，我们直观感受到了NumPy在处理批量数据运算时的便捷与高效。接下来，我们将深入探讨其核心对象——`ndarray`。

![](img/8835ed43a954118e66f715ca77558be5_35.png)

![](img/8835ed43a954118e66f715ca77558be5_37.png)

![](img/8835ed43a954118e66f715ca77558be5_39.png)

## 理解ndarray对象 🧱

![](img/8835ed43a954118e66f715ca77558be5_41.png)

![](img/8835ed43a954118e66f715ca77558be5_43.png)

![](img/8835ed43a954118e66f715ca77558be5_45.png)

`ndarray`是NumPy的核心，它是一个多维数组对象。使用`np.array()`函数并传入一个列表（或任何可迭代对象）即可创建。

![](img/8835ed43a954118e66f715ca77558be5_47.png)

![](img/8835ed43a954118e66f715ca77558be5_49.png)

NumPy数组与Python列表有两大关键区别：
1.  **元素类型必须相同**：数组中的所有元素必须是相同的数据类型（如全是整数或全是浮点数），而列表可以混合存储不同类型的数据。
2.  **大小不可变**：数组一旦创建，其大小（即元素总数）就固定了，不能像列表那样随意追加(`append`)元素。这是其实现高性能内存访问的基础。

以下是NumPy数组的一些常用属性，我们将通过代码示例来了解它们。

![](img/8835ed43a954118e66f715ca77558be5_51.png)

## 数组的常用属性 📊

![](img/8835ed43a954118e66f715ca77558be5_53.png)

![](img/8835ed43a954118e66f715ca77558be5_55.png)

首先，我们创建一个数组并查看其属性。
```python
import numpy as np
arr = np.array([1, 2, 3, 4, 5])  # 创建一维数组
```

![](img/8835ed43a954118e66f715ca77558be5_57.png)

以下是该数组的一些关键属性：

![](img/8835ed43a954118e66f715ca77558be5_59.png)

*   **dtype**: 返回数组中元素的数据类型。
    ```python
    print(arr.dtype)  # 输出：int64
    ```
    NumPy有丰富的数据类型，主要分为以下几类：
    *   `bool_`: 布尔型
    *   `int8/int16/int32/int64`: 有符号整数
    *   `uint8/uint16/uint32/uint64`: 无符号整数
    *   `float16/float32/float64`: 浮点数
    *   `complex64/complex128`: 复数
    类型名后的数字表示该类型在内存中占用的位数（如`int64`占64位）。这决定了该类型能表示的数值范围。

*   **size**: 返回数组中元素的总数。
    ```python
    print(arr.size)  # 输出：5
    ```

*   **shape**: 返回一个元组，表示数组每个维度的大小。对于一维数组，它表示长度；对于二维数组，它表示（行数， 列数）。
    ```python
    arr_2d = np.array([[1, 2, 3], [4, 5, 6]])  # 创建一个2行3列的二维数组
    print(arr_2d.shape)  # 输出：(2, 3)
    ```

*   **ndim**: 返回数组的维数。
    ```python
    print(arr.ndim)    # 输出：1 (一维)
    print(arr_2d.ndim) # 输出：2 (二维)
    ```

![](img/8835ed43a954118e66f715ca77558be5_61.png)

![](img/8835ed43a954118e66f715ca77558be5_63.png)

*   **T**: 返回数组的转置。对于二维数组，行和列会互换。
    ```python
    print(arr_2d)
    # 输出：
    # [[1 2 3]
    #  [4 5 6]]
    print(arr_2d.T)
    # 输出：
    # [[1 4]
    #  [2 5]
    #  [3 6]]
    ```

## 总结 🎯

![](img/8835ed43a954118e66f715ca77558be5_65.png)

![](img/8835ed43a954118e66f715ca77558be5_67.png)

本节课中我们一起学习了NumPy库的基础知识。我们首先了解了NumPy在金融量化分析中的核心地位，然后通过实例对比，深刻体会到其`ndarray`数组在批量数据运算上带来的简洁性与高效性。我们重点探讨了`ndarray`对象的创建、它与Python列表的核心区别，并学习了`dtype`、`size`、`shape`、`ndim`、`T`等关键属性的含义与用法。掌握这些基础是后续进行更复杂数值计算和数据分析的重要第一步。