# 机器学习与量化交易：P11：回归方程与相关系数实例 📈

![](img/6dd65e631f8d1be145fa9056de064b5a_1.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_3.png)

在本节课中，我们将学习如何使用Python工具包构建回归方程、计算相关系数，并探索如何分析金融时间序列数据中两个指标之间的动态关系。

![](img/6dd65e631f8d1be145fa9056de064b5a_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何量化两个指标之间的关系。构建回归方程的方法有很多，我们可以使用多种工具包来完成。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/6dd65e631f8d1be145fa9056de064b5a_7.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_9.png)

`polyfit`函数可以帮助我们计算一个回归模型的系数。它的核心是拟合一个多项式。例如，对于线性关系 `Y = KX + B`，我们拟合的是一次多项式。

![](img/6dd65e631f8d1be145fa9056de064b5a_11.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_13.png)

以下是使用`polyfit`函数的关键步骤和参数：

![](img/6dd65e631f8d1be145fa9056de064b5a_15.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_17.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_19.png)

1.  **传入数据**：需要传入两个指标的数据，例如`X`和`Y`。
2.  **指定阶数**：通过参数`deg`指定多项式的阶数。`deg=1`表示拟合一次多项式（即直线 `Y = KX + B`），包含X的一次项和常数项。`deg=2`则表示拟合二次多项式（曲线），包含X的二次项、一次项和常数项。
3.  **处理数据**：在计算前，需要确保数据中没有空值（NaN），否则可能导致计算错误。我们可以使用`dropna()`方法清理数据。

![](img/6dd65e631f8d1be145fa9056de064b5a_21.png)

核心代码公式如下：
```python
# 假设 df[‘X’] 和 df[‘Y’] 是我们的两个指标
# 清理数据
df.dropna(inplace=True)

![](img/6dd65e631f8d1be145fa9056de064b5a_23.png)

# 使用 polyfit 计算线性回归系数 (deg=1)
# 结果返回系数数组，对于 deg=1，结果为 [K, B]
coefficients = np.polyfit(df[‘X’], df[‘Y’], deg=1)
K, B = coefficients[0], coefficients[1]
```

得到回归系数K和B后，我们就得到了回归方程 `Y = K * X + B`。

![](img/6dd65e631f8d1be145fa9056de064b5a_25.png)

---

![](img/6dd65e631f8d1be145fa9056de064b5a_27.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_29.png)

## 可视化回归方程

![](img/6dd65e631f8d1be145fa9056de064b5a_31.png)

在得到回归方程后，我们可以将其可视化，与原始数据散点图叠加，直观地观察拟合效果。

![](img/6dd65e631f8d1be145fa9056de064b5a_33.png)

以下是绘制散点图和回归线的步骤：

![](img/6dd65e631f8d1be145fa9056de064b5a_35.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_37.png)

1.  首先，使用`scatter`函数绘制原始数据的散点图。
2.  然后，根据回归方程，计算出一系列用于绘制直线的点。我们可以利用之前求得的系数K和B，以及X的值来计算对应的Y值。
3.  最后，使用`plot`函数将这条回归线画在同一个图上。

核心代码如下：
```python
import matplotlib.pyplot as plt

![](img/6dd65e631f8d1be145fa9056de064b5a_39.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_41.png)

# 1. 绘制散点图
plt.scatter(df[‘X’], df[‘Y’], s=16) # s 控制点的大小

![](img/6dd65e631f8d1be145fa9056de064b5a_43.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_45.png)

# 2. 计算回归线对应的Y值
# 使用 np.poly1d 根据系数生成多项式函数，方便计算
regression_func = np.poly1d(coefficients)
Y_pred = regression_func(df[‘X’])

![](img/6dd65e631f8d1be145fa9056de064b5a_47.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_49.png)

# 3. 绘制回归线
plt.plot(df[‘X’], Y_pred, color=‘red’) # 将线设置为红色

![](img/6dd65e631f8d1be145fa9056de064b5a_51.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_53.png)

plt.show()
```
通过这张图，我们可以清晰地看到两个指标之间的线性关系趋势。

