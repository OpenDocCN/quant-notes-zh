# Python金融量化投资分析与股票交易：P11：10 金融量化分析-numpy-array基础 🧮

在本节课中，我们将要学习NumPy库的基础知识。NumPy是Python进行科学计算和数据分析的核心工具包，尤其擅长处理多维数组数据。掌握NumPy是后续学习Pandas等高级数据分析库的基石。

## 概述：为什么需要NumPy？

上一节我们介绍了金融量化分析的整体框架，本节中我们来看看其中一个核心工具——NumPy。NumPy是一个高性能的科学计算和数据分析基础包。它是后续要讲的Pandas库的基础，因为Pandas正是在NumPy的基础上构建的。

![](img/28e2cf27dd0894eceed2162a12c44e75_1.png)

NumPy最主要的功能是提供了一个名为`ndarray`的多维数组对象，它能高效地处理批量数据运算。使用NumPy后，在对一批数据进行运算时，可以避免使用循环，从而极大地提升代码效率和运行速度。除此之外，NumPy还提供了线性代数、傅里叶变换等数学功能。

## 安装与导入

![](img/28e2cf27dd0894eceed2162a12c44e75_3.png)

![](img/28e2cf27dd0894eceed2162a12c44e75_5.png)

NumPy的安装非常简单，通常使用pip命令即可完成。在课程中，我们假设已经安装完毕。

![](img/28e2cf27dd0894eceed2162a12c44e75_7.png)

以下是NumPy的标准导入方式，这也是业界的通用约定：

![](img/28e2cf27dd0894eceed2162a12c44e75_9.png)

```python
import numpy as np
```

这种缩写形式`np`使得后续代码更加简洁。因此，当你看到代码中出现`np.`时，就知道它引用了NumPy库。

## 直观感受：NumPy的强大之处

为了让大家直观感受NumPy的便利性，我们来看两个例子。

### 示例一：货币批量换算

假设我们已知50家跨国公司的市值（单位为美元），存储在一个列表`A`中。现在需要将所有市值换算成人民币（假设汇率为6.8）。

如果不使用NumPy，我们可能需要使用循环或列表推导式来逐个计算。但使用NumPy，操作变得异常简单：

![](img/28e2cf27dd0894eceed2162a12c44e75_11.png)

```python
import numpy as np
import random

![](img/28e2cf27dd0894eceed2162a12c44e75_13.png)

# 模拟生成50家公司的市值（单位：万美元）
A = [random.uniform(100, 200) for _ in range(50)]

![](img/28e2cf27dd0894eceed2162a12c44e75_15.png)

# 将列表转换为NumPy数组
A_np = np.array(A)

![](img/28e2cf27dd0894eceed2162a12c44e75_17.png)

![](img/28e2cf27dd0894eceed2162a12c44e75_19.png)

# 汇率
rate = 6.8

![](img/28e2cf27dd0894eceed2162a12c44e75_21.png)

# 批量换算：数组直接乘以标量
result = A_np * rate
print(result)
```

可以看到，我们只需要将列表转换为NumPy数组，然后直接进行乘法运算即可，无需任何循环。代码简洁，执行效率高。

![](img/28e2cf27dd0894eceed2162a12c44e75_23.png)

### 示例二：计算购物车总金额

假设我们有两个列表：一个存储商品单价`A`，另一个存储对应商品数量`B`。我们需要计算购物车的总金额。

![](img/28e2cf27dd0894eceed2162a12c44e75_25.png)

![](img/28e2cf27dd0894eceed2162a12c44e75_27.png)

使用NumPy，我们可以轻松实现两个数组的对应元素相乘并求和：

![](img/28e2cf27dd0894eceed2162a12c44e75_29.png)

```python
import numpy as np
import random

![](img/28e2cf27dd0894eceed2162a12c44e75_31.png)

# 生成50件商品的单价和数量
A = [random.uniform(10, 20) for _ in range(50)]  # 单价
B = [random.randint(1, 10) for _ in range(50)]    # 数量

![](img/28e2cf27dd0894eceed2162a12c44e75_33.png)

# 转换为NumPy数组
A_np = np.array(A)
B_np = np.array(B)

# 计算每件商品的总价（对应元素相乘）
item_totals = A_np * B_np

# 计算所有商品的总金额（求和）
total_cost = item_totals.sum()
print(total_cost)
```

同样，我们避免了繁琐的循环，用一行代码`A_np * B_np`就完成了对应元素的乘法运算。

通过以上例子，我们感受到了NumPy在处理批量数据运算时的简洁与高效。接下来，我们将深入探讨其核心数据结构。

![](img/28e2cf27dd0894eceed2162a12c44e75_35.png)

## 核心：ndarray数组对象

![](img/28e2cf27dd0894eceed2162a12c44e75_37.png)

![](img/28e2cf27dd0894eceed2162a12c44e75_39.png)

NumPy的核心是`ndarray`（N-dimensional array，N维数组）对象。我们通过`np.array()`函数来创建它，通常传入一个列表作为参数。

![](img/28e2cf27dd0894eceed2162a12c44e75_41.png)

