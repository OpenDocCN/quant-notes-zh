# Python量化交易：P10：NumPy数组基础 🧮

在本节课中，我们将要学习金融量化分析的三大核心工具包之一：NumPy。我们将重点介绍NumPy的核心数据结构——`ndarray`（多维数组），了解其基本概念、创建方法以及常用属性。掌握这些基础知识是后续进行高效数据分析和科学计算的前提。

![](img/f7304bfa9f138851af26070c53de1fcd_1.png)

## 概述

![](img/f7304bfa9f138851af26070c53de1fcd_3.png)

NumPy是一个高性能的科学计算和数据分析基础包。它是后续课程中将要学习的Pandas库的构建基础。NumPy最主要的功能是提供了一个名为`ndarray`的多维数组对象，它允许我们进行高效的批量数据运算，而无需使用循环。此外，NumPy还提供了线性代数、傅里叶变换等数学功能。

![](img/f7304bfa9f138851af26070c53de1fcd_5.png)

![](img/f7304bfa9f138851af26070c53de1fcd_7.png)

安装NumPy非常简单，使用`pip install numpy`命令即可。

![](img/f7304bfa9f138851af26070c53de1fcd_9.png)

## 为什么需要NumPy？

上一节我们介绍了NumPy的概况，本节中我们通过两个例子来直观感受NumPy带来的效率提升。

### 示例一：货币转换

假设我们已知50家跨国公司的市值（以美元为单位），存储在一个Python列表中。现在需要将所有市值转换为人民币（假设汇率为6.8）。

![](img/f7304bfa9f138851af26070c53de1fcd_11.png)

使用传统的Python列表方法，我们需要使用循环或列表推导式：

![](img/f7304bfa9f138851af26070c53de1fcd_13.png)

![](img/f7304bfa9f138851af26070c53de1fcd_15.png)

```python
import random
# 生成50个100到200之间的随机浮点数，模拟市值（万美元）
a = [random.uniform(100, 200) for _ in range(50)]
# 使用列表推导式进行转换
b = [value * 6.8 for value in a]
```

![](img/f7304bfa9f138851af26070c53de1fcd_17.png)

![](img/f7304bfa9f138851af26070c53de1fcd_19.png)

而使用NumPy，操作变得异常简洁：

![](img/f7304bfa9f138851af26070c53de1fcd_21.png)

```python
import numpy as np
# 将列表转换为NumPy数组
a_np = np.array(a)
# 直接进行数组与标量的乘法
result = a_np * 6.8
```

![](img/f7304bfa9f138851af26070c53de1fcd_23.png)

NumPy的数组运算不仅代码简洁，而且底层由C语言实现，执行速度远快于Python循环。

![](img/f7304bfa9f138851af26070c53de1fcd_25.png)

![](img/f7304bfa9f138851af26070c53de1fcd_27.png)

### 示例二：计算购物车总金额

![](img/f7304bfa9f138851af26070c53de1fcd_29.png)

假设我们有两个列表，一个存储商品单价，另一个存储对应商品数量，需要计算总金额。

![](img/f7304bfa9f138851af26070c53de1fcd_31.png)

![](img/f7304bfa9f138851af26070c53de1fcd_33.png)

传统方法同样需要循环。而使用NumPy：

```python
import numpy as np
# 生成单价和数量数组
price = np.random.uniform(10, 20, 50) # 50个商品单价
quantity = np.random.randint(1, 10, 50) # 50个商品数量
# 计算每个商品的总价
item_total = price * quantity
# 计算所有商品的总金额
total_cost = np.sum(item_total)
```

通过以上例子，我们可以感受到NumPy在批量数据处理上的强大与便捷。接下来，我们将深入探讨NumPy数组的核心概念。

![](img/f7304bfa9f138851af26070c53de1fcd_35.png)

## NumPy核心：ndarray对象

![](img/f7304bfa9f138851af26070c53de1fcd_37.png)

