# Python金融量化与股票交易：P3：03 NumPy数组的三种创建方式 📊

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_1.png)

在本节课中，我们将学习数据分析“三剑客”中的第一个核心模块——NumPy。我们将重点掌握NumPy数组的基本概念，并详细讲解创建NumPy数组的三种主要方式。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_3.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_5.png)

## 概述：数据分析三剑客

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_7.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_9.png)

上一节我们完成了环境的安装与配置。本节中，我们将开始学习在数据分析中常用的核心模块。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_11.png)

在数据分析领域，有三个模块至关重要，它们被称为“数据分析三剑客”，分别是：
*   **NumPy** 模块
*   **pandas** 模块
*   **matplotlib** 模块

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_13.png)

我们后续所有的数据分析操作都将围绕这三个模块展开。其中，**pandas** 模块是学习的重点和核心操作对象。NumPy 和 matplotlib 虽然同样重要，但在整个课程体系中的权重相对较低。NumPy 模块的主要作用之一是为 pandas 模块提供数据源。

## NumPy模块简介 🔢

首先，我们来初步了解NumPy模块。

NumPy可以理解为一个用于存储**数值型数据**的一维或多维**数组**。如果你有其他编程语言（如C、Java）的经验，应该对数组不陌生。如果只接触过Python，可以暂时将NumPy数组视为一种特殊的列表，或者将列表视为一种特殊的数组。它们都是一种**数据结构**，即可以装载数据的**容器**。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_15.png)

**维度**的理解很简单：
*   **一维数组**：像一条线，例如 `[1, 2, 3]`。
*   **二维数组**：像一个平面（表格），有行和列，例如 `[[1, 2, 3], [4, 5, 6]]`。
*   更高维度的数组以此类推，但实际应用中一维、二维和三维数组最为常见。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_17.png)

NumPy数组与Python列表的一个关键区别在于：**NumPy数组要求内部所有元素的类型必须统一**。如果创建时传入的数据类型不一致，NumPy会自动将它们转换为同一类型。数据类型的转换优先级为：**字符串 > 浮点数 > 整数**。

NumPy的核心重点在于两点：
1.  **数值计算**：它是一个专为存储和运算**数值型数据**而设计的容器。
2.  **科学计算基础**：它封装了丰富的科学计算工具类（如各种数学函数），我们可以直接调用这些工具来对数组中的数据进行高效运算。

## 创建NumPy数组的三种方式 🛠️

介绍完NumPy的基本概念后，接下来我们看看如何创建NumPy数组。首先需要导入模块。

```python
import numpy as np
```

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_19.png)

我们约定俗成地使用 `np` 作为NumPy的别名。Anaconda环境已内置NumPy，无需单独安装。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_21.png)

### 方式一：使用 `np.array()` 从现有数据创建

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_23.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_25.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_27.png)

这是最直接的方式，可以将Python的列表、元组等序列类型转换为NumPy数组。

`np.array(object, dtype=None)` 函数接受两个主要参数：
*   `object`：数据源，例如一个列表。
*   `dtype`：可选参数，指定数组元素的数据类型。如果不指定，NumPy会自动推断。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_29.png)

以下是创建示例：

```python
# 创建一维数组
arr1 = np.array([1, 2, 3])
print(arr1)
# 输出: [1 2 3]

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_31.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_33.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_35.png)

# 创建二维数组
arr2 = np.array([[1, 2, 3], [4, 5, 6]])
print(arr2)
# 输出:
# [[1 2 3]
#  [4 5 6]]

# 演示数据类型自动统一
arr_mixed = np.array([1, 2.2, ‘three‘])
print(arr_mixed)
# 输出: [‘1‘ ‘2.2‘ ‘three‘] (全部转换为字符串类型)
```

### 方式二：使用 `plt.imread()` 从图像文件创建

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_37.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_39.png)

我们可以将外部图片的数据读取到NumPy数组中，这演示了NumPy作为数据容器的另一个应用场景。图片本质上是由二进制数据（数值）构成的，因此可以装入数组。

这里需要用到三剑客之一的 `matplotlib` 模块进行图像读取和显示。

```python
import matplotlib.pyplot as plt

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_41.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_43.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_45.png)

# 读取图片，返回一个NumPy数组（三维数组，代表图片的RGB通道和像素）
image_array = plt.imread(‘1.jpg‘)
print(image_array.shape) # 查看数组形状，例如 (高度, 宽度, 3)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_47.png)

# 显示原始图片
plt.imshow(image_array)
plt.show()

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_49.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_51.png)

# 对图片数据（即数组元素）进行运算，会影响图片显示效果
# 例如，将所有像素值减去100，图片会变暗
modified_image = image_array - 100
plt.imshow(modified_image)
plt.show()
```

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_53.png)

这个例子表明，对装载在NumPy数组中的图片数据进行数学运算，等同于对图片进行处理（如调色、滤镜等）。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_55.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_57.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_59.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_61.png)

### 方式三：使用NumPy内置函数快速创建

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_63.png)

NumPy提供了一系列函数，用于快速创建具有特定结构和内容的数组。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_65.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_67.png)

以下是几个常用函数：

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_69.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_71.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_73.png)

```python
# 1. 创建全零数组
zeros_arr = np.zeros((3, 4)) # 创建3行4列的全0二维数组
print(zeros_arr)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_75.png)

# 2. 创建全一数组
ones_arr = np.ones((2, 3)) # 创建2行3列的全1二维数组
print(ones_arr)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_77.png)

# 3. 创建等差数列数组 (一维)
# np.linspace(start, stop, num)
lin_arr = np.linspace(0, 100, 20) # 在0到100之间生成20个等间隔的数
print(lin_arr)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_79.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_81.png)

# 4. 创建等差间隔数组 (一维)
# np.arange(start, stop, step)
range_arr = np.arange(10, 50, 2) # 从10开始，到50结束（不含50），步长为2
print(range_arr)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_83.png)

# 5. 创建随机整数数组
# np.random.randint(low, high, size)
random_arr = np.random.randint(0, 100, (5, 3)) # 创建5行3列，元素为0-100随机整数的数组
print(random_arr)
```

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_85.png)

**函数对比小结**：
*   `zeros`/`ones`：可创建任意维度的数组，填充固定值。
*   `linspace`/`arange`：主要用于创建**一维**的等差数列。
*   `random` 相关函数：用于创建随机数数组。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_87.png)

## 总结 📝

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_89.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_91.png)

本节课我们一起学习了NumPy模块的基础知识以及创建NumPy数组的三种主要方式：

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_93.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_95.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_97.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_99.png)

1.  **`np.array()`**：从已有的列表、元组等数据结构创建，是最灵活的方式。
2.  **`plt.imread()`**：从图像文件创建，数组中的数据即为图像的像素值，便于进行图像处理。
3.  **NumPy内置函数**（如 `zeros`, `ones`, `linspace`, `arange`, `random`）：用于快速创建具有特定规律（如全零、全等间隔、随机）的数组。

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_101.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_103.png)

![](img/5405dc9edb411deeb67a6f7d8b74ebe5_105.png)

掌握这些创建方法是使用NumPy进行数值计算的第一步。我们已经了解到NumPy数组是一个要求元素类型统一的、强大的数值容器。在接下来的课程中，我们将学习如何操作和使用这些数组进行数据分析。