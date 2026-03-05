# Python金融量化投资分析：P10：NumPy数组基础 🧮

在本节课中，我们将要学习Python金融量化分析的核心工具之一——NumPy。NumPy是进行高性能科学计算和数据分析的基础包，也是后续学习Pandas等库的基石。我们将重点介绍其核心数据结构——数组（ndarray），并通过实例理解其高效性。

![](img/f4a890fa209caf653e1aa59b155780fc_1.png)

## 为什么要使用NumPy？ 🤔

![](img/f4a890fa209caf653e1aa59b155780fc_3.png)

![](img/f4a890fa209caf653e1aa59b155780fc_5.png)

上一节我们提到了NumPy的重要性，本节中我们来看看它具体解决了什么问题。通过两个简单的例子，我们可以直观感受NumPy带来的便利。

![](img/f4a890fa209caf653e1aa59b155780fc_7.png)

![](img/f4a890fa209caf653e1aa59b155780fc_9.png)

**示例一：货币批量转换**
假设我们已知50家跨国公司的市值（单位：万美元），需要将所有美元市值转换为人民币（假设汇率为6.8）。

如果不使用NumPy，我们可能需要使用循环或列表推导式：

```python
# 生成模拟数据：50家公司的市值（100-200万美元）
import random
A = [random.uniform(100, 200) for _ in range(50)]

# 传统方法：使用列表推导式进行转换
B = [value * 6.8 for value in A]
```

![](img/f4a890fa209caf653e1aa59b155780fc_11.png)

![](img/f4a890fa209caf653e1aa59b155780fc_13.png)

而使用NumPy，操作将变得异常简洁高效：

![](img/f4a890fa209caf653e1aa59b155780fc_15.png)

![](img/f4a890fa209caf653e1aa59b155780fc_17.png)

```python
import numpy as np
A_np = np.array(A)  # 将列表转换为NumPy数组
B_np = A_np * 6.8   # 直接对整个数组进行标量乘法
```

![](img/f4a890fa209caf653e1aa59b155780fc_19.png)

![](img/f4a890fa209caf653e1aa59b155780fc_21.png)

**示例二：计算购物车总金额**
假设我们有一个商品价格列表和一个对应商品数量的列表，需要计算总金额。

![](img/f4a890fa209caf653e1aa59b155780fc_23.png)

使用NumPy可以轻松实现向量化运算：

![](img/f4a890fa209caf653e1aa59b155780fc_25.png)

![](img/f4a890fa209caf653e1aa59b155780fc_27.png)

```python
# 模拟数据：50件商品的价格和数量
prices = np.array([random.uniform(10, 20) for _ in range(50)])
quantities = np.array([random.randint(1, 10) for _ in range(50)])

![](img/f4a890fa209caf653e1aa59b155780fc_29.png)

![](img/f4a890fa209caf653e1aa59b155780fc_31.png)

# 计算总金额：对应元素相乘后求和
total_cost = (prices * quantities).sum()
```

![](img/f4a890fa209caf653e1aa59b155780fc_33.png)

通过以上例子可以看到，NumPy允许我们直接对整个数组进行数学运算，无需编写循环。这种向量化操作不仅代码简洁，而且底层由C语言实现，运行速度远超Python原生循环。

## NumPy核心：ndarray对象 🏗️

NumPy的核心是提供了一个名为`ndarray`的多维数组对象。它与Python列表有本质区别。

![](img/f4a890fa209caf653e1aa59b155780fc_35.png)

![](img/f4a890fa209caf653e1aa59b155780fc_37.png)

![](img/f4a890fa209caf653e1aa59b155780fc_39.png)

以下是创建数组的基本方法：

![](img/f4a890fa209caf653e1aa59b155780fc_41.png)

![](img/f4a890fa209caf653e1aa59b155780fc_43.png)

![](img/f4a890fa209caf653e1aa59b155780fc_45.png)

```python
import numpy as np
# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
# 从范围创建
arr2 = np.array(range(10))
```

![](img/f4a890fa209caf653e1aa59b155780fc_47.png)

![](img/f4a890fa209caf653e1aa59b155780fc_49.png)

