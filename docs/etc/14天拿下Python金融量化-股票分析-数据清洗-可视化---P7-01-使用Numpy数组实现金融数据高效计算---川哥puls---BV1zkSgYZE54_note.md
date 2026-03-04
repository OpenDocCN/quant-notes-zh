# Python金融量化：P7：01 使用Numpy数组实现金融数据高效计算 📊

![](img/6a115cfca3a1581e25c154e28f0c1a1f_0.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_2.png)

在本节课中，我们将学习Python中用于科学计算的核心库——NumPy。我们将重点掌握NumPy数组的创建、属性、数据选取、预处理、重塑以及各种数学运算。这些技能是进行高效金融数据分析与计算的基础。

![](img/6a115cfca3a1581e25c154e28f0c1a1f_4.png)

## NumPy数组介绍 🧮

![](img/6a115cfca3a1581e25c154e28f0c1a1f_6.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_8.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_10.png)

NumPy是Python语言的一个扩展程序库，它支持大量的维度数组与矩阵运算。此外，NumPy也为数组运算提供了大量的数学函数库。NumPy是一个运算速度非常快的数学库，在Python中主要用它来进行数组计算。

在金融领域的使用中，NumPy通常与SciPy和Matplotlib一起使用，广泛用于替代MATLAB。它是一个非常强大的科学计算环境，有助于我们通过Python学习数据科学或机器学习。

NumPy是一个针对多维数组的科学计算包，这个包里有许多科学计算函数可以调用。数组是相同数据类型的元素按照一定顺序排列的组合。它是有序的，这一点与列表、元组类似，但与字典不同。

## 生成NumPy数组 🏗️

上一节我们介绍了NumPy的基本概念，本节中我们来看看如何生成NumPy数组。

以下是生成NumPy数组的几种常用方法：

1.  **使用 `np.array()` 直接创建**
    这是最基础的创建方法，可以将列表、元组等序列转换为NumPy数组。
    ```python
    import numpy as np
    array1 = np.array([0, 1, 2, 3, 4, 5, 6])
    ```

2.  **使用 `np.arange()` 生成序列数组**
    这个函数类似于Python的`range()`，但直接生成数组。可以指定起始值、终止值和步长。
    ```python
    array2 = np.arange(10) # 生成 0 到 9
    array3 = np.arange(1, 20, 2) # 生成 1 到 19，步长为2
    ```

3.  **使用特定函数生成特殊数组**
    *   `np.ones()`: 生成全为1的数组。
    *   `np.zeros()`: 生成全为0的数组。
    *   `np.eye()`: 生成单位矩阵（对角线为1的方阵）。
    *   `np.full()`: 生成指定形状和填充值的数组。
    ```python
    ones_array = np.ones((3, 2)) # 3行2列的全1数组
    identity_matrix = np.eye(4) # 4x4的单位矩阵
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_12.png)

4.  **使用 `np.random` 模块生成随机数组**
    *   `np.random.rand()`: 生成[0,1)均匀分布的随机数组。
    *   `np.random.randn()`: 生成标准正态分布的随机数组。
    *   `np.random.randint()`: 生成指定范围内的随机整数数组。
    *   `np.random.choice()`: 从给定数组中随机抽样。
    *   `np.random.shuffle()`: 将数组顺序随机打乱。
    ```python
    random_array = np.random.rand(2, 3) # 2行3列的[0,1)随机数组
    normal_array = np.random.randn(5) # 长度为5的标准正态分布数组
    int_array = np.random.randint(0, 100, size=10) # 10个0到100的随机整数
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_14.png)

## NumPy数组的属性和数据选取 🔍

了解了如何创建数组后，我们需要知道如何查看数组的属性和从中选取数据。

![](img/6a115cfca3a1581e25c154e28f0c1a1f_16.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_18.png)

### 数组的基本属性

对于一个NumPy数组，我们通常关心它的形状、大小、数据类型和维度。

```python
import numpy as np
arr = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

print(arr.shape)   # 输出形状：(3, 3)
print(arr.size)    # 输出元素总数：9
print(arr.dtype)   # 输出数据类型：int64 (取决于系统)
print(arr.ndim)    # 输出维度：2
```

### 数据的选取

数据选取的方法与Python列表类似，但功能更强大。

1.  **索引和切片**
    ```python
    arr = np.arange(10) # [0 1 2 3 4 5 6 7 8 9]
    print(arr[3])       # 输出第4个元素：3
    print(arr[2:5])     # 输出第3到第5个元素：[2 3 4]
    print(arr[::2])     # 输出所有元素，步长为2：[0 2 4 6 8]
    ```

