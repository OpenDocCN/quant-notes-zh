# 机器学习：P139：2-Kmeans聚类亚洲国家队类别可视化

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_1.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_3.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_5.png)

在本节课中，我们将学习如何使用K-Means聚类算法对亚洲国家足球队的数据进行分析，并将聚类结果在三维空间中进行可视化展示。通过这个过程，我们可以直观地看到不同球队在历年赛事表现上的类别划分。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_7.png)

---

上一节我们介绍了如何使用K-Means算法对亚洲国家队进行聚类划分。本节中，我们来看看如何将聚类结果进行可视化，以便更直观地理解数据分布和类别划分。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_9.png)

首先，我们需要调整绘图尺寸并创建一个三维图形。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_11.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_13.png)

```python
plt.figure(figsize=(12, 9))
ax = plt.subplot(111, projection='3d')
```

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_15.png)

执行上述代码后，一个三维坐标系的图形就创建好了。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_17.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_19.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_21.png)

接下来，我们需要将数据点绘制到这个三维空间中。以下是绘制散点图的步骤。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_23.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_25.png)

我们使用`scatter`函数绘制散点图。数据来自变量`x`，它包含了各球队在2006年世界杯、2010年世界杯和2007年亚洲杯的表现数据。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_27.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_29.png)

```python
ax.scatter(x.iloc[:, 0], x.iloc[:, 1], x.iloc[:, 2], c=y_pred, s=100)
```
*   **`x.iloc[:, 0]`**: 代表2006年世界杯的数据（第一列）。
*   **`x.iloc[:, 1]`**: 代表2010年世界杯的数据（第二列）。
*   **`x.iloc[:, 2]`**: 代表2007年亚洲杯的数据（第三列）。
*   **`c=y_pred`**: 表示根据预测的类别`y_pred`（其值为0, 1, 2）为不同类别的点分配不同的颜色。
*   **`s=100`**: 设置散点的大小为100。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_31.png)

我们可以自定义颜色映射，让不同类别的颜色更符合我们的偏好。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_33.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_35.png)

```python
from matplotlib.colors import ListedColormap
colors = ListedColormap(['red', 'green', 'purple'])
ax.scatter(x.iloc[:, 0], x.iloc[:, 1], x.iloc[:, 2], c=y_pred, s=100, cmap=colors)
```

执行代码后，三维散点图就绘制完成了。图中不同颜色的点代表不同的类别，颜色的深浅由图形自动生成以体现远近感。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_37.png)

为了使图表信息更完整，我们需要为三个坐标轴添加标签。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_39.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_41.png)

```python
ax.set_xlabel('2006年世界杯', fontsize=18)
ax.set_ylabel('2010年世界杯', fontsize=18)
ax.set_zlabel('2007年亚洲杯', fontsize=18)
```

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_43.png)

由于标签中包含中文，默认设置可能无法正常显示。我们需要设置中文字体。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_45.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_47.png)

```python
plt.rcParams['font.family'] = ['STKaiti'] # 设置为楷体
```

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_49.png)

如果你不确定自己电脑上有哪些可用字体，可以通过以下代码查找所有已安装的字体，并从中选择合适的中文字体（如仿宋、宋体、楷体、黑体等）。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_51.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_53.png)

```python
from matplotlib import font_manager
fm = font_manager.FontManager()
font_names = [f.name for f in fm.ttflist]
print(font_names)
```

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_55.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_57.png)

完成所有设置后，执行代码即可看到最终的可视化结果。在图中：
*   **红色点**可能对应日本、韩国等一流强队。
*   **紫色点**可能对应中国等队伍。
*   **绿色点**可能对应朝鲜、沙特等亚洲强队。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_59.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_61.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_63.png)

---

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_65.png)

本节课中我们一起学习了如何将K-Means聚类的结果进行三维可视化。我们创建了三维坐标系，绘制了代表不同球队的散点，并根据聚类结果为它们着色，最后添加了坐标轴标签并解决了中文显示问题。通过这个直观的图表，我们可以清晰地看到算法根据历史赛事表现将亚洲球队分成的不同类别。

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_67.png)

![](img/a87df3e5b1de4de3437a6b1bbc780f8e_69.png)

与人工划分相比，机器学习算法在处理多维、大量数据时效率更高，优势明显。这体现了计算机在复杂数据分析中的强大能力。随着社会进步，这类技术将发挥越来越重要的作用。