![](img/6dd65e631f8d1be145fa9056de064b5a_55.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_57.png)

---

![](img/6dd65e631f8d1be145fa9056de064b5a_59.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_61.png)

## 计算相关系数

![](img/6dd65e631f8d1be145fa9056de064b5a_63.png)

除了回归方程，相关系数是衡量两个变量之间线性关系强度和方向的另一个重要指标。它的取值范围在-1到1之间。

在Pandas中，计算两个序列的相关系数非常简单。我们可以直接使用`.corr()`方法。

![](img/6dd65e631f8d1be145fa9056de064b5a_65.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_67.png)

以下是计算相关系数的方法：
```python
# 计算两个指标之间的相关系数
correlation = df[‘X’].corr(df[‘Y’])
print(f“相关系数为： {correlation}“)

![](img/6dd65e631f8d1be145fa9056de064b5a_69.png)

# 如果DataFrame中有多个列，可以直接计算相关系数矩阵
correlation_matrix = df[[‘X‘， ‘Y‘]].corr()
print(correlation_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自己与自己的完全相关）。

---

![](img/6dd65e631f8d1be145fa9056de064b5a_71.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_73.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_75.png)

## 进阶分析：动态相关系数

![](img/6dd65e631f8d1be145fa9056de064b5a_77.png)

在实际分析中，指标间的关系可能随时间变化。因此，我们可以计算动态的、滚动窗口下的相关系数，以观察关系如何随时间演变。

![](img/6dd65e631f8d1be145fa9056de064b5a_79.png)

这个想法是：设定一个固定大小的窗口（例如250天），在时间轴上滚动这个窗口。在每个窗口内，计算两个指标的相关系数。这样就能得到一条随时间变化的相关系数曲线。

以下是实现滚动窗口相关系数计算的步骤：

1.  使用`.rolling()`方法创建滚动窗口对象。
2.  对窗口对象应用`.corr()`方法，并传入第二个指标作为参数。
3.  将结果可视化。

![](img/6dd65e631f8d1be145fa9056de064b5a_81.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_83.png)

核心代码如下：
```python
# 设置窗口大小
window_size = 250

![](img/6dd65e631f8d1be145fa9056de064b5a_85.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_87.png)

# 计算指标X相对于指标Y的滚动相关系数
rolling_corr = df[‘X’].rolling(window=window_size).corr(df[‘Y’])

![](img/6dd65e631f8d1be145fa9056de064b5a_89.png)

# 绘制滚动相关系数变化图
rolling_corr.plot(figsize=(11， 6))
plt.title(‘滚动窗口相关系数变化’)
plt.ylabel(‘相关系数’)
plt.show()
```
这张图展示了在两个指标在不同时期关联性的强弱变化，对于理解市场状态演变非常有价值。

![](img/6dd65e631f8d1be145fa9056de064b5a_91.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_93.png)

---

![](img/6dd65e631f8d1be145fa9056de064b5a_95.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_97.png)

## 总结

![](img/6dd65e631f8d1be145fa9056de064b5a_99.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_101.png)

本节课中我们一起学习了量化分析中两个核心工具：回归方程与相关系数。

![](img/6dd65e631f8d1be145fa9056de064b5a_103.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_105.png)

我们首先使用NumPy的`polyfit`函数构建了线性回归方程，并学会了如何将回归线可视化出来。接着，我们使用Pandas的`corr()`方法计算了静态的相关系数。最后，为了探索关系的动态特性，我们引入了滚动窗口的概念，计算并绘制了动态相关系数随时间的变化图。

![](img/6dd65e631f8d1be145fa9056de064b5a_107.png)

![](img/6dd65e631f8d1be145fa9056de064b5a_109.png)

这些技能是金融时间序列分析和建模的基础。建议你在课后亲自动手实践代码，尝试改变窗口大小或使用不同的指标，以加深对概念和方法的理解。