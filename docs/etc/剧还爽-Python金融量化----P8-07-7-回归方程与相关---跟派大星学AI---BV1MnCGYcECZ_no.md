# 量化交易全教程：P8：07-7-回归方程与相关系数实例 📈

![](img/87dd2d2c5c00140ae51cda2d08202261_1.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是量化分析中理解变量间关系的基础技能。我们将通过实例演示，从数据处理到结果可视化的完整流程。

![](img/87dd2d2c5c00140ae51cda2d08202261_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/87dd2d2c5c00140ae51cda2d08202261_7.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_9.png)

`polyfit`函数可以帮助我们计算一个回归模型的系数。具体来说，它需要传入几个参数：
1.  自变量 `x` 的数据。
2.  因变量 `y` 的数据。
3.  多项式的阶数 `deg`。

![](img/87dd2d2c5c00140ae51cda2d08202261_11.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_13.png)

例如，对于一元线性回归方程 **`y = kx + b`**，其中包含 `x` 的一次项和零次项（常数项）。如果我们指定阶数 `deg=1`，就表示只拟合 `x` 的一次项和零次项。如果指定 `deg=2`，则会拟合 `x` 的二次项、一次项和零次项，从而得到一条曲线。在本任务中，我们只研究线性关系，因此指定 `deg=1`。

![](img/87dd2d2c5c00140ae51cda2d08202261_15.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_17.png)

以下是构建回归方程的关键代码步骤：

![](img/87dd2d2c5c00140ae51cda2d08202261_19.png)

```python
import numpy as np

![](img/87dd2d2c5c00140ae51cda2d08202261_21.png)

# 假设 df[‘PX‘] 是自变量 x， df[‘VX‘] 是因变量 y
# 首先，需要处理数据中的空值
df.dropna(inplace=True)

![](img/87dd2d2c5c00140ae51cda2d08202261_23.png)

# 使用 polyfit 计算线性回归系数 (k, b)
coefficients = np.polyfit(df[‘PX‘], df[‘VX‘], deg=1)
k, b = coefficients
```

![](img/87dd2d2c5c00140ae51cda2d08202261_25.png)

在计算过程中，如果数据包含空值，`polyfit`函数可能会在求逆矩阵时报错。因此，在构建回归方程前，务必使用 `dropna()` 等方法清理数据。

![](img/87dd2d2c5c00140ae51cda2d08202261_27.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_29.png)

---

![](img/87dd2d2c5c00140ae51cda2d08202261_31.png)

## 可视化回归方程

![](img/87dd2d2c5c00140ae51cda2d08202261_33.png)

得到回归系数后，我们就可以将回归线可视化，与原始数据散点图进行对比。

![](img/87dd2d2c5c00140ae51cda2d08202261_35.png)

以下是绘制散点图和回归线的步骤：
1.  首先，使用 `matplotlib` 绘制原始数据的散点图。
2.  然后，根据求解出的回归方程 **`y = kx + b`**，计算出一系列对应的 `y` 预测值。
3.  最后，将这些点连接起来，绘制出回归直线。

![](img/87dd2d2c5c00140ae51cda2d08202261_37.png)

```python
import matplotlib.pyplot as plt

![](img/87dd2d2c5c00140ae51cda2d08202261_39.png)

# 绘制原始数据散点图
plt.figure(figsize=(16, 9))
plt.scatter(df[‘PX‘], df[‘VX‘], label=‘原始数据‘)

![](img/87dd2d2c5c00140ae51cda2d08202261_41.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_43.png)

# 计算回归线的y值
x_values = df[‘PX‘]
y_predicted = k * x_values + b  # 使用求得的k和b

![](img/87dd2d2c5c00140ae51cda2d08202261_45.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_47.png)

# 绘制回归线
plt.plot(x_values, y_predicted, color=‘red‘, label=‘回归线‘)

![](img/87dd2d2c5c00140ae51cda2d08202261_49.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_51.png)

plt.legend()
plt.show()
```

![](img/87dd2d2c5c00140ae51cda2d08202261_53.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_55.png)

通过可视化，我们可以直观地看到两个指标之间的关系以及回归线的拟合情况。需要注意的是，在分析前应确保数据经过了合适的预处理，例如本例中使用的可能是增长率数据而非原始价格。

![](img/87dd2d2c5c00140ae51cda2d08202261_57.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_59.png)

---

![](img/87dd2d2c5c00140ae51cda2d08202261_61.png)

## 计算相关系数

![](img/87dd2d2c5c00140ae51cda2d08202261_63.png)

除了回归方程，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/87dd2d2c5c00140ae51cda2d08202261_65.png)

