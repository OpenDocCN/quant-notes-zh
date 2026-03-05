# Python金融分析与量化交易实战：P8：07-7-回归方程与相关系数实例 📈

![](img/10b2082768fb0e38bd0265cd618b23b9_1.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融数据分析中衡量两个变量之间关系强度的核心方法。我们将通过实际代码演示，从数据预处理到结果可视化的完整流程。

![](img/10b2082768fb0e38bd0265cd618b23b9_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/10b2082768fb0e38bd0265cd618b23b9_7.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_9.png)

构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/10b2082768fb0e38bd0265cd618b23b9_11.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_13.png)

**公式**：`y = kx + b`

![](img/10b2082768fb0e38bd0265cd618b23b9_15.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_17.png)

以下是构建回归方程的具体步骤：

![](img/10b2082768fb0e38bd0265cd618b23b9_19.png)

1.  首先，我们需要准备数据。假设我们有两个指标：`dpx`和`vix`。
2.  使用`np.polyfit`函数，传入`x`值（`dpx`）、`y`值（`vix`）以及多项式的阶数（`deg`）。
3.  阶数`deg=1`表示我们构建的是一元一次线性方程（即`y = kx + b`）。如果指定`deg=2`，则方程会包含`x`的二次项，可以拟合曲线。

![](img/10b2082768fb0e38bd0265cd618b23b9_21.png)

```python
import numpy as np
# 假设 df 是包含数据的DataFrame，且已处理空值
coefficients = np.polyfit(df[‘dpx’], df[‘vix’], deg=1)
# coefficients 返回 [k, b]
```

![](img/10b2082768fb0e38bd0265cd618b23b9_23.png)

在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。可以使用`df.dropna(inplace=True)`进行清理。

![](img/10b2082768fb0e38bd0265cd618b23b9_25.png)

---

![](img/10b2082768fb0e38bd0265cd618b23b9_27.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_29.png)

## 可视化回归结果

![](img/10b2082768fb0e38bd0265cd618b23b9_31.png)

得到回归系数后，我们可以在散点图上将这条回归线画出来，使关系更直观。

![](img/10b2082768fb0e38bd0265cd618b23b9_33.png)

以下是绘制散点图与回归线的步骤：

![](img/10b2082768fb0e38bd0265cd618b23b9_35.png)

1.  首先，使用`matplotlib`绘制原始数据的散点图。
2.  然后，根据回归方程`y = kx + b`，计算出一系列对应的`y`值。
3.  最后，将这些点连接成线，覆盖在散点图上。

![](img/10b2082768fb0e38bd0265cd618b23b9_37.png)

```python
import matplotlib.pyplot as plt

![](img/10b2082768fb0e38bd0265cd618b23b9_39.png)

# 绘制散点图
plt.figure(figsize=(16, 9))
plt.scatter(x=df[‘dpx’], y=df[‘vix’])

![](img/10b2082768fb0e38bd0265cd618b23b9_41.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_43.png)

# 计算回归线的y值
x_line = df[‘dpx’]
y_line = coefficients[0] * x_line + coefficients[1]

![](img/10b2082768fb0e38bd0265cd618b23b9_45.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_47.png)

# 绘制回归线
plt.plot(x_line, y_line, color=‘red’)
plt.show()
```

![](img/10b2082768fb0e38bd0265cd618b23b9_49.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_51.png)

通过图像，我们可以清晰地看到两个指标之间是正相关、负相关还是无明显线性关系。需要注意的是，本例中使用的数据是增长率，在分析时需明确数据的实际含义。

![](img/10b2082768fb0e38bd0265cd618b23b9_53.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_55.png)

---

![](img/10b2082768fb0e38bd0265cd618b23b9_57.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_59.png)

## 计算与可视化相关系数

![](img/10b2082768fb0e38bd0265cd618b23b9_61.png)

除了回归方程，相关系数是另一个衡量变量间线性关系强度的关键指标。

![](img/10b2082768fb0e38bd0265cd618b23b9_63.png)

计算相关系数非常简单，Pandas的`.corr()`方法可以直接为DataFrame中的所有数值列生成相关系数矩阵。

![](img/10b2082768fb0e38bd0265cd618b23b9_65.png)

```python
correlation_matrix = df[[‘dpx’, ‘vix’]].corr()
print(correlation_matrix)
```

![](img/10b2082768fb0e38bd0265cd618b23b9_67.png)

矩阵中，对角线上的值均为1（变量与自身的完全相关），非对角线上的值表示两个变量间的相关系数，矩阵是对称的。

![](img/10b2082768fb0e38bd0265cd618b23b9_69.png)

---

![](img/10b2082768fb0e38bd0265cd618b23b9_71.png)

## 进阶分析：滚动相关系数

![](img/10b2082768fb0e38bd0265cd618b23b9_73.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_75.png)

为了更深入地分析，我们可以研究相关系数如何随时间变化。这引出了一个常见的进阶任务：计算滚动窗口下的相关系数。

![](img/10b2082768fb0e38bd0265cd618b23b9_77.png)

具体思路是：设定一个固定大小的窗口（例如250个交易日），让这个窗口在时间序列上滑动。在每个窗口内，计算两个指标在该时间段内的相关系数。

![](img/10b2082768fb0e38bd0265cd618b23b9_79.png)

以下是实现滚动相关系数的代码：

```python
# 设置滚动窗口大小，例如250天
window_size = 250

![](img/10b2082768fb0e38bd0265cd618b23b9_81.png)

# 计算dpx与vix的滚动相关系数
rolling_corr = df[‘dpx’].rolling(window=window_size).corr(df[‘vix’])

![](img/10b2082768fb0e38bd0265cd618b23b9_83.png)

# 可视化滚动相关系数的变化
plt.figure(figsize=(11, 6))
rolling_corr.plot()
plt.show()
```

![](img/10b2082768fb0e38bd0265cd618b23b9_85.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_87.png)

这张图展示了两个指标间的相关性在不同时期是如何波动的，这比单一的全局相关系数包含了更多动态信息。

![](img/10b2082768fb0e38bd0265cd618b23b9_89.png)

---

![](img/10b2082768fb0e38bd0265cd618b23b9_91.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_93.png)

## 总结

![](img/10b2082768fb0e38bd0265cd618b23b9_95.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_97.png)

本节课中我们一起学习了金融数据分析中两个核心工具：回归方程与相关系数。

![](img/10b2082768fb0e38bd0265cd618b23b9_99.png)

我们首先使用`np.polyfit`构建了线性回归方程，并通过可视化将关系直观呈现。接着，我们使用`.corr()`方法快速计算了全局相关系数。最后，我们通过引入滚动窗口的概念，计算并可视化了随时间变化的动态相关系数，这能帮助我们捕捉市场关系在不同阶段的变化。

![](img/10b2082768fb0e38bd0265cd618b23b9_101.png)

![](img/10b2082768fb0e38bd0265cd618b23b9_103.png)

建议大家在课后对照代码练习，尝试修改参数（如窗口大小），以加深对数据处理、统计分析和结果展示全流程的理解。这些技能是进行更复杂量化分析的基础。