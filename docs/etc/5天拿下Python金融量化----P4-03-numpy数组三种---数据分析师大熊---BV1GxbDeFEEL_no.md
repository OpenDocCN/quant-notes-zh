# Python金融量化：P4：03 numpy数组三种神秘创建方式 🧮

![](img/4d1fd31d197aa24622fcc96be7e87b82_1.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_3.png)

在本节课中，我们将要学习数据分析“三剑客”之一——NumPy模块的基本使用。NumPy是Python科学计算的基础库，其核心是**数组**对象，它能够高效地存储和操作数值型数据。本节课的重点是掌握创建NumPy数组的三种主要方式。

![](img/4d1fd31d197aa24622fcc96be7e87b82_5.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_7.png)

上一节我们介绍了环境的安装，本节中我们来看看在数据分析中常用模块的基本使用。

![](img/4d1fd31d197aa24622fcc96be7e87b82_9.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_11.png)

在整个数据分析中，有三个核心模块需要掌握，它们被称为数据分析的“三剑客”：
*   **NumPy**：用于高效的数值计算，是科学计算的基础。
*   **pandas**：用于数据清洗、处理和分析，是课程的重点。
*   **matplotlib**：用于数据可视化。

![](img/4d1fd31d197aa24622fcc96be7e87b82_13.png)

我们整个课程的核心是学习pandas模块，而NumPy模块的主要作用之一是为pandas提供数据源。首先，让我们从NumPy模块开始。

## NumPy模块简介 📦

NumPy模块的核心是一个称为**数组（ndarray）** 的容器。你可以将其理解为一个加强版的列表，但它专为数值计算设计。

与Python列表的关键区别在于：**NumPy数组中的所有元素必须是相同的数据类型**。如果创建时传入不同类型的数据，NumPy会自动将它们转换为统一的类型（优先级：字符串 > 浮点数 > 整数）。

![](img/4d1fd31d197aa24622fcc96be7e87b82_15.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_17.png)

NumPy数组可以是**一维**的（类似列表），也可以是**多维**的（例如二维数组可看作矩阵或表格）。它的两大核心作用是：
1.  作为容器，高效存储和计算**数值型数据**。
2.  作为Python科学计算库的基础，内置了大量数学函数和工具，可以直接对数组进行复杂的科学运算（如三角函数、矩阵运算等）。

## 创建NumPy数组的三种方式 🛠️

了解了NumPy的基本概念后，接下来我们学习如何创建NumPy数组。主要有以下三种方式。

### 方式一：使用 `np.array()` 从现有数据创建

这是最直接的方式，可以将Python的列表或元组等序列转换为NumPy数组。

![](img/4d1fd31d197aa24622fcc96be7e87b82_19.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_21.png)

首先，我们需要导入NumPy模块，并约定俗成地将其简写为 `np`。

![](img/4d1fd31d197aa24622fcc96be7e87b82_23.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_25.png)

```python
import numpy as np
```

![](img/4d1fd31d197aa24622fcc96be7e87b82_27.png)

以下是使用 `np.array()` 创建数组的示例：

![](img/4d1fd31d197aa24622fcc96be7e87b82_29.png)

```python
# 创建一维数组
arr_1d = np.array([1, 2, 3])
print(arr_1d)
# 输出: [1 2 3]

# 创建二维数组
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(arr_2d)
# 输出:
# [[1 2 3]
#  [4 5 6]]

![](img/4d1fd31d197aa24622fcc96be7e87b82_31.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_33.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_35.png)

# 演示数据类型统一
arr_mix = np.array([1, 2.2, ‘three‘])
print(arr_mix)
# 输出: [‘1‘ ‘2.2‘ ‘three‘] (全部被转换为字符串类型)
```

`np.array()` 函数的关键参数是 `object`（数据源）和 `dtype`（指定数组元素的数据类型，可选）。

### 方式二：使用 `plt.imread()` 从图像文件创建

![](img/4d1fd31d197aa24622fcc96be7e87b82_37.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_39.png)

我们可以利用matplotlib库读取外部图片，并将其像素数据加载到一个NumPy数组中。这演示了NumPy数组作为数据容器的强大能力——图像本质上就是由数值（像素值）构成的。

