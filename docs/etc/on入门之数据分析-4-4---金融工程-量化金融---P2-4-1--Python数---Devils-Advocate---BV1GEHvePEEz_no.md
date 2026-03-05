# 量化交易：Python入门之数据分析【4/4】：4-1. Python可视化 📊

在本节课中，我们将学习如何使用Python进行数据可视化。我们将重点介绍两个核心库：`matplotlib`和`seaborn`，并通过绘制散点图和折线图来掌握基本的绘图方法。

![](img/8e46ff8619d12efccfdb51f4016cc293_1.png)

## 环境准备与库导入

![](img/8e46ff8619d12efccfdb51f4016cc293_3.png)

首先，确保你已经安装了必要的Python库。如果尚未安装，请在终端中使用以下命令进行安装：

![](img/8e46ff8619d12efccfdb51f4016cc293_5.png)

```bash
pip install matplotlib
pip install seaborn
pip install scipy
```

![](img/8e46ff8619d12efccfdb51f4016cc293_7.png)

安装完成后，我们可以在Python脚本或Jupyter Notebook中导入这些库，同时导入常用的`numpy`库来生成示例数据。

![](img/8e46ff8619d12efccfdb51f4016cc293_9.png)

```python
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
```

![](img/8e46ff8619d12efccfdb51f4016cc293_10.png)

## 绘制散点图

![](img/8e46ff8619d12efccfdb51f4016cc293_12.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_13.png)

上一节我们介绍了库的导入，本节中我们来看看如何绘制一个基础的散点图。散点图常用于展示两个变量之间的关系。

首先，我们需要生成一些示例数据。我们将使用`numpy`创建两个简单的数组。

![](img/8e46ff8619d12efccfdb51f4016cc293_15.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_17.png)

```python
# 生成X轴数据
X = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])
# 生成Y轴数据，这里让Y等于X，形成一条直线
Y = X
```

![](img/8e46ff8619d12efccfdb51f4016cc293_19.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_20.png)

现在，我们可以使用`matplotlib`来绘制散点图。以下是绘制的基本步骤：

![](img/8e46ff8619d12efccfdb51f4016cc293_22.png)

1.  初始化一个图形。
2.  使用`scatter`函数绘制散点。
3.  显示图形。

```python
plt.figure()  # 初始化图形
plt.scatter(X, Y)  # 绘制散点图
plt.show()  # 显示图形
```

![](img/8e46ff8619d12efccfdb51f4016cc293_24.png)

执行上述代码，你将看到一个由点(1,1), (2,2), ..., (9,9)构成的散点图。

![](img/8e46ff8619d12efccfdb51f4016cc293_25.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_26.png)

### 自定义散点图

我们可以通过添加参数来自定义散点图的外观，例如点的颜色和大小。

![](img/8e46ff8619d12efccfdb51f4016cc293_28.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_29.png)

以下是自定义散点图的步骤，我们将创建一组点，其中大部分为绿色，最后一个点为红色。

```python
# 创建颜色列表，前8个点为绿色，最后一个点为红色
colors = ['green'] * (len(X) - 1) + ['red']

![](img/8e46ff8619d12efccfdb51f4016cc293_31.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_33.png)

plt.figure()
# 绘制散点图，s参数控制点的大小，c参数控制颜色
plt.scatter(X, Y, s=100, c=colors)
plt.show()
```

![](img/8e46ff8619d12efccfdb51f4016cc293_35.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_37.png)

### 组合多个散点图与添加标签

![](img/8e46ff8619d12efccfdb51f4016cc293_38.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_40.png)

我们可以将多组数据绘制在同一张图上，并为图表添加标题、坐标轴标签和图例，使其更具可读性。

![](img/8e46ff8619d12efccfdb51f4016cc293_42.png)

以下是如何将数据分为“高树”和“矮树”两组，并分别用红色和蓝色绘制，最后添加完整图表元素。

![](img/8e46ff8619d12efccfdb51f4016cc293_44.png)

```python
plt.figure()

![](img/8e46ff8619d12efccfdb51f4016cc293_46.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_47.png)

# 绘制第一组数据（前两个点，代表“高树”）
plt.scatter(X[:2], Y[:2], s=100, c='red', label='Tall Trees')
# 绘制第二组数据（剩余的点，代表“矮树”）
plt.scatter(X[2:], Y[2:], s=100, c='blue', label='Short Trees')

![](img/8e46ff8619d12efccfdb51f4016cc293_49.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_50.png)

# 添加图表标签和标题
plt.xlabel('Number of Trees')
plt.ylabel('Height of the Trees')
plt.title('Trees in My Backyard')

# 添加图例，loc参数控制图例位置
plt.legend(loc=4)

plt.show()
```

![](img/8e46ff8619d12efccfdb51f4016cc293_52.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_54.png)

## 绘制折线图

![](img/8e46ff8619d12efccfdb51f4016cc293_56.png)

在掌握了散点图之后，本节我们来看看另一种常见的图表——折线图。折线图非常适合展示数据随时间或其他连续变量的变化趋势。

![](img/8e46ff8619d12efccfdb51f4016cc293_58.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_60.png)

首先，我们生成两组示例数据：一组线性数据和一组指数增长数据。

![](img/8e46ff8619d12efccfdb51f4016cc293_62.png)

```python
# 生成X轴数据
linear_data = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])
# 生成指数数据：Y = 2^X
exponential_data = 2 ** linear_data
```

![](img/8e46ff8619d12efccfdb51f4016cc293_64.png)

现在，我们来绘制这两组数据的折线图。`plot`函数用于绘制折线，`'o'`参数表示在数据点处用圆圈标记。

![](img/8e46ff8619d12efccfdb51f4016cc293_66.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_68.png)

```python
plt.figure()

![](img/8e46ff8619d12efccfdb51f4016cc293_70.png)

# 绘制线性数据，用圆圈标记
plt.plot(linear_data, exponential_data, 'o')
# 绘制指数数据，同样用圆圈标记
plt.plot(linear_data, linear_data, 'o')

![](img/8e46ff8619d12efccfdb51f4016cc293_72.png)

# 添加一条简单的红色线段
plt.plot([2, 2, 4, 4, 5, 5], [2, 5, 5, 2, 2, 5], 'r')

![](img/8e46ff8619d12efccfdb51f4016cc293_74.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_76.png)

# 添加图表标签、标题和图例
plt.xlabel('X data')
plt.ylabel('Y data')
plt.title('My Line Graph')
plt.legend(['Baseline', 'Exponential', 'Random Line'])

plt.show()  # 显示最终图形
```

![](img/8e46ff8619d12efccfdb51f4016cc293_78.png)

## 总结

![](img/8e46ff8619d12efccfdb51f4016cc293_80.png)

![](img/8e46ff8619d12efccfdb51f4016cc293_81.png)

本节课中我们一起学习了Python数据可视化的基础。我们首先介绍了`matplotlib`和`seaborn`库的安装与导入。接着，详细讲解了如何绘制和自定义**散点图**，包括设置颜色、大小以及组合多组数据并添加完整的图表标签。然后，我们学习了如何绘制**折线图**来展示数据的变化趋势。这些基础图表是金融数据分析中展示关系、分布和趋势的重要工具。在后续课程中，我们将继续探索更多高级的图表类型，如箱形图和提琴图。