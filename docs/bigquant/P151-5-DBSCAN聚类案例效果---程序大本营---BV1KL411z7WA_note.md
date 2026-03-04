# 机器学习：P151：DBSCAN聚类案例效果 📊

![](img/d553d33c9f54dc9a1f4b145269759aeb_0.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_2.png)

在本节课中，我们将学习如何使用DBSCAN算法对数据进行聚类，并观察其效果。我们将通过调整参数来理解算法行为，并使用轮廓系数评估聚类质量。

---

## DBSCAN聚类效果展示

![](img/d553d33c9f54dc9a1f4b145269759aeb_4.png)

上一节我们介绍了DBSCAN的基本概念，本节中我们来看看它的实际聚类效果。

![](img/d553d33c9f54dc9a1f4b145269759aeb_6.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_8.png)

首先，我们需要创建并配置一个DBSCAN模型。以下是核心代码：

![](img/d553d33c9f54dc9a1f4b145269759aeb_10.png)

```python
from sklearn.cluster import DBSCAN

![](img/d553d33c9f54dc9a1f4b145269759aeb_12.png)

# 创建DBSCAN模型，设置半径(eps)和最小样本数(min_samples)
dbscan = DBSCAN(eps=0.5, min_samples=3)
# 对数据X进行拟合
dbscan.fit(X)
# 获取每个样本的聚类标签
y_ = dbscan.labels_
```

![](img/d553d33c9f54dc9a1f4b145269759aeb_14.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_16.png)

接下来，我们可以将聚类结果可视化。使用Matplotlib绘制散点图，并根据聚类标签`y_`为不同类别的点着色。

![](img/d553d33c9f54dc9a1f4b145269759aeb_18.png)

```python
import matplotlib.pyplot as plt

![](img/d553d33c9f54dc9a1f4b145269759aeb_20.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_22.png)

plt.figure(figsize=(5, 5))
plt.scatter(X[:, 0], X[:, 1], c=y_)
plt.show()
```

![](img/d553d33c9f54dc9a1f4b145269759aeb_24.png)

执行代码后，可以看到数据被划分成了几个不同颜色的簇。例如，可能看到黄色、蓝绿色、蓝色和紫色的点。

![](img/d553d33c9f54dc9a1f4b145269759aeb_26.png)

---

![](img/d553d33c9f54dc9a1f4b145269759aeb_28.png)

## 参数调整对结果的影响

![](img/d553d33c9f54dc9a1f4b145269759aeb_30.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_32.png)

DBSCAN的聚类效果高度依赖于参数设置，特别是半径`eps`。

![](img/d553d33c9f54dc9a1f4b145269759aeb_34.png)

以下是调整`eps`参数时可能观察到的现象：

![](img/d553d33c9f54dc9a1f4b145269759aeb_36.png)

*   **增大`eps`（例如设为2）**：聚类半径变大，算法可能将原本分开的簇合并，导致最终只识别出一个大类。
*   **减小`eps`（例如设为0.1）**：聚类半径变小，算法对簇的定义更严格。原本属于一个簇的边缘点可能因为其邻域内样本数不足，而被识别为噪声点（离群点），导致图中出现更多散落的点（通常用紫色表示）。

![](img/d553d33c9f54dc9a1f4b145269759aeb_38.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_40.png)

---

## 识别与过滤噪声点

![](img/d553d33c9f54dc9a1f4b145269759aeb_42.png)

在DBSCAN中，无法被归入任何簇的样本点会被标记为噪声，其标签值为 **`-1`**。

![](img/d553d33c9f54dc9a1f4b145269759aeb_44.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_46.png)

我们可以通过检查`y_`中的唯一值来确认噪声点的存在：

![](img/d553d33c9f54dc9a1f4b145269759aeb_48.png)

```python
import numpy as np
print(np.unique(y_))
# 输出可能包含：[-1  0  1  2]
```

![](img/d553d33c9f54dc9a1f4b145269759aeb_50.png)

这里的`-1`就代表噪声点（离群点）。我们可以根据这个条件过滤掉噪声数据，只关注核心的簇。

![](img/d553d33c9f54dc9a1f4b145269759aeb_52.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_54.png)

以下是过滤噪声点并重新绘制核心簇的代码：

![](img/d553d33c9f54dc9a1f4b145269759aeb_56.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_58.png)

```python
# 创建条件，选择所有标签不等于-1的样本
cond = y_ != -1

![](img/d553d33c9f54dc9a1f4b145269759aeb_60.png)

# 使用条件过滤数据X和标签y_
plt.figure(figsize=(5, 5))
plt.scatter(X[cond, 0], X[cond, 1], c=y_[cond])
plt.show()
```

![](img/d553d33c9f54dc9a1f4b145269759aeb_62.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_64.png)

执行后，图像中将不再显示那些紫色的噪声点，只留下清晰的核心簇结构。

![](img/d553d33c9f54dc9a1f4b145269759aeb_66.png)

---

![](img/d553d33c9f54dc9a1f4b145269759aeb_68.png)

## 使用轮廓系数评估聚类效果

![](img/d553d33c9f54dc9a1f4b145269759aeb_70.png)

与K-Means等算法一样，我们也可以使用轮廓系数来量化评估DBSCAN的聚类效果。

![](img/d553d33c9f54dc9a1f4b145269759aeb_72.png)

首先导入轮廓系数计算模块：

![](img/d553d33c9f54dc9a1f4b145269759aeb_74.png)

```python
from sklearn.metrics import silhouette_score
```

![](img/d553d33c9f54dc9a1f4b145269759aeb_76.png)

然后，对DBSCAN的聚类结果进行计算。**注意**：轮廓系数计算通常排除噪声点（标签为-1），因为噪声点不属于任何簇。

```python
# 计算轮廓系数（通常过滤掉噪声点）
score = silhouette_score(X[cond], y_[cond])
print(f"轮廓系数为：{score:.2f}")
```

![](img/d553d33c9f54dc9a1f4b145269759aeb_78.png)

![](img/d553d33c9f54dc9a1f4b145269759aeb_80.png)

得到的分数（例如0.21）可以帮助我们判断当前参数下聚类的紧密度和分离度。我们可以通过调整`eps`和`min_samples`参数，寻找能使轮廓系数更高的配置。

![](img/d553d33c9f54dc9a1f4b145269759aeb_82.png)

---

![](img/d553d33c9f54dc9a1f4b145269759aeb_84.png)

本节课中我们一起学习了DBSCAN算法的实践应用。我们掌握了如何创建模型、调整参数观察聚类变化、识别并过滤噪声点，以及使用轮廓系数评估聚类质量。DBSCAN的优势在于能发现任意形状的簇并自动识别噪声，但其效果对参数设置较为敏感，需要根据数据特点进行调试。