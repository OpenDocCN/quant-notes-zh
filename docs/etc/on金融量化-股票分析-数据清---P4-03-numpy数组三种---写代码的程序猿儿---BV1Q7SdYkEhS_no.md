# Python金融量化：P4：03 NumPy数组三种创建方式 📊

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_1.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_3.png)

在本节课中，我们将要学习数据分析“三剑客”之一——NumPy模块的基本使用。NumPy是Python科学计算的基础库，核心在于处理**数值型数据**的运算。本节课的重点是掌握创建NumPy数组的三种主要方式。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_5.png)

上一节我们介绍了环境的安装，本节中我们来看看在数据分析中常用模块的基本使用。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_7.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_9.png)

在数据分析领域，有三个核心模块需要掌握，它们被称为数据分析的“三剑客”，分别是：
*   **NumPy** 模块
*   **pandas** 模块
*   **matplotlib** 模块

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_11.png)

我们后续所有的数据分析操作都将围绕这三个模块展开。其中，**pandas** 模块是学习的重点和核心操作对象。NumPy 和 matplotlib 模块同样重要，但相对于 pandas 而言，其核心地位稍低。NumPy 模块在后期的学习中，主要扮演为 pandas 模块提供数据源的角色。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_13.png)

## NumPy模块简介 🔍

首先，我们来大致了解一下NumPy模块。

NumPy模块的核心是一个称为“数组”的数据结构。你可以将其理解为一个一维或多维的数组。如果你有其他编程语言（如C、Java）的经验，应该对数组不陌生。如果只接触过Python，可以暂时将NumPy数组视为一种特殊的列表，或者将列表视为一种特殊的数组。无论是列表还是数组，它们都是一种**数据结构**，即一种可以装载数据的**容器**。

NumPy数组这个容器可以装载数据，并且它主要装载**数值型数据**。因此，NumPy模块的重点在于**数值计算**。当我们需要对一组数值进行某种运算时，可以先将这些数值装载到NumPy数组中，然后利用数组的特性进行运算。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_15.png)

此外，NumPy模块是Python**科学计算库的基础**。这意味着NumPy内部已经封装好了各种各样用于科学计算的工具类。例如，如果需要对一组数据进行反三角函数运算，我们无需手动实现，直接调用NumPy中封装好的对应函数即可。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_17.png)

总结NumPy的两大重点：
1.  它是一个容器，用于装载**数值型数据**并进行运算。
2.  它是Python科学计算的基础，提供了丰富的科学计算工具。

## 创建NumPy数组 🛠️

介绍完NumPy后，接下来我们看看如何使用它。首先需要创建NumPy数组。

使用前需要导入模块，业界通常使用别名 `np`。

```python
import numpy as np
```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_19.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_21.png)

NumPy模块通常无需单独安装，在安装Anaconda集成环境时已自动包含。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_23.png)

### 方式一：使用 `np.array()` 从列表创建

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_25.png)

第一种创建数组的方式是使用 `np.array()` 函数，它可以将列表转换为NumPy数组。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_27.png)

以下是使用 `np.array()` 创建数组的步骤：

1.  **创建一维数组**：传入一个一维列表作为数据源。
    ```python
    arr1 = np.array([1, 2, 3])
    arr1
    ```
    执行上述代码，将创建一个一维数组 `array([1, 2, 3])`。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_29.png)

2.  **创建多维数组**：传入一个嵌套列表（列表的列表）作为数据源。
    ```python
    arr2 = np.array([[1, 2, 3], [4, 5, 6]])
    arr2
    ```
    执行上述代码，将创建一个二维数组。输出显示为两行三列，这表示一个二维平面。同理，可以创建更高维度的数组，但实际应用中最常见的是**一维、二维和三维数组**。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_31.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_33.png)

**数组与列表的核心区别**：
列表可以容纳不同类型的元素，但NumPy数组要求所有元素的**数据类型必须统一**。如果传入的数据类型不一致，NumPy会按照一定规则进行强制类型转换。

数据类型转换的优先级规则是：**字符串 > 浮点数 > 整数**。
```python
# 示例：混合类型数据
arr_mixed = np.array([1, 2.2, ‘three‘])
arr_mixed
# 输出：array([‘1‘, ‘2.2‘, ‘three‘], dtype=‘<U32‘) 全部转换为字符串类型

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_35.png)

arr_mixed2 = np.array([1, 2.2, 3.3])
arr_mixed2
# 输出：array([1. , 2.2, 3.3]) 全部转换为浮点数类型
```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_37.png)

