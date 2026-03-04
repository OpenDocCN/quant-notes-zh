# 机器学习：P146：K-Means聚类提取特征与图片压缩 🖼️

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_1.png)

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_3.png)

在本节课中，我们将学习如何使用K-Means聚类算法进行特征提取，并具体应用于图片压缩。我们将看到如何将一张彩色图片的丰富颜色信息，通过聚类简化为少数几种主要颜色，从而实现图片的压缩和简化。

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_5.png)

## 概述

K-Means聚类算法的作用之一是特征提取。我们将通过一个具体的例子——图片压缩，来演示这一过程。图片由成千上万个像素点组成，每个像素点都有其颜色值。通过K-Means算法，我们可以从这些颜色中提取出最具代表性的几种颜色，并用这些颜色来重新构建图片，从而达到压缩和简化视觉效果的目的。

---

## 加载与观察图片数据

首先，我们需要加载一张图片并观察其数据结构。我们将使用一张名为“11-bird”的图片。

```python
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from sklearn.cluster import KMeans

# 加载图片
image = plt.imread('11-bird.png')
plt.imshow(image)
plt.show()
```

执行上述代码后，我们可以看到一张鸟的图片。接下来，我们查看这张图片的数据结构。

```python
print(image)
print(image.shape)
```

输出结果将显示图片数据的形状为 `(128, 128, 3)`。这表示图片的尺寸是128像素宽、128像素高，并且每个像素由红、绿、蓝三个通道的颜色值组成。

---

## 数据预处理与K-Means聚类

上一节我们介绍了图片的数据结构，本节中我们来看看如何为K-Means算法准备数据并进行聚类。

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_7.png)

K-Means算法要求输入数据是二维的，其中行代表样本，列代表特征。然而，我们的图片数据是三维的 `(128, 128, 3)`。因此，我们需要将其重塑为二维数组。

```python
# 重塑数据：将每个像素点视为一个样本，其RGB值作为特征
X = image.reshape(-1, 3)
print(X.shape)  # 输出: (16384, 3)
```

现在，`X` 的形状是 `(16384, 3)`，表示我们有16384个样本（像素点），每个样本有3个特征（R, G, B值）。

接下来，我们使用K-Means算法进行聚类。假设我们希望将图片的颜色压缩为8种主要颜色。

```python
# 初始化K-Means模型，设置聚类数为8
kmeans = KMeans(n_clusters=8)
kmeans.fit(X)
```

训练完成后，我们可以获取聚类中心，这些中心点就代表了我们从图片中提取出的8种主要颜色。

```python
# 获取聚类中心，即主要的8种颜色
main_colors = kmeans.cluster_centers_
print(main_colors)
```

---

## 使用聚类结果重建图片

在提取了主要颜色之后，我们需要根据这些颜色来重建一张新的图片。这需要两个步骤：首先，确定原图中每个像素点属于哪个颜色类别；然后，用对应类别的中心颜色替换原像素颜色。

以下是具体操作步骤：

1.  **预测每个像素点的类别**：使用训练好的K-Means模型对原始数据 `X` 进行预测，得到每个像素点所属的类别标签（0到7）。
    ```python
    y_pred = kmeans.predict(X)
    print(y_pred)
    ```
    输出 `y_pred` 是一个长度为16384的数组，其中的每个数字（0-7）对应原图中一个像素点被分配到的颜色类别。

2.  **根据类别标签重建图片**：我们使用 `y_pred` 作为索引，从 `main_colors` 数组中取出对应的颜色值，生成新的图片数据。
    ```python
    # 使用花式索引，根据预测的类别获取对应的主要颜色
    image_compressed = main_colors[y_pred]
    # 将数据重塑回图片的原始形状 (128, 128, 3)
    image_compressed = image_compressed.reshape(128, 128, 3)
    ```

---

## 对比原始图片与压缩后图片

最后，我们将原始图片和经过K-Means压缩后的图片并排显示，以直观地对比效果。

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_9.png)

```python
# 设置画布大小
plt.figure(figsize=(8, 4))

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_11.png)

# 显示原始图片
plt.subplot(1, 2, 1)
plt.imshow(image)
plt.title('Original Image')

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_13.png)

# 显示压缩后的图片
plt.subplot(1, 2, 2)
plt.imshow(image_compressed)
plt.title('Compressed Image (8 Colors)')

plt.show()
```

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_15.png)

执行代码后，你会看到左侧是高清的原图，颜色丰富；右侧是压缩后的图片，只使用了8种颜色，因此看起来会有些模糊，类似于素描或像素画的效果，但图片的主体形状和结构依然清晰可辨。

---

## 核心概念总结

本节课中我们一起学习了K-Means聚类在特征提取和图片压缩中的应用。让我们回顾一下关键步骤和概念：

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_17.png)

*   **数据重塑**：将三维图片数据 `(height, width, channels)` 重塑为二维数组 `(n_samples, n_features)`，以适应K-Means的输入要求。公式表示为：
    `X_reshaped = image.reshape(-1, 3)`
*   **聚类中心**：通过 `kmeans.cluster_centers_` 属性获得，代表了算法提取出的主要特征（在本例中是主要颜色）。
*   **预测与重建**：使用 `kmeans.predict(X)` 为每个样本分配类别标签，然后根据标签用聚类中心颜色替换原值，最后将数据重塑回图片格式。

![](img/d9e4f8aa6ec1379d27e28b10ab87817e_19.png)

这个过程清晰地展示了K-Means如何从海量数据（数万种颜色）中提取核心特征（几种主要颜色），并利用这些特征重新表达数据，实现了数据的简化和压缩。这种方法不仅限于图片，也可以应用于其他需要提取代表性特征的数据分析场景。