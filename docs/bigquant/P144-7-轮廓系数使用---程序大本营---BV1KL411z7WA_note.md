# 机器学习：P144：轮廓系数使用教程 📊

![](img/2077da5de348682fa498e4e0a5082629_1.png)

![](img/2077da5de348682fa498e4e0a5082629_3.png)

![](img/2077da5de348682fa498e4e0a5082629_5.png)

在本节课中，我们将学习如何使用轮廓系数（Silhouette Score）来评估K-Means聚类算法的效果，并据此选择最佳的聚类数量（K值）。我们将通过代码演示，从数据生成、模型训练到结果评估的完整流程。

---

## 创建数据 📈

上一节我们介绍了轮廓系数的概念，本节中我们来看看如何在实际代码中应用它。首先，我们需要创建用于聚类的示例数据。

![](img/2077da5de348682fa498e4e0a5082629_7.png)

我们将使用 `sklearn.datasets` 中的 `make_blobs` 方法来生成一个包含三个类别的二维数据集，以便于可视化。

```python
import numpy as np
from sklearn import datasets
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
import matplotlib.pyplot as plt

# 设置中文字体
plt.rcParams[‘font.family‘] = [‘STKaiti‘]
plt.rcParams[‘font.size‘] = 20

# 生成数据
x, y = datasets.make_blobs(n_samples=100, centers=3, random_state=2048)

# 可视化原始数据
plt.scatter(x[:, 0], x[:, 1], c=y)
plt.title(‘原始数据分布‘)
plt.show()
```

![](img/2077da5de348682fa498e4e0a5082629_9.png)

执行以上代码，我们将看到生成的数据点被清晰地分为三类。

---

![](img/2077da5de348682fa498e4e0a5082629_11.png)

## 使用K-Means进行聚类 🔍

现在，我们有了数据，但计算机并不知道这些数据属于哪一类。接下来，我们将使用K-Means算法让计算机自动进行聚类。

这里存在一个问题：我们应该将数据分成几类（即K值取多少）？为了找到最佳K值，我们将尝试从2到7的不同K值。

以下是使用K-Means进行聚类的步骤：

1.  遍历K值（从2到7）。
2.  对每个K值，初始化K-Means模型并进行训练。
3.  使用训练好的模型对数据进行预测，得到每个样本的类别标签。

![](img/2077da5de348682fa498e4e0a5082629_13.png)

```python
# 存储不同K值下的轮廓系数
scores = []

![](img/2077da5de348682fa498e4e0a5082629_15.png)

# 尝试不同的K值
for k in range(2, 8):
    # 声明算法
    kmeans = KMeans(n_clusters=k)
    # 训练模型
    kmeans.fit(x)
    # 预测类别
    y_pred = kmeans.predict(x)
    # 计算轮廓系数
    score = silhouette_score(x, y_pred)
    scores.append(score)
```

---

## 评估与选择最佳K值 🏆

上一节我们使用K-Means进行了聚类，本节中我们利用轮廓系数来评估每个K值下聚类的效果。

轮廓系数的取值范围在-1到1之间。**分数越高，表示聚类效果越好**，样本与自身簇的相似度高，与其他簇的差异大。

我们将计算出的轮廓系数进行可视化，并找出分数最高的K值。

```python
# 绘制轮廓系数随K值变化的曲线
k_values = range(2, 8)
plt.plot(k_values, scores, color=‘green‘)
plt.xlabel(‘K值‘)
plt.ylabel(‘轮廓系数‘)
plt.title(‘轮廓系数 vs K值‘)

# 找出并标记最高分数点
best_index = np.argmax(scores)
best_k = k_values[best_index]
best_score = scores[best_index]

plt.scatter(best_k, best_score, color=‘red‘, s=50)
plt.text(best_k, best_score, f‘最佳K={best_k}‘, fontsize=12)

plt.show()

print(f“轮廓系数最大的K值是：{best_k}“)
```

运行代码后，图表会显示轮廓系数在K=3时达到峰值。这与我们最初生成数据时设定的类别数（`centers=3`）完全一致。这说明，**当K值设置合理时，轮廓系数会给出较高的分数**，从而帮助我们确定最佳的聚类数量。

---

## 总结 📝

![](img/2077da5de348682fa498e4e0a5082629_17.png)

本节课中我们一起学习了轮廓系数的实际应用。我们首先创建了模拟数据，然后使用K-Means算法进行聚类，最后通过计算和比较不同K值下的轮廓系数，成功找到了数据的最佳聚类数量（K=3）。轮廓系数是一个简单而有效的内部评估指标，能够在不依赖真实标签的情况下，帮助我们判断聚类结果的好坏并选择合理的模型参数。