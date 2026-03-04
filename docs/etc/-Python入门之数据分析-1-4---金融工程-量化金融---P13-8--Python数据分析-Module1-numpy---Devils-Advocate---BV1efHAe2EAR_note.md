# 量化交易：Python入门之数据分析【1／4】：1：NumPy基础入门 🧮

在本节课中，我们将学习NumPy的基础知识。NumPy是Python中用于科学计算的核心库，尤其在处理数组、矩阵运算和线性代数方面功能强大。它在金融数据分析、机器学习和人工智能等领域应用广泛。掌握NumPy是进行高效数据分析的第一步。

## 导入NumPy库

首先，我们需要导入NumPy库。在Python中，通常使用`import numpy as np`来导入，并将`np`作为其别名，这是一种广泛使用的约定。

![](img/e9f684ad6d2de87e46e424b90cdeecca_1.png)

```python
import numpy as np
```

## 创建NumPy数组

![](img/e9f684ad6d2de87e46e424b90cdeecca_3.png)

NumPy的核心数据结构是`ndarray`，即多维数组。我们可以通过多种方式创建它。

![](img/e9f684ad6d2de87e46e424b90cdeecca_5.png)

### 从列表创建数组

![](img/e9f684ad6d2de87e46e424b90cdeecca_7.png)

最直接的方法是将Python列表转换为NumPy数组。

![](img/e9f684ad6d2de87e46e424b90cdeecca_8.png)

```python
my_list = [1, 2, 3]
x = np.array(my_list)
print(x)  # 输出: [1 2 3]
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_10.png)

除了先创建列表再转换，也可以直接将列表作为参数传入`np.array()`函数。

![](img/e9f684ad6d2de87e46e424b90cdeecca_12.png)

```python
y = np.array([3, 4, 5, 6])
print(y)  # 输出: [3 4 5 6]
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_14.png)

### 创建多维数组

![](img/e9f684ad6d2de87e46e424b90cdeecca_15.png)

我们可以通过嵌套列表来创建二维或更高维度的数组。

![](img/e9f684ad6d2de87e46e424b90cdeecca_17.png)

```python
z = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
print(z)
# 输出:
# [[1 2 3]
#  [4 5 6]
#  [7 8 9]]
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_18.png)

可以使用`.shape`属性查看数组的维度。

![](img/e9f684ad6d2de87e46e424b90cdeecca_20.png)

```python
print(z.shape)  # 输出: (3, 3)，表示3行3列
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_22.png)

## 生成特定序列的数组

NumPy提供了多种函数来生成具有特定规律的数组。

![](img/e9f684ad6d2de87e46e424b90cdeecca_24.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_26.png)

### 使用`np.arange`

![](img/e9f684ad6d2de87e46e424b90cdeecca_28.png)

`np.arange`函数类似于Python的`range`，但生成的是NumPy数组。它可以指定起始值、结束值和步长。

![](img/e9f684ad6d2de87e46e424b90cdeecca_30.png)

```python
a = np.arange(0, 30, 2)  # 从0开始，到30结束（不包含30），步长为2
print(a)  # 输出: [ 0  2  4  6  8 10 12 14 16 18 20 22 24 26 28]
```

### 使用`np.linspace`

`np.linspace`函数用于在指定区间内生成指定数量的等间隔数字。

![](img/e9f684ad6d2de87e46e424b90cdeecca_32.png)

```python
b = np.linspace(0, 4, 9)  # 在0到4之间生成9个等间隔的数
print(b)  # 输出: [0.  0.5 1.  1.5 2.  2.5 3.  3.5 4. ]
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_34.png)

## 改变数组形状

![](img/e9f684ad6d2de87e46e424b90cdeecca_35.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_36.png)

我们可以改变现有数组的形状，而不改变其数据。

![](img/e9f684ad6d2de87e46e424b90cdeecca_38.png)

### 使用`reshape`

![](img/e9f684ad6d2de87e46e424b90cdeecca_40.png)

`reshape`方法返回一个具有新形状的数组视图。

![](img/e9f684ad6d2de87e46e424b90cdeecca_41.png)

```python
arr = np.arange(15)  # 创建一个0到14的一维数组
reshaped_arr = arr.reshape(3, 5)  # 重塑为3行5列的二维数组
print(reshaped_arr)
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_43.png)

### 使用`resize`

![](img/e9f684ad6d2de87e46e424b90cdeecca_45.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_47.png)