**数组与列表的主要区别：**
1.  **元素类型必须一致**：Python列表可以存储不同类型的元素（如字符串、整数混合）。而NumPy数组要求所有元素必须是相同的数据类型。
2.  **大小固定**：Python列表可以动态地`append`新元素。NumPy数组在创建时大小就固定了，不能直接添加元素（但可以创建新数组）。

这种设计使得数组在内存中连续存储，是NumPy能够进行高效批量运算的关键。

## 数组的常用属性 📊

![](img/f4a890fa209caf653e1aa59b155780fc_51.png)

了解数组的属性有助于我们更好地操作数据。让我们创建一个数组并查看其属性。

![](img/f4a890fa209caf653e1aa59b155780fc_53.png)

```python
import numpy as np
arr = np.array([[1, 2, 3], [4, 5, 6]])  # 创建一个2行3列的二维数组
```

![](img/f4a890fa209caf653e1aa59b155780fc_55.png)

![](img/f4a890fa209caf653e1aa59b155780fc_57.png)

以下是ndarray对象的一些关键属性：

![](img/f4a890fa209caf653e1aa59b155780fc_59.png)

*   **dtype**: 返回数组中元素的数据类型。
    ```python
    print(arr.dtype)  # 输出：int64 (64位整数)
    ```
*   **size**: 返回数组中元素的总个数。
    ```python
    print(arr.size)   # 输出：6
    ```
*   **shape**: 返回数组的维度，以一个元组表示（行数， 列数， ...）。
    ```python
    print(arr.shape)  # 输出：(2, 3)
    ```
*   **ndim**: 返回数组的维数。
    ```python
    print(arr.ndim)   # 输出：2 (二维数组)
    ```
*   **T**: 返回数组的转置（行变列，列变行）。
    ```python
    print(arr.T)
    # 输出：
    # [[1 4]
    #  [2 5]
    #  [3 6]]
    ```

**关于维度的理解：**
*   **一维数组**：可以看作一条线。
*   **二维数组**：可以看作一张表格或矩阵（有行和列）。
*   **三维数组**：可以看作一叠表格（页、行、列），例如多只股票多天的数据。

## NumPy的数据类型 🔢

NumPy有自己的一套数据类型系统，主要用于数值计算。以下是常见的数据类型分类：

| 类型 | 说明 | 示例 |
| :--- | :--- | :--- |
| **布尔型** | 存储 `True` 或 `False` | `bool_` |
| **有符号整型** | 存储正负整数 | `int8`, `int16`, `int32`, `int64` |
| **无符号整型** | 仅存储非负整数 | `uint8`, `uint16`, `uint32`, `uint64` |
| **浮点型** | 存储小数 | `float16`, `float32`, `float64` |
| **复数型** | 存储复数 | `complex64`, `complex128` |

![](img/f4a890fa209caf653e1aa59b155780fc_61.png)

**说明：**
*   类型名后的数字（如`int64`中的64）代表该类型在内存中占用的**位数**。
*   位数越多，能表示的数值范围越大或精度越高。例如，`int64`比`int8`能表示更大范围的整数。
*   默认情况下，整数会创建为`int64`，浮点数会创建为`float64`（取决于操作系统）。

![](img/f4a890fa209caf653e1aa59b155780fc_63.png)

## 总结 📝

本节课中我们一起学习了NumPy库的基础知识：
1.  **NumPy的作用**：它是Python科学计算的基础包，通过`ndarray`对象实现高效的向量化运算，避免了低效的循环。
2.  **核心对象**：`ndarray`多维数组，其元素类型必须一致且大小固定，这与Python列表不同。
3.  **高效性体现**：我们通过货币转换和购物车计算的例子，直观对比了使用NumPy向量化运算和传统方法的区别。
4.  **数组属性**：我们学习了`dtype`（数据类型）、`shape`（形状）、`size`（元素总数）、`ndim`（维数）和`T`（转置）等关键属性。
5.  **数据类型**：NumPy提供了丰富的数据类型（如各种整型、浮点型），以满足不同精度和范围的数值计算需求。

![](img/f4a890fa209caf653e1aa59b155780fc_65.png)

![](img/f4a890fa209caf653e1aa59b155780fc_67.png)

掌握NumPy数组是步入金融数据分析和量化投资领域的关键一步，它为处理大规模的股价、交易量等数值数据提供了强大且高效的工具。