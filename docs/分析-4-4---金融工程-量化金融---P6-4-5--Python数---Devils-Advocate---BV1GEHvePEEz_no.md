# 量化交易：Python入门之数据分析：4-5. Python数据分析：Matplotlib 3D 图像 📊

在本节课中，我们将学习如何使用Matplotlib库创建3D图像。我们将从基础的3D散点图开始，逐步介绍如何绘制线框图、曲面图和等高线图，帮助你直观地展示和分析多维数据。

![](img/8bc7851023f14ec9d09edcfe41374718_1.png)

![](img/8bc7851023f14ec9d09edcfe41374718_2.png)

---

## 概述

![](img/8bc7851023f14ec9d09edcfe41374718_4.png)

本节课程将介绍Matplotlib的3D绘图功能。我们将使用经典的鸢尾花数据集，演示如何将二维散点图扩展为三维散点图，并探索其他几种常见的3D图形。

---

## 准备工作

首先，我们需要导入必要的库并加载数据。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits import mplot3d
%matplotlib notebook

![](img/8bc7851023f14ec9d09edcfe41374718_6.png)

# 读取数据
iris = pd.read_csv('iris.csv')
```

数据包含以下列：`sepal_length`（萼片长度）、`sepal_width`（萼片宽度）、`petal_length`（花瓣长度）、`petal_width`（花瓣宽度）和`name`（种类名称）。

![](img/8bc7851023f14ec9d09edcfe41374718_8.png)

![](img/8bc7851023f14ec9d09edcfe41374718_9.png)

---

![](img/8bc7851023f14ec9d09edcfe41374718_11.png)

## 绘制二维散点图

在进入3D绘图之前，我们先回顾一下如何用二维散点图展示数据。这里我们使用花瓣的长度和宽度作为两个维度。

![](img/8bc7851023f14ec9d09edcfe41374718_13.png)

以下是绘制二维散点图的步骤：

![](img/8bc7851023f14ec9d09edcfe41374718_14.png)

1.  创建一个图形。
2.  按花的种类分组数据。
3.  为每个种类绘制散点图，并设置标签。
4.  添加坐标轴标签和图例。

```python
plt.figure()
for species, iris_subset in iris.groupby('name'):
    plt.scatter(iris_subset['petal_length'],
                iris_subset['petal_width'],
                alpha=0.8,
                label=species)
plt.xlabel('Petal Length')
plt.ylabel('Petal Width')
plt.legend()
plt.show()
```

![](img/8bc7851023f14ec9d09edcfe41374718_16.png)

运行代码后，我们可以看到三种鸢尾花（Setosa, Versicolor, Virginica）在花瓣长宽特征上的分布情况。

![](img/8bc7851023f14ec9d09edcfe41374718_18.png)

---

## 升级为三维散点图

![](img/8bc7851023f14ec9d09edcfe41374718_20.png)

![](img/8bc7851023f14ec9d09edcfe41374718_21.png)

上一节我们介绍了二维散点图，本节中我们来看看如何增加一个维度，将其升级为三维散点图。我们将把`sepal_length`（萼片长度）作为第三个维度（Z轴）加入。

以下是绘制三维散点图的关键步骤：

![](img/8bc7851023f14ec9d09edcfe41374718_23.png)

![](img/8bc7851023f14ec9d09edcfe41374718_25.png)

1.  创建图形时，指定投影方式为`3d`。
2.  使用`ax.scatter3D`方法进行三维绘图。
3.  分别为X、Y、Z轴设置标签。

![](img/8bc7851023f14ec9d09edcfe41374718_27.png)

```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')

for species, iris_subset in iris.groupby('name'):
    ax.scatter3D(iris_subset['sepal_length'],  # X轴：萼片长度
                 iris_subset['petal_length'],   # Y轴：花瓣长度
                 iris_subset['petal_width'],    # Z轴：花瓣宽度
                 alpha=0.8,
                 label=species)

![](img/8bc7851023f14ec9d09edcfe41374718_29.png)

![](img/8bc7851023f14ec9d09edcfe41374718_30.png)

ax.set_xlabel('Sepal Length')
ax.set_ylabel('Petal Length')
ax.set_zlabel('Petal Width')
ax.legend(loc='upper left')  # 设置图例位置，避免报错
plt.show()
```

现在，我们得到了一个可以旋转、缩放的三维散点图，能更清晰地观察三种花在三个特征维度上的聚类情况。

![](img/8bc7851023f14ec9d09edcfe41374718_32.png)

---

## 探索其他3D图形

![](img/8bc7851023f14ec9d09edcfe41374718_34.png)

![](img/8bc7851023f14ec9d09edcfe41374718_35.png)

除了散点图，Matplotlib还支持多种其他3D图形。下面我们快速浏览几种常见的类型。

![](img/8bc7851023f14ec9d09edcfe41374718_37.png)

![](img/8bc7851023f14ec9d09edcfe41374718_39.png)

以下是几种其他3D图形的绘制方法：

![](img/8bc7851023f14ec9d09edcfe41374718_41.png)

*   **线框图 (Wireframe Plot)**：用网格线描绘曲面形状。
    ```python
    fig = plt.figure()
    ax = fig.add_subplot(111, projection='3d')
    X, Y, Z = ax.get_test_data(0.05)  # 获取测试数据
    ax.plot_wireframe(X, Y, Z)
    plt.show()
    ```

![](img/8bc7851023f14ec9d09edcfe41374718_43.png)

*   **曲面图 (Surface Plot)**：用彩色曲面展示三维数据。
    ```python
    fig = plt.figure()
    ax = fig.add_subplot(111, projection='3d')
    X, Y, Z = ax.get_test_data(0.05)
    ax.plot_surface(X, Y, Z, cmap='viridis')  # 使用viridis配色
    plt.show()
    ```

![](img/8bc7851023f14ec9d09edcfe41374718_45.png)

*   **等高线图 (Contour Plot)**：在三维空间中显示等高线。
    ```python
    fig = plt.figure()
    ax = fig.add_subplot(111, projection='3d')
    X, Y, Z = ax.get_test_data(0.05)
    ax.contourf(X, Y, Z, 100)  # 绘制填充等高线
    plt.show()
    ```

![](img/8bc7851023f14ec9d09edcfe41374718_47.png)

---

![](img/8bc7851023f14ec9d09edcfe41374718_49.png)

## 总结

![](img/8bc7851023f14ec9d09edcfe41374718_51.png)

![](img/8bc7851023f14ec9d09edcfe41374718_52.png)

本节课中我们一起学习了Matplotlib的3D绘图功能。我们从二维散点图出发，通过添加新的数据维度，将其成功转换为三维散点图。随后，我们还简要介绍了线框图、曲面图和等高线图等其他3D图形的绘制方法。掌握这些技能，可以帮助你在量化分析和数据探索中，更立体、更全面地理解和展示复杂的数据关系。