以下是创建数组的示例：

![](img/28e2cf27dd0894eceed2162a12c44e75_43.png)

![](img/28e2cf27dd0894eceed2162a12c44e75_45.png)

```python
import numpy as np

![](img/28e2cf27dd0894eceed2162a12c44e75_47.png)

# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
print(arr1)

![](img/28e2cf27dd0894eceed2162a12c44e75_49.png)

# 从range对象创建一维数组
arr2 = np.array(range(10))
print(arr2)
```

### 数组与列表的区别

理解数组与Python原生列表的区别至关重要，主要有两点：

1.  **元素类型必须相同**：Python列表可以容纳不同类型的元素（如整数、字符串混合）。但NumPy数组要求所有元素必须是相同的数据类型（通常是数字）。这是其能进行高效数学运算的前提。
2.  **大小不可动态改变**：Python列表可以随时使用`append()`方法添加元素，大小可变。而NumPy数组在创建时就固定了大小（形状），不能像列表一样随意追加元素。虽然可以修改元素值，但其维度（shape）是固定的。这种设计与其底层高效的内存结构有关。

## 数组的常用属性

![](img/28e2cf27dd0894eceed2162a12c44e75_51.png)

创建数组后，我们可以通过一系列属性来了解它。以下是一些最常用的属性：

```python
import numpy as np

![](img/28e2cf27dd0894eceed2162a12c44e75_53.png)

# 创建一个一维数组
arr = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])

![](img/28e2cf27dd0894eceed2162a12c44e75_55.png)

# 1. dtype: 查看数组元素的数据类型
print(arr.dtype)  # 输出: int64 (64位整数)

![](img/28e2cf27dd0894eceed2162a12c44e75_57.png)

# 2. size: 查看数组元素的总个数
print(arr.size)   # 输出: 10

![](img/28e2cf27dd0894eceed2162a12c44e75_59.png)

# 3. shape: 查看数组的维度形状（对于一维数组，显示为(n,)）
print(arr.shape)  # 输出: (10,)

# 创建一个二维数组（2行3列）
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(arr_2d)
# 输出:
# [[1 2 3]
#  [4 5 6]]

# 4. 对于二维数组，shape返回 (行数, 列数)
print(arr_2d.shape)  # 输出: (2, 3)

# 5. ndim: 查看数组的维数
print(arr.ndim)     # 输出: 1 (一维)
print(arr_2d.ndim)  # 输出: 2 (二维)

# 6. T: 获取数组的转置（行变列，列变行）
print(arr_2d.T)
# 输出:
# [[1 4]
#  [2 5]
#  [3 6]]
```

### 关于数据类型（dtype）

NumPy有自己的一套数据类型系统，主要分为以下几类：
*   **布尔型**：`bool_`
*   **整型**：`int8`, `int16`, `int32`, `int64`
*   **无符号整型**：`uint8`, `uint16`, `uint32`, `uint64`
*   **浮点型**：`float16`, `float32`, `float64`
*   **复数型**：`complex64`, `complex128`

数字后缀（如8, 16, 32, 64）表示该数据类型占用的**位数**，这决定了它能表示的数值范围。例如，`int64`表示64位有符号整数，其表示范围约为$-2^{63}$到$2^{63}-1$。在金融量化中，我们最常使用的是`int64`和`float64`。

### 关于多维数组

我们可以创建二维、三维甚至更高维的数组。
*   **一维数组**：可以想象成一条线。
*   **二维数组**：可以想象成一张表格（有行和列）。
*   **三维数组**：可以想象成一个由多张表格（页）组成的本子。

![](img/28e2cf27dd0894eceed2162a12c44e75_61.png)

```python
# 创建一个三维数组（2页，每页2行3列）
arr_3d = np.array([[[1,2,3],[4,5,6]], [[7,8,9],[10,11,12]]])
print(arr_3d.shape)  # 输出: (2, 2, 3) -> (页， 行， 列)
```

![](img/28e2cf27dd0894eceed2162a12c44e75_63.png)

在金融数据分析中，二维数组（表格数据）是最常见的形态。

## 总结

本节课中我们一起学习了NumPy库的基础知识。我们首先了解了NumPy在金融量化分析中的核心地位及其高效处理批量数据的能力。通过两个生动的例子，我们直观感受到了使用NumPy数组进行运算的简洁与强大。

![](img/28e2cf27dd0894eceed2162a12c44e75_65.png)

接着，我们深入探讨了NumPy的核心数据结构`ndarray`，明确了它与Python列表在**元素类型一致性**和**大小不可变性**上的关键区别。最后，我们学习了数组的一系列常用属性，包括：
*   `dtype`：查看数据类型。
*   `size`：查看元素总数。
*   `shape`：查看数组形状（维度）。
*   `ndim`：查看维数。
*   `.T`：获取数组的转置。

![](img/28e2cf27dd0894eceed2162a12c44e75_67.png)

掌握这些基础是后续进行更复杂数据操作和金融计算的起点。下一节，我们将学习如何对NumPy数组进行创建、索引和切片等操作。