```python
import matplotlib.pyplot as plt

![](img/4d1fd31d197aa24622fcc96be7e87b82_41.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_43.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_45.png)

# 读取图片，数据存储在三维NumPy数组中（高度，宽度，颜色通道）
image_array = plt.imread(‘1.jpg‘)
print(image_array.shape) # 查看数组形状，例如 (360, 640, 3)

![](img/4d1fd31d197aa24622fcc96be7e87b82_47.png)

# 显示原始图片
plt.imshow(image_array)
plt.show()

![](img/4d1fd31d197aa24622fcc96be7e87b82_49.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_51.png)

# 对图片数据进行运算（例如，将所有像素值减100，图片会变暗）
modified_image = image_array - 100
plt.imshow(modified_image)
plt.show()
```

![](img/4d1fd31d197aa24622fcc96be7e87b82_53.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_55.png)

通过操作这个数组中的数值，我们就能实现像调整亮度、对比度等基本的图像处理效果。

![](img/4d1fd31d197aa24622fcc96be7e87b82_57.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_59.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_61.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_63.png)

### 方式三：使用NumPy内置函数快速创建特殊数组

![](img/4d1fd31d197aa24622fcc96be7e87b82_65.png)

NumPy提供了一系列函数，可以快速创建具有特定规律的数组，这在数值计算和模拟中非常常用。

![](img/4d1fd31d197aa24622fcc96be7e87b82_67.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_69.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_71.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_73.png)

以下是几个核心函数：

![](img/4d1fd31d197aa24622fcc96be7e87b82_75.png)

```python
# 1. 创建全0或全1数组
zeros_arr = np.zeros((3, 4)) # 创建3行4列的全0数组
ones_arr = np.ones((2, 3))   # 创建2行3列的全1数组

![](img/4d1fd31d197aa24622fcc96be7e87b82_77.png)

# 2. 创建等差数列数组 (一维)
# np.linspace(起点， 终点， 元素个数)
lin_arr = np.linspace(0, 100, 20) # 在0到100之间生成20个等间隔的数

![](img/4d1fd31d197aa24622fcc96be7e87b82_79.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_81.png)

# 3. 创建等差间隔数组 (一维)
# np.arange(起点， 终点， 步长)
range_arr = np.arange(10, 50, 2) # 从10开始，到50结束（不含50），步长为2

![](img/4d1fd31d197aa24622fcc96be7e87b82_83.png)

# 4. 创建随机整数数组
# np.random.randint(最小值， 最大值， size=形状)
random_arr = np.random.randint(0, 100, size=(5, 3)) # 5行3列，元素为0-100的随机整数
```

![](img/4d1fd31d197aa24622fcc96be7e87b82_85.png)

这些函数中，`zeros`、`ones`、`random.randint` 可以通过 `size` 或 `shape` 参数创建多维数组，而 `linspace` 和 `arange` 通常用于创建一维数组。

![](img/4d1fd31d197aa24622fcc96be7e87b82_87.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_89.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_91.png)

## 总结 📝

![](img/4d1fd31d197aa24622fcc96be7e87b82_93.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_95.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_97.png)

本节课中我们一起学习了NumPy模块的基础知识以及创建NumPy数组的三种核心方式：
1.  **`np.array()`**：从列表、元组等现有数据结构转换，是最灵活的方式。
2.  **`plt.imread()`**：从图像文件读取，将像素数据加载为多维数组，展示了数组作为数据容器的应用。
3.  **NumPy内置函数**：如 `zeros`、`ones`、`linspace`、`arange`、`random.randint` 等，用于快速生成具有特定规律（如全零、等差、随机）的数组。

![](img/4d1fd31d197aa24622fcc96be7e87b82_99.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_101.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_103.png)

![](img/4d1fd31d197aa24622fcc96be7e87b82_105.png)

掌握这些创建数组的方法是使用NumPy进行后续数值计算和数据分析的第一步。数组创建出来后，我们才能利用NumPy强大的功能对其中的数据进行各种运算和处理。