`resize`方法会直接修改原数组的形状。如果新形状大于原数组，会用原数组的元素重复填充。

![](img/e9f684ad6d2de87e46e424b90cdeecca_49.png)

```python
arr2 = np.array([1, 2, 3, 4, 5, 6])
arr2.resize(3, 3)
print(arr2)
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_51.png)

## 创建特殊数组

![](img/e9f684ad6d2de87e46e424b90cdeecca_53.png)

NumPy提供了快速创建全0、全1、单位矩阵等特殊数组的函数。

![](img/e9f684ad6d2de87e46e424b90cdeecca_55.png)

### 全0和全1数组

![](img/e9f684ad6d2de87e46e424b90cdeecca_57.png)

```python
zeros_arr = np.zeros((3, 2))  # 创建一个3行2列的全0数组
print(zeros_arr)

![](img/e9f684ad6d2de87e46e424b90cdeecca_58.png)

ones_arr = np.ones((3, 2), dtype=int)  # 创建一个3行2列的全1整数数组
print(ones_arr)
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_59.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_61.png)

### 单位矩阵

![](img/e9f684ad6d2de87e46e424b90cdeecca_63.png)

单位矩阵是一个方阵，主对角线上的元素为1，其余为0。

```python
identity_matrix = np.eye(3)  # 创建一个3x3的单位矩阵
print(identity_matrix)
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_65.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_66.png)

### 对角矩阵

![](img/e9f684ad6d2de87e46e424b90cdeecca_68.png)

`np.diag`函数可以从给定的对角线元素创建对角矩阵。

```python
diag_matrix = np.diag([4, 5, 6])
print(diag_matrix)
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_70.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_71.png)

## 数组的重复与堆叠

![](img/e9f684ad6d2de87e46e424b90cdeecca_73.png)

上一节我们介绍了如何创建和重塑数组，本节中我们来看看如何复制和组合数组。

![](img/e9f684ad6d2de87e46e424b90cdeecca_75.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_77.png)

### 重复数组元素

![](img/e9f684ad6d2de87e46e424b90cdeecca_79.png)

`np.tile`函数用于重复整个数组。

![](img/e9f684ad6d2de87e46e424b90cdeecca_81.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_82.png)

```python
tiled_arr = np.tile([1, 2, 3], 3)  # 将数组[1,2,3]整体重复3次
print(tiled_arr)  # 输出: [1 2 3 1 2 3 1 2 3]
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_84.png)

`np.repeat`函数用于重复数组中的每个元素。

![](img/e9f684ad6d2de87e46e424b90cdeecca_86.png)

```python
repeated_arr = np.repeat([1, 2, 3], 3)  # 将每个元素重复3次
print(repeated_arr)  # 输出: [1 1 1 2 2 2 3 3 3]
```

### 堆叠数组

![](img/e9f684ad6d2de87e46e424b90cdeecca_88.png)

堆叠操作可以将多个数组合并成一个。

**垂直堆叠 (vstack)**：将数组在垂直方向（行方向）上堆叠。

![](img/e9f684ad6d2de87e46e424b90cdeecca_90.png)

```python
y = np.ones((2, 3), dtype=int)  # 创建一个2行3列的全1数组
vstacked = np.vstack((y, y * 2))  # 将y和y*2垂直堆叠
print(vstacked)
```

**水平堆叠 (hstack)**：将数组在水平方向（列方向）上堆叠。

![](img/e9f684ad6d2de87e46e424b90cdeecca_92.png)

```python
hstacked = np.hstack((y, y * 2))  # 将y和y*2水平堆叠
print(hstacked)
```

![](img/e9f684ad6d2de87e46e424b90cdeecca_93.png)

![](img/e9f684ad6d2de87e46e424b90cdeecca_94.png)

## 总结

![](img/e9f684ad6d2de87e46e424b90cdeecca_95.png)

本节课中我们一起学习了NumPy库的基础操作。我们首先学习了如何导入NumPy以及从列表创建数组。接着，我们探索了生成特定序列数组（如`arange`和`linspace`）和改变数组形状（`reshape`和`resize`）的方法。然后，我们介绍了创建全0、全1、单位矩阵和对角矩阵等特殊数组的函数。最后，我们了解了如何重复数组元素（`tile`和`repeat`）以及如何垂直和水平堆叠数组（`vstack`和`hstack`）。这些基础操作是后续进行更复杂数值计算和数据分析的基石。