以下是计算并展示相关系数矩阵的方法：

![](img/87dd2d2c5c00140ae51cda2d08202261_67.png)

```python
# 计算两个序列的相关系数矩阵
correlation_matrix = df[[‘PX‘, ‘VX‘]].corr()
print(correlation_matrix)
```

![](img/87dd2d2c5c00140ae51cda2d08202261_69.png)

该矩阵对角线上的值均为1（表示自身完全相关），而非对角线上的值则显示了两个指标间的相关系数，矩阵是对称的。

![](img/87dd2d2c5c00140ae51cda2d08202261_71.png)

---

![](img/87dd2d2c5c00140ae51cda2d08202261_73.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_75.png)

## 附加任务：随时间变化的相关系数

![](img/87dd2d2c5c00140ae51cda2d08202261_77.png)

为了更深入地分析，我们可以研究相关系数如何随时间变化。这需要通过滑动窗口来计算动态的相关系数。

![](img/87dd2d2c5c00140ae51cda2d08202261_79.png)

具体思路是：
1.  定义一个窗口大小（例如250个数据点）。
2.  从时间序列的起始点开始，滑动这个窗口。
3.  在每个窗口内，计算两个指标在该时间段内的相关系数。
4.  随着窗口在时间轴上滑动，我们就能得到一系列随时间变化的相关系数值。

以下是实现代码：

![](img/87dd2d2c5c00140ae51cda2d08202261_81.png)

```python
# 设置窗口大小
window_size = 250

![](img/87dd2d2c5c00140ae51cda2d08202261_83.png)

# 计算‘PX‘与‘VX‘在滑动窗口内的滚动相关系数
rolling_corr = df[‘PX‘].rolling(window=window_size).corr(df[‘VX‘])

![](img/87dd2d2c5c00140ae51cda2d08202261_85.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_87.png)

# 绘制滚动相关系数随时间的变化图
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(‘滚动相关系数变化图‘)
plt.show()
```

![](img/87dd2d2c5c00140ae51cda2d08202261_89.png)

这张图展示了两个指标间关系强度随时间演变的情况，比单一的静态相关系数包含了更多信息。

![](img/87dd2d2c5c00140ae51cda2d08202261_91.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_93.png)

---

![](img/87dd2d2c5c00140ae51cda2d08202261_95.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_97.png)

## 总结

![](img/87dd2d2c5c00140ae51cda2d08202261_99.png)

本节课中我们一起学习了金融量化分析中的两个核心工具：回归方程与相关系数。
*   我们使用NumPy的`polyfit`函数构建了线性回归方程，并学会了如何将其可视化。
*   我们使用Pandas的`.corr()`方法快速计算了指标间的静态相关系数。
*   最后，我们通过滑动窗口计算了动态的滚动相关系数，以观察关系随时间的变化趋势。

![](img/87dd2d2c5c00140ae51cda2d08202261_101.png)

![](img/87dd2d2c5c00140ae51cda2d08202261_103.png)

这些方法是理解数据间关系的基础，建议大家在理解代码逻辑后，尝试修改参数（如窗口大小、多项式阶数）进行练习，以加深理解。