### 方式二：使用 `plt.imread()` 从图像文件创建

第二种创建数组的方式涉及将外部数据读入数组。这里我们以一个需求为例：将一张外部图片读取并加载到NumPy数组中，然后通过修改数组值来观察对原始图片的影响。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_39.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_41.png)

数据装载在不同的容器（如列表、字典、数组）中，容器自身的特性决定了能对数据进行的操作。NumPy数组的特性使得我们可以方便地对数值型数据进行批量运算。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_43.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_45.png)

以下是将图片读入NumPy数组的步骤：

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_47.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_49.png)

1.  **导入必要的模块**：这里需要用到三剑客中的 `matplotlib` 模块进行图像读取和显示。
    ```python
    import matplotlib.pyplot as plt
    ```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_51.png)

2.  **读取图片到数组**：使用 `plt.imread()` 函数读取图片文件，其返回值就是一个NumPy数组。
    ```python
    image_array = plt.imread(‘1.jpg‘) # 假设当前目录下有1.jpg文件
    image_array
    ```
    执行后，`image_array` 是一个三维NumPy数组，其数据对应图片的像素信息（通常是高度、宽度和RGB颜色通道）。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_53.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_55.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_57.png)

3.  **显示和操作图片数组**：我们可以显示这个数组对应的图片，并通过数组运算来修改图片。
    ```python
    plt.imshow(image_array) # 显示原始图片
    plt.show()

    modified_array = image_array - 100 # 将所有像素值减去100
    plt.imshow(modified_array) # 显示修改后的图片
    plt.show()
    ```
    执行后，你会发现第二张图片变暗了。这演示了图片处理软件（如美图秀秀）的基本原理：通过特定的算法对图片的像素数据（即NumPy数组）进行运算，从而实现各种效果。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_59.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_61.png)

### 方式三：使用NumPy内置函数创建

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_63.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_65.png)

第三种方式是直接使用NumPy提供的一系列内置函数来生成具有特定规律或属性的数组。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_67.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_69.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_71.png)

以下是几种常用的创建函数：

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_73.png)

*   **`np.zeros(shape)` / `np.ones(shape)`**：创建指定形状（`shape`），元素全为0或全为1的数组。`shape` 参数可以指定维度，例如 `(3, 4)` 表示3行4列。
    ```python
    np.zeros((3, 4)) # 创建3行4列的全0数组
    np.ones((2, 3))  # 创建2行3列的全1数组
    ```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_75.png)

*   **`np.linspace(start, stop, num)`**：在指定的起始值（`start`）和结束值（`stop`）之间，生成等间隔的 `num` 个数值，返回一个**一维等差数列数组**。
    ```python
    np.linspace(0, 100, 20) # 在0到100之间生成20个等间隔的数
    ```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_77.png)

*   **`np.arange(start, stop, step)`**：类似于Python的 `range()` 函数，生成从 `start` 到 `stop`（不包括），步长为 `step` 的数组，返回一个**一维等差数列数组**。
    ```python
    np.arange(10, 50, 2) # 生成10到50（不含50），步长为2的数组
    ```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_79.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_81.png)

*   **`np.random.randint(low, high, size)`**：生成指定形状（`size`）的数组，数组中的每个元素都是 `[low, high)` 区间内的随机整数。
    ```python
    np.random.randint(0, 100, (5, 3)) # 生成5行3列的数组，元素为0到99的随机整数
    ```

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_83.png)

**小结这几种函数**：
`zeros` 和 `ones` 可以创建任意维度的数组。`linspace` 和 `arange` 主要用于创建**一维**的规律数组。`random` 相关函数用于创建随机数数组。

## 总结 📝

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_85.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_87.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_89.png)

本节课中我们一起学习了NumPy数组的三种创建方式：

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_91.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_93.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_95.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_97.png)

1.  **`np.array(list)`**：从Python列表或嵌套列表创建，是最直接的方式，需注意数组元素的**数据类型必须统一**。
2.  **`plt.imread(‘image.jpg‘)`**：从外部图像文件读取像素数据并创建多维数组，常用于图像处理。
3.  **NumPy内置函数**：如 `zeros`, `ones`, `linspace`, `arange`, `random.randint` 等，用于快速创建具有特定规律（如全零、等差数列、随机数）的数组。

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_99.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_101.png)

![](img/6f5c2680bbb1b3dcaf66c69a06e70f56_103.png)

掌握这些创建数组的方法是使用NumPy进行数值计算和数据分析的第一步。创建数组后，我们才能进一步利用NumPy强大的功能对数组中的数据进行各种操作和运算。