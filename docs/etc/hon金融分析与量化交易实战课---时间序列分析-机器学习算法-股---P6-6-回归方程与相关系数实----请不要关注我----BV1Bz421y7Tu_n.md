# 人生苦短，我学量化！：P6：回归方程与相关系数实例

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_0.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_2.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_4.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数。我们将通过一个金融时间序列分析的实例，演示如何利用`numpy`和`pandas`库进行数据处理、模型构建和结果可视化。课程内容将涵盖线性回归模型的构建、数据预处理、回归线的绘制，以及如何计算并分析随时间变化的相关系数。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_6.png)

## 构建回归方程

上一节我们介绍了回归分析的基本概念，本节中我们来看看如何用代码实际构建一个回归方程。构建回归方程的方法很多，许多工具包都能完成这个任务。这里我们选择使用`numpy`库中的`polyfit`函数来计算回归系数。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_8.png)

`polyfit`函数可以帮助我们计算一个回归模型（本质上是计算回归系数）。使用该函数时，需要传递几个关键参数。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_10.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_12.png)

以下是使用`polyfit`函数的基本步骤：

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_14.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_16.png)

1.  指定两个变量：你需要告诉函数是哪两个指标之间存在关系。例如，变量`x`和变量`y`。
2.  指定多项式阶数：这决定了回归方程的形式。例如，阶数`deg=1`表示我们构建的是一元线性回归方程 **`y = kx + b`**，其中包含`x`的一次项和零次项（常数项）。如果指定`deg=2`，则方程会包含`x`的二次项、一次项和零次项，可以拟合曲线。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_18.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_20.png)

在我们的任务中，两个指标之间近似为线性关系，因此指定阶数`deg=1`即可。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_22.png)

```python
import numpy as np

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_24.png)

# 假设 df[‘x’] 和 df[‘y’] 是我们的两个指标数据
# 构建回归方程， deg=1 表示一元线性回归
coefficients = np.polyfit(df[‘x’], df[‘y’], deg=1)
# coefficients 返回的数组 [k, b] 即回归系数
k, b = coefficients
```

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_26.png)

在运行上述代码前，必须确保数据中没有空值（NaN），否则计算过程可能出错。因此，构建回归方程前通常需要进行数据清洗。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_28.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_30.png)

```python
# 数据预处理：删除包含空值的行
df.dropna(inplace=True)
```

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_32.png)

## 绘制回归线

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_34.png)

现在我们已经求解出回归方程 **`y = kx + b`** 的系数`k`和`b`。接下来，我们可以在散点图上将这条回归线画出来，以便直观地观察两个变量之间的关系。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_36.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_38.png)

绘制过程分为两步：首先绘制原始数据的散点图，然后根据回归方程计算对应的`y`值并绘制直线。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_40.png)

以下是绘制回归线的代码示例：

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_42.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_44.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_46.png)

```python
import matplotlib.pyplot as plt

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_48.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_50.png)

# 1. 绘制原始数据的散点图
plt.scatter(x=df[‘x’], y=df[‘y’], s=16) # s 参数控制点的大小

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_52.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_54.png)

# 2. 绘制回归线
# 生成一组x值（通常使用原始数据的x范围）
x_line = df[‘x’]
# 根据回归方程计算对应的y值
y_line = np.polyval(coefficients, x_line) # 使用polyval函数计算
# 绘制红色的回归线
plt.plot(x_line, y_line, color=‘red’)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_56.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_58.png)

plt.show()
```

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_60.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_62.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_64.png)

通过这张图，两个指标之间的线性关系就一目了然了。需要注意的是，在金融分析中，我们常常对指标的增长率（变化率）进行回归分析，而不是原始价格本身，这一点在处理数据时需要根据目标进行合适的预处理。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_66.png)

## 计算相关系数

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_68.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_70.png)

除了回归方程，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。在`pandas`中，计算相关系数非常简单。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_72.png)

我们可以直接调用DataFrame的`.corr()`方法来计算所有列两两之间的相关系数矩阵。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_74.png)

```python
# 计算相关系数矩阵
correlation_matrix = df[[‘指标A‘， ’指标B‘]].corr()
print(correlation_matrix)
```

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_76.png)

在这个矩阵中，对角线上的值都是1（表示自己与自己的完全相关），而非对角线上的值就是两个不同指标之间的相关系数。矩阵是对称的，即指标A与指标B的相关系数等于指标B与指标A的相关系数。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_78.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_80.png)

## 附加任务：分析相关系数随时间的变化

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_82.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_84.png)

为了更深入地分析，我们可以提出一个更有挑战性的问题：随着时间推移，两个指标之间的相关系数是如何变化的？

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_86.png)

这需要我们引入“滑动窗口”的概念。我们不再计算整个时间段的单一相关系数，而是计算在每一个时间点附近、一个特定时间窗口内的相关系数。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_88.png)

以下是实现思路和代码：

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_90.png)

1.  **定义窗口**：例如，定义一个包含250个交易日数据的窗口。
2.  **滑动计算**：让这个窗口在时间轴上滑动，每滑动一次，就计算窗口内两个指标的相关系数。
3.  **结果展示**：将计算出的每个窗口的相关系数按时间顺序连接起来，就得到了相关系数随时间变化的序列。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_92.png)

```python
# 设置窗口大小
window_size = 250

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_94.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_96.png)

# 计算指标A与指标B在滑动窗口下的相关系数序列
rolling_corr = df[‘指标A‘].rolling(window=window_size).corr(df[‘指标B‘])

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_98.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_100.png)

# 绘制相关系数随时间变化的曲线
rolling_corr.plot(figsize=(11, 6))
plt.title(‘滚动相关系数变化图‘)
plt.show()
```

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_102.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_104.png)

通过这张图，我们可以观察到两个指标间的关联强度在不同市场时期是增强、减弱还是保持稳定。这比单一的全局相关系数包含了更丰富的信息。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_106.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_108.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_110.png)

## 课程总结

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_112.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_114.png)

本节课中我们一起学习了金融时间序列分析中的两个核心工具：回归方程与相关系数。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_116.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_118.png)

我们首先使用`numpy.polyfit`函数构建了一元线性回归方程，并学会了在绘制散点图的基础上添加回归线进行可视化。接着，我们使用`pandas`快速计算了变量间的相关系数矩阵。最后，我们通过一个附加任务，探索了如何使用滑动窗口计算动态的、随时间变化的相关系数，从而获得更深入的市场洞察。

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_120.png)

![](img/af08cb77316eddb5f202ebe8cd8e8bb2_122.png)

建议大家在课后对照代码练习一遍，理解每一步的操作目的。可以尝试修改参数，例如改变回归方程的阶数、调整滑动窗口的大小，观察结果有何不同。通过动手实践，能够更好地掌握这些在量化分析中非常实用的技能。