2.  **条件选取（布尔索引）**
    这是NumPy非常强大的功能，可以直接用条件表达式筛选数据。
    ```python
    arr = np.arange(10)
    print(arr[arr > 5])          # 输出大于5的元素：[6 7 8 9]
    print(arr[(arr > 3) & (arr < 7)]) # 输出大于3且小于7的元素：[4 5 6]
    ```

3.  **多维数组的选取**
    对于多维数组，索引由外向内一层层进行。
    ```python
    arr_2d = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
    print(arr_2d[0])        # 输出第一行：[1 2 3]
    print(arr_2d[0, 1])     # 输出第一行第二列的元素：2
    print(arr_2d[:, 1])     # 输出所有行的第二列：[2 5 8]
    print(arr_2d[1:, :2])   # 输出第二行及之后，前两列：[[4 5] [7 8]]
    ```

## 数组的预处理与重塑 🛠️

![](img/6a115cfca3a1581e25c154e28f0c1a1f_20.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_22.png)

在实际分析中，我们经常需要对数据进行清洗和变换。本节我们学习数组的预处理与重塑。

### 数据预处理

1.  **数据类型转换 `astype()`**
    有时需要将数组的数据类型进行转换，例如将整数转为浮点数以便进行除法运算。
    ```python
    arr_int = np.array([1, 2, 3, 4])
    arr_float = arr_int.astype(np.float64)
    print(arr_float.dtype) # float64
    ```

2.  **缺失值处理**
    NumPy用`np.nan`表示缺失值。使用`np.isnan()`函数可以检测缺失值。
    ```python
    arr_with_nan = np.array([1, 2, np.nan, 4])
    print(np.isnan(arr_with_nan)) # [False False  True False]
    # 将缺失值替换为0
    arr_with_nan[np.isnan(arr_with_nan)] = 0
    print(arr_with_nan) # [1. 2. 0. 4.]
    ```

3.  **删除重复值 `np.unique()`**
    ```python
    arr_dup = np.array([2, 1, 3, 1, 2, 4])
    arr_unique = np.unique(arr_dup)
    print(arr_unique) # [1 2 3 4]
    ```

### 数组重塑 `reshape()`

![](img/6a115cfca3a1581e25c154e28f0c1a1f_24.png)

重塑可以改变数组的维度形状，但元素总数必须保持不变。
```python
arr = np.arange(8) # [0 1 2 3 4 5 6 7]
arr_2x4 = arr.reshape((2, 4)) # 变为2行4列
arr_4x2 = arr.reshape((4, 2)) # 变为4行2列
arr_1x8 = arr.reshape((1, 8)) # 变为1行8列（二维）
arr_8x1 = arr.reshape((8, 1)) # 变为8行1列（二维）
```
**注意**：`reshape`方法返回一个新数组，原数组本身并不改变。`(8,)`是一维数组，`(1, 8)`是二维数组，两者形状不同。

![](img/6a115cfca3a1581e25c154e28f0c1a1f_26.png)

### 数组转置 `.T` 或 `transpose()`

转置是矩阵的常见操作，将行和列互换。
```python
arr = np.array([[1, 2, 3], [4, 5, 6]]) # 2行3列
arr_t = arr.T # 或 arr.transpose()
print(arr_t.shape) # (3, 2) 变为3行2列
```

## NumPy数组的计算 ⚙️

NumPy的核心优势在于其高效且便捷的数组计算能力。计算主要分为两类：数组与数字的运算、数组与数组的运算。

### 数组与数字的运算（广播机制）

NumPy支持数组与单个数字进行逐元素运算，这称为广播。
```python
arr = np.array([1, 2, 3, 4])
print(arr + 10)  # 每个元素加10：[11 12 13 14]
print(arr * 2)   # 每个元素乘2：[2 4 6 8]
print(arr ** 2)  # 每个元素平方：[ 1  4  9 16]
print(np.sqrt(arr)) # 每个元素开方：[1.         1.41421356 1.73205081 2.        ]
```

### 数组与数组的运算

形状相同的数组可以进行逐元素的加减乘除等运算。
```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])
print(a + b) # [5 7 9]
print(a * b) # [ 4 10 18]
```

### 数组的合并

![](img/6a115cfca3a1581e25c154e28f0c1a1f_28.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_30.png)

合并数组时，需要关注合并的方向（横向或纵向）。

1.  **`np.concatenate()` 通用合并**
    通过`axis`参数指定合并方向（0为纵向，1为横向）。
    ```python
    a = np.array([[1, 2], [3, 4]])
    b = np.array([[5, 6], [7, 8]])
    # 纵向合并 (axis=0)
    v_stack = np.concatenate([a, b], axis=0)
    # 横向合并 (axis=1)
    h_stack = np.concatenate([a, b], axis=1)
    ```

