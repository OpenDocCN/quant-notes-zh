# 量化交易：Python入门之数据分析：4-4. Python数据分析：Seaborn 📊

在本节课中，我们将学习如何使用Seaborn库来美化和增强由Pandas或Matplotlib生成的图表。Seaborn基于Matplotlib，提供了更高级的接口和更美观的默认样式，特别适合用于统计数据的可视化。

---

## 导入必要的库

首先，我们需要导入本节课将要用到的所有Python包。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

![](img/39b2344298b072a9f767fedf534fab0e_1.png)

---

![](img/39b2344298b072a9f767fedf534fab0e_3.png)

## 创建示例数据

为了演示Seaborn的功能，我们首先创建两组示例数据。

![](img/39b2344298b072a9f767fedf534fab0e_5.png)

```python
# 生成两个Pandas Series作为示例数据
v1 = pd.Series(np.random.normal(0, 10, 1000), name='V1')
v2 = pd.Series(np.random.normal(60, 15, 1000), name='V2')
```

![](img/39b2344298b072a9f767fedf534fab0e_7.png)

---

![](img/39b2344298b072a9f767fedf534fab0e_9.png)

## 基础直方图对比

在介绍Seaborn之前，我们先使用Matplotlib绘制一个基础的双变量直方图，以便后续对比。

![](img/39b2344298b072a9f767fedf534fab0e_11.png)

```python
# 使用Matplotlib绘制直方图
plt.figure(figsize=(10, 6))
plt.hist(v1, alpha=0.7, bins=np.arange(-50, 150, 5), label='V1')
plt.hist(v2, alpha=0.7, bins=np.arange(-50, 150, 5), label='V2')
plt.legend()
plt.show()
```

这个图表展示了`V1`和`V2`两个数据系列的分布，但样式较为基础。

![](img/39b2344298b072a9f767fedf534fab0e_13.png)

---

## 使用Seaborn绘制联合分布图

上一节我们介绍了基础直方图，本节中我们来看看Seaborn如何提升图表的视觉效果。Seaborn的`jointplot`函数可以同时展示两个变量的散点图和各自的分布直方图。

![](img/39b2344298b072a9f767fedf534fab0e_15.png)

![](img/39b2344298b072a9f767fedf534fab0e_16.png)

以下是使用Seaborn绘制基础联合分布图的方法：

![](img/39b2344298b072a9f767fedf534fab0e_17.png)

```python
# 使用Seaborn绘制联合分布图
sns.jointplot(x=v1, y=v2, alpha=0.5)
plt.show()
```

相比于Matplotlib的图表，Seaborn生成的图表在样式上更加美观和专业。

![](img/39b2344298b072a9f767fedf534fab0e_19.png)

---

![](img/39b2344298b072a9f767fedf534fab0e_20.png)

## 调整坐标轴比例

有时，两个变量的坐标轴单位不同，可能导致图形失真。我们可以通过设置`ratio`参数来调整坐标轴的比例，使它们具有相同的单位尺度。

![](img/39b2344298b072a9f767fedf534fab0e_22.png)

![](img/39b2344298b072a9f767fedf534fab0e_23.png)

```python
# 绘制比例协调的联合分布图
g = sns.jointplot(x=v1, y=v2)
g.ax_joint.set_aspect('equal')
plt.show()
```

![](img/39b2344298b072a9f767fedf534fab0e_25.png)

通过这种方法，我们可以得到一个在横纵坐标尺度上更准确的图形。

---

![](img/39b2344298b072a9f767fedf534fab0e_27.png)

## 定制化联合分布图

Seaborn允许我们对图表进行高度定制。例如，我们可以更改图表样式、将直方图替换为核密度估计图。

![](img/39b2344298b072a9f767fedf534fab0e_29.png)

以下是定制化图表的步骤：

![](img/39b2344298b072a9f767fedf534fab0e_31.png)

```python
# 设置Seaborn的图表样式
sns.set_style("whitegrid")

![](img/39b2344298b072a9f767fedf534fab0e_33.png)

# 绘制核密度估计类型的联合分布图
sns.jointplot(x=v1, y=v2, kind='kde', space=0)
plt.show()
```

通过将`kind`参数设置为`'kde'`，我们得到了一个用等高线表示数据密度的平滑图表。

---

![](img/39b2344298b072a9f767fedf534fab0e_35.png)

## 绘制矩阵散点图

![](img/39b2344298b072a9f767fedf534fab0e_37.png)

除了双变量分析，Seaborn的`pairplot`函数非常适合用于探索数据集中多个变量之间的关系。它能生成一个矩阵，显示所有数值型变量两两之间的散点图和对角线上的分布图。

![](img/39b2344298b072a9f767fedf534fab0e_39.png)

我们将使用经典的鸢尾花数据集进行演示。

![](img/39b2344298b072a9f767fedf534fab0e_41.png)

```python
# 加载鸢尾花数据集
iris_df = sns.load_dataset('iris')

![](img/39b2344298b072a9f767fedf534fab0e_42.png)

# 绘制矩阵散点图，并按‘species’列着色
sns.pairplot(iris_df, hue='species', diag_kind='kde', height=2)
plt.show()
```

在这个图表中，不同颜色的点代表不同种类的鸢尾花（Setosa, Versicolor, Virginica）。`hue`参数根据‘species’列对数据进行分组和着色，使得类别间的差异一目了然。

![](img/39b2344298b072a9f767fedf534fab0e_44.png)

---

## 绘制分类数据图

最后，我们来看看Seaborn如何处理分类数据。`swarmplot`（蜂群图）和`violinplot`（小提琴图）是两种有效的可视化工具。

![](img/39b2344298b072a9f767fedf534fab0e_46.png)

以下是创建包含两个子图的示例：

```python
# 设置图形大小
plt.figure(figsize=(12, 6))

# 第一个子图：蜂群图
plt.subplot(1, 2, 1)
sns.swarmplot(x='species', y='petal_length', data=iris_df)

# 第二个子图：小提琴图
plt.subplot(1, 2, 2)
sns.violinplot(x='species', y='petal_length', data=iris_df)

![](img/39b2344298b072a9f767fedf534fab0e_48.png)

# 自动调整子图布局
plt.tight_layout()
plt.show()
```
*   **蜂群图**：将每个数据点绘制出来，并避免重叠，可以清晰看到数据分布和密度。
*   **小提琴图**：结合了箱形图和核密度估计，展示了数据在不同类别中的分布形状。

![](img/39b2344298b072a9f767fedf534fab0e_49.png)

---

![](img/39b2344298b072a9f767fedf534fab0e_51.png)

## 总结

![](img/39b2344298b072a9f767fedf534fab0e_52.png)

本节课中我们一起学习了Seaborn库在数据可视化中的应用。我们从基础直方图对比开始，逐步探索了Seaborn的联合分布图、矩阵散点图以及分类数据图（蜂群图和小提琴图）。Seaborn通过其高级接口和精美的默认样式，能够帮助我们快速生成更具洞察力和吸引力的统计图形，是数据分析中不可或缺的工具。