# Python金融分析与量化交易实战教程：P8：07-7-回归方程与相关系数实例 📈

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_1.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融数据分析中衡量两个变量间关系的重要方法。我们将使用NumPy和Pandas库，通过实例代码演示从数据处理到结果可视化的完整流程。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_5.png)

---

## 构建回归方程

上一节我们介绍了金融时间序列的基本处理方法，本节中我们来看看如何量化两个指标之间的关系。构建回归方程的方法有很多，我们可以使用多种工具包。这里我们使用NumPy库来完成。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_7.png)

NumPy库中有一个`polyfit`函数，可以帮助我们计算回归模型的系数。它并不是训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_9.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_11.png)

构建回归方程需要传递几个参数。首先，需要明确是哪两个指标之间的关系。例如，我们有指标`SPX`和`VIX`，需要计算它们之间的关系。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_13.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_15.png)

回归方程通常形式为 `Y = KX + B`，其中包含X的一次项和常数项（X的零次项）。实际上，回归方程不仅可以拟合直线，还可以拟合曲线，这通过指定阶数来实现。阶数等于1表示方程只包含X的一次项和常数项；阶数等于2则会包含X的二次项、一次项和常数项。在本任务中，我们只涉及线性关系，因此指定阶数为1即可。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_17.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_19.png)

以下是构建回归方程的核心代码：

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_21.png)

```python
# 假设 df 是包含 SPX 和 VIX 列的 DataFrame
# 首先处理数据中的空值
df.dropna(inplace=True)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_23.png)

# 使用 numpy 的 polyfit 函数计算线性回归系数 (K, B)
# df[‘SPX’] 作为 X， df[‘VIX’] 作为 Y， 1 表示线性拟合
coefficients = np.polyfit(df[‘SPX’], df[‘VIX’], 1)
# coefficients[0] 是斜率 K， coefficients[1] 是截距 B
K, B = coefficients
```

在计算过程中，如果数据包含空值，求逆矩阵时可能会报错。因此，在构建回归方程前，务必对数据进行清洗，使用`dropna()`方法删除含有空值的行。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_25.png)

---

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_27.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_29.png)

## 可视化回归方程

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_31.png)

得到回归方程的系数后，我们可以在散点图上将这条回归线画出来，使关系一目了然。

以下是绘制散点图及回归线的步骤：

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_33.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_35.png)

1.  首先绘制两个指标的散点图。
2.  根据求解出的回归方程系数，计算出一系列对应的Y值。
3.  将这些点连接起来，绘制出回归线。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_37.png)

具体实现代码如下：

```python
import matplotlib.pyplot as plt

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_39.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_41.png)

# 1. 绘制散点图
plt.figure(figsize=(16, 9))
plt.scatter(df[‘SPX’], df[‘VIX’], label=‘Data Points’)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_43.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_45.png)

# 2. 计算回归线对应的Y值
# 使用之前计算出的系数 K 和 B，根据 X 值计算 Y_hat
x_values = df[‘SPX’]
y_predicted = K * x_values + B  # 公式：Y_hat = K * X + B

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_47.png)

# 3. 绘制回归线
plt.plot(x_values, y_predicted, color=‘red’, linewidth=2, label=‘Regression Line’)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_49.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_51.png)

plt.xlabel(‘SPX’)
plt.ylabel(‘VIX’)
plt.legend()
plt.show()
```

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_53.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_55.png)

通过这张图，我们可以清晰地看到`SPX`和`VIX`两个指标之间的线性关系趋势。需要注意的是，本例中使用的数据是增长率，而非原始价格。在实际分析中，根据目标选择合适的预处理数据（如原始价格、收益率、对数收益率等）非常重要。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_57.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_59.png)

---

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_61.png)

## 计算相关系数

除了回归方程，衡量两个变量之间线性关系强度的常用指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_63.png)

对于只有两个指标的数据框，可以直接计算它们的相关系数矩阵：

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_65.png)

```python
# 计算 SPX 和 VIX 的相关系数矩阵
correlation_matrix = df[[‘SPX’, ‘VIX’]].corr()
print(correlation_matrix)
```

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_67.png)

相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示变量与自身的完全相关），非对角线上的值就是两个变量间的相关系数，其值在-1到1之间。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_69.png)

---

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_71.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_73.png)

## 附加任务：分析相关系数的动态变化

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_75.png)

为了更深入地分析，我们可以研究相关系数如何随时间变化。例如，计算`SPX`和`VIX`的滚动相关系数。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_77.png)

思路是设置一个滑动窗口（例如250天），在每个窗口内计算两个序列的相关系数，随着窗口在时间轴上滑动，就得到了相关系数的时间序列。

以下是实现代码：

```python
# 设置窗口大小，例如250个交易日
window_size = 250

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_79.png)

# 计算 SPX 相对于 VIX 的滚动相关系数
rolling_corr = df[‘SPX’].rolling(window=window_size).corr(df[‘VIX’])

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_81.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_83.png)

# 可视化滚动相关系数
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f‘Rolling Correlation (Window={window_size} days) between SPX and VIX’)
plt.xlabel(‘Date’)
plt.ylabel(‘Correlation Coefficient’)
plt.axhline(y=0, color=‘black’, linestyle=‘--’, linewidth=0.5) # 添加y=0的参考线
plt.show()
```

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_85.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_87.png)

这张图展示了`SPX`和`VIX`之间相关性的动态演变，比单一的静态相关系数包含了更多信息，可以帮助我们识别市场在不同阶段的关系模式。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_89.png)

---

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_91.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_93.png)

## 总结

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_95.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_97.png)

*   **回归方程**用于量化并预测一个变量如何随另一个变量变化，我们使用`np.polyfit`函数计算系数，并通过绘图进行可视化。
*   **相关系数**用于衡量两个变量线性关系的强度和方向，Pandas的`.corr()`方法可以方便地进行计算。
*   **动态分析**通过计算滚动相关系数，我们可以观察变量间关系随时间的变化，获得更深入的洞察。

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_99.png)

![](img/6ba31ab2f0efbe9fddfbe5061b37be0c_101.png)

建议大家在课后对照代码实践一遍，理解每一步的作用。可以尝试修改参数，例如改变回归方程的阶数、调整滚动窗口的大小，观察结果有何不同。熟练掌握这些基础分析方法，是进行更复杂金融建模和量化交易策略开发的基石。