2.  **专用合并函数**
    *   `np.vstack()`: 纵向堆叠（垂直方向）。
    *   `np.hstack()`: 横向堆叠（水平方向）。
    ```python
    v_stack = np.vstack((a, b))
    h_stack = np.hstack((a, b))
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_32.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_34.png)

## NumPy常用函数与高级操作 🧰

NumPy提供了丰富的数学和统计函数，可以方便地对整个数组或沿特定轴进行计算。

![](img/6a115cfca3a1581e25c154e28f0c1a1f_36.png)

### 数学函数

![](img/6a115cfca3a1581e25c154e28f0c1a1f_38.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_40.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_42.png)

```python
arr = np.array([-4, -3, -2, -1, 0, 1, 2, 3, 4])
print(np.abs(arr))   # 绝对值：[4 3 2 1 0 1 2 3 4]
print(np.square(arr)) # 平方：[16  9  4  1  0  1  4  9 16]
print(np.sqrt(np.abs(arr))) # 开方（先取绝对值）
```

### 描述性统计函数

这些函数可以计算整个数组或沿指定轴的统计量。
```python
arr = np.random.rand(4, 5) * 100 # 4x5的随机数组
print(arr.sum())          # 所有元素求和
print(arr.sum(axis=0))    # 沿0轴（列）求和，得到每列的和
print(arr.mean(axis=1))   # 沿1轴（行）求均值，得到每行的均值
print(arr.max())          # 最大值
print(arr.min())          # 最小值
print(arr.std())          # 标准差
print(arr.var())          # 方差
```

### 其他实用函数

1.  **累计运算 `cumsum()`, `cumprod()`**
    ```python
    arr = np.array([1, 2, 3, 4])
    print(np.cumsum(arr))  # 累计和：[ 1  3  6 10]
    print(np.cumprod(arr)) # 累计积：[ 1  2  6 24]
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_44.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_46.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_48.png)

2.  **条件函数 `np.where()`**
    类似于Excel的IF函数，根据条件替换值。
    ```python
    arr = np.array([65, 78, 92, 45, 88])
    # 大于80的替换为1，否则替换为0
    result = np.where(arr > 80, 1, 0)
    print(result) # [0 0 1 0 1]
    ```

3.  **集合运算**
    ```python
    a = np.array([1, 2, 3, 4])
    b = np.array([1, 2, 5])
    print(np.intersect1d(a, b)) # 交集：[1 2]
    print(np.union1d(a, b))     # 并集：[1 2 3 4 5]
    print(np.setdiff1d(a, b))   # 差集（a有b无）：[3 4]
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_50.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_52.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_54.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_56.png)

### 矩阵运算补充

![](img/6a115cfca3a1581e25c154e28f0c1a1f_58.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_60.png)

NumPy的`linalg`模块提供了线性代数运算。

![](img/6a115cfca3a1581e25c154e28f0c1a1f_62.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_64.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_66.png)

1.  **矩阵求逆**
    ```python
    from numpy.linalg import inv
    A = np.array([[1, 2], [3, 4]])
    A_inv = inv(A) # 求逆矩阵
    print(A_inv)
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_68.png)

2.  **特征值与特征向量**
    ```python
    from numpy.linalg import eig
    A = np.array([[1, 2], [2, 1]])
    eigenvalues, eigenvectors = eig(A) # 计算特征值和特征向量
    print("特征值:", eigenvalues)
    print("特征向量:\n", eigenvectors)
    ```

![](img/6a115cfca3a1581e25c154e28f0c1a1f_70.png)

![](img/6a115cfca3a1581e25c154e28f0c1a1f_72.png)

## 总结 📝

本节课中我们一起学习了NumPy库的核心知识。我们从NumPy数组的创建开始，逐步掌握了查看数组属性、选取数据、进行数据预处理（如类型转换、缺失值处理）和重塑数组的方法。接着，我们深入探讨了NumPy强大的计算能力，包括数组与标量的广播运算、数组间的运算、合并以及常用的数学和统计函数。最后，我们还简要介绍了条件判断、集合运算以及基础的线性代数操作。

![](img/6a115cfca3a1581e25c154e28f0c1a1f_74.png)

掌握NumPy是高效处理金融数值数据的第一步，它为后续使用Pandas进行更复杂的数据分析和建模打下了坚实的基础。请务必通过练习巩固这些操作，并尝试阅读官方文档来探索更多功能。