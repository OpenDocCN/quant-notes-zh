# 金融量化分析：P11：NumPy数组创建 🏗️

在本节课中，我们将学习如何使用NumPy库创建各种类型的数组。这是进行金融数据分析和量化计算的基础。

![](img/003ddb847cee6a01986d158f10997361_1.png)

![](img/003ddb847cee6a01986d158f10997361_3.png)

![](img/003ddb847cee6a01986d158f10997361_5.png)

上一节我们介绍了ndarray的常用属性，本节中我们来看看如何创建数组。我们已经知道可以使用`np.array()`方法将列表转换为数组。除此之外，NumPy还提供了多种便捷的函数来创建具有特定内容的数组。

## 创建特定值的数组

![](img/003ddb847cee6a01986d158f10997361_7.png)

![](img/003ddb847cee6a01986d158f10997361_9.png)

以下是几种创建具有特定初始值数组的方法。

![](img/003ddb847cee6a01986d158f10997361_11.png)

![](img/003ddb847cee6a01986d158f10997361_13.png)

### 创建全零数组
使用`np.zeros()`函数可以创建一个所有元素都为0的数组。默认情况下，数组的元素类型是浮点数（`float64`）。
```python
import numpy as np
a = np.zeros(10)
print(a.dtype)  # 输出：float64
```
如果你希望创建整数类型的全零数组，可以通过`dtype`参数指定。
```python
a_int = np.zeros(10, dtype=int)
print(a_int.dtype)  # 输出：int64
```

![](img/003ddb847cee6a01986d158f10997361_15.png)

![](img/003ddb847cee6a01986d158f10997361_17.png)

![](img/003ddb847cee6a01986d158f10997361_19.png)

### 创建全一数组
与`np.zeros()`类似，`np.ones()`函数用于创建所有元素都为1的数组。
```python
b = np.ones(5)  # 创建一个包含5个1.0的数组
b_int = np.ones(5, dtype=int)  # 创建一个包含5个1的整数数组
```

![](img/003ddb847cee6a01986d158f10997361_21.png)

![](img/003ddb847cee6a01986d158f10997361_23.png)

![](img/003ddb847cee6a01986d158f10997361_25.png)

### 创建“空”数组
`np.empty()`函数用于创建一个“空”数组。这里的“空”并非指值为零或None，而是指分配内存后，不进行初始化，数组中的值是内存中残留的、未定义的随机数据。
```python
c = np.empty(100)
print(c)  # 输出内容为随机值
```
使用`empty`比使用`zeros`少了一步初始化操作，因此速度稍快。但前提是你后续会覆盖数组中的所有值。如果不确定，使用`zeros`或`ones`更安全。

![](img/003ddb847cee6a01986d158f10997361_27.png)

![](img/003ddb847cee6a01986d158f10997361_29.png)

## 创建数值序列数组

![](img/003ddb847cee6a01986d158f10997361_31.png)

![](img/003ddb847cee6a01986d158f10997361_33.png)

![](img/003ddb847cee6a01986d158f10997361_35.png)

在Python列表中有`range`函数，NumPy也提供了类似的数组创建函数。

![](img/003ddb847cee6a01986d158f10997361_37.png)

![](img/003ddb847cee6a01986d158f10997361_39.png)

### 使用`np.arange`
`np.arange`的用法与Python的`range`类似，用于创建等差序列的数组。
```python
# 从0开始，到99结束（前闭后开）
arr1 = np.arange(100)
# 从5开始，到15结束，步长为2
arr2 = np.arange(5, 15, 2)
```
与`range`不同的是，`np.arange`的步长可以是小数。
```python
arr3 = np.arange(0, 1, 0.1)  # 创建一个从0到1，步长为0.1的数组
```

![](img/003ddb847cee6a01986d158f10997361_41.png)

![](img/003ddb847cee6a01986d158f10997361_43.png)

![](img/003ddb847cee6a01986d158f10997361_45.png)

### 使用`np.linspace`
`np.linspace`用于在指定的区间内，创建包含指定数量等间距点的数组。它的第三个参数是点的数量，而不是步长。
```python
# 在0到100之间（包含0和100），生成101个等间距的点
lin_arr = np.linspace(0, 100, 101)
```
`linspace`生成的数组是**前闭后闭**的区间，而`arange`是前闭后开。`linspace`的一个典型应用场景是生成用于绘制函数图像的密集点集。
```python
# 生成-10到10之间的10000个点，用于绘制函数图像
x = np.linspace(-10, 10, 10000)
y = x ** 2  # 计算y = x^2
# （此处假设使用matplotlib绘图）plt.plot(x, y)
```

![](img/003ddb847cee6a01986d158f10997361_47.png)

![](img/003ddb847cee6a01986d158f10997361_49.png)

![](img/003ddb847cee6a01986d158f10997361_51.png)

## 创建特殊矩阵

![](img/003ddb847cee6a01986d158f10997361_53.png)

![](img/003ddb847cee6a01986d158f10997361_55.png)

最后，介绍一个在线性代数中常用的函数。

![](img/003ddb847cee6a01986d158f10997361_57.png)

![](img/003ddb847cee6a01986d158f10997361_59.png)

### 创建单位矩阵
`np.eye(N)`用于创建一个N×N的单位矩阵（对角线元素为1，其余为0）。
```python
identity_matrix = np.eye(3)
print(identity_matrix)
# 输出：
# [[1. 0. 0.]
#  [0. 1. 0.]
#  [0. 0. 1.]]
```
如果不涉及线性代数运算，这个函数使用频率较低。

![](img/003ddb847cee6a01986d158f10997361_61.png)

![](img/003ddb847cee6a01986d158f10997361_63.png)

![](img/003ddb847cee6a01986d158f10997361_65.png)

![](img/003ddb847cee6a01986d158f10997361_67.png)

---

![](img/003ddb847cee6a01986d158f10997361_69.png)

![](img/003ddb847cee6a01986d158f10997361_71.png)

![](img/003ddb847cee6a01986d158f10997361_73.png)

本节课中我们一起学习了多种创建NumPy数组的方法，包括创建全零、全一、空数组，生成数值序列（`arange`, `linspace`）以及创建单位矩阵。掌握这些方法是高效处理金融数据的第一步。下一节，我们将学习如何对数组进行索引和切片操作。