![](img/f7304bfa9f138851af26070c53de1fcd_39.png)

![](img/f7304bfa9f138851af26070c53de1fcd_41.png)

NumPy的核心是`ndarray`（N-dimensional array，N维数组）对象。它与Python列表有显著区别。

![](img/f7304bfa9f138851af26070c53de1fcd_43.png)

![](img/f7304bfa9f138851af26070c53de1fcd_45.png)

创建数组非常简单，使用`np.array()`函数，并传入一个列表或其他可迭代对象：

![](img/f7304bfa9f138851af26070c53de1fcd_47.png)

![](img/f7304bfa9f138851af26070c53de1fcd_49.png)

```python
import numpy as np
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array(range(10))
```

### 数组与列表的区别

以下是数组与Python列表的主要区别：

1.  **元素类型必须相同**：Python列表可以容纳不同类型的元素（如整数、字符串混合）。而NumPy数组要求所有元素必须是相同的数据类型。
2.  **大小固定**：Python列表可以动态地使用`append()`方法增加元素。NumPy数组在创建后大小是固定的，不能直接添加元素（但可以创建新数组或调整形状）。

![](img/f7304bfa9f138851af26070c53de1fcd_51.png)

这些特性使得NumPy数组在内存中连续存储，从而实现了高效的计算。

![](img/f7304bfa9f138851af26070c53de1fcd_53.png)

![](img/f7304bfa9f138851af26070c53de1fcd_55.png)

## 数组的常用属性

![](img/f7304bfa9f138851af26070c53de1fcd_57.png)

创建数组后，我们可以通过一系列属性来了解其信息。

![](img/f7304bfa9f138851af26070c53de1fcd_59.png)

```python
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
```

以下是几个关键属性：

*   **`dtype`**：查看数组元素的数据类型。
    ```python
    print(arr.dtype) # 输出：int64
    ```
    NumPy有丰富的数据类型，主要分为以下几类：
    *   **布尔型**：`bool_`
    *   **整型**：`int8`, `int16`, `int32`, `int64`
    *   **无符号整型**：`uint8`, `uint16`, `uint32`, `uint64`
    *   **浮点型**：`float16`, `float32`, `float64`
    *   **复数型**：`complex64`, `complex128`
    类型名后的数字表示该类型在内存中占用的位数（如`int64`占64位）。这决定了该类型能表示的数值范围。

*   **`size`**：查看数组元素的总个数。
    ```python
    print(arr.size) # 输出：5
    ```

*   **`shape`**：查看数组的维度形状，返回一个元组。
    ```python
    arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
    print(arr_2d.shape) # 输出：(2, 3)，表示2行3列
    ```

*   **`ndim`**：查看数组的维数。
    ```python
    print(arr.ndim)    # 输出：1，一维数组
    print(arr_2d.ndim) # 输出：2，二维数组
    ```

![](img/f7304bfa9f138851af26070c53de1fcd_61.png)

![](img/f7304bfa9f138851af26070c53de1fcd_63.png)

*   **`T`**：获取数组的转置。对于二维数组，行和列会互换。
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

## 总结

本节课中我们一起学习了NumPy库的基础知识。我们首先通过实例了解了NumPy在批量运算上的高效与简洁。然后，我们深入探讨了NumPy的核心——`ndarray`数组对象，理解了其与Python列表在**元素类型**和**大小固定**上的关键区别。最后，我们学习了数组的几个常用属性：**`dtype`**、**`size`**、**`shape`**、**`ndim`**和**`T`**，这些属性帮助我们快速了解和操作数组的结构。

![](img/f7304bfa9f138851af26070c53de1fcd_65.png)

![](img/f7304bfa9f138851af26070c53de1fcd_67.png)

掌握这些基础是迈向金融量化分析的重要一步，在接下来的课程中，我们将学习更多关于NumPy数组的创建、索引和运算技巧。