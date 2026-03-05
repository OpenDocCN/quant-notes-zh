# Python金融分析与量化交易实战：P11：11.5-回归方程与相关系数实例 📈

![](img/f6cb38dc707ff433b89cbb56dc4d310c_1.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_3.png)

在本节课中，我们将学习如何使用Python构建回归方程和计算相关系数，这是分析两个金融指标之间关系的基础工具。我们将使用NumPy和Pandas库，通过实例代码演示从数据处理到结果可视化的完整流程。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_7.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_9.png)

构建回归方程的方法很多，许多工具包都可以完成。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练模型，而是直接计算回归方程的系数。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_11.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_13.png)

**公式**：`y = kx + b`

![](img/f6cb38dc707ff433b89cbb56dc4d310c_15.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_17.png)

以下是使用`polyfit`函数的关键步骤：

![](img/f6cb38dc707ff433b89cbb56dc4d310c_19.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_21.png)

1.  **准备数据**：需要传入两个指标的数据，例如`x`和`y`。
2.  **指定阶数**：`polyfit`函数的第三个参数`deg`指定回归方程的阶数。`deg=1`表示构建一次线性方程（即`y = kx + b`），包含`x`的一次项和零次项。`deg=2`则表示构建二次方程，包含`x`的二次项、一次项和零次项。在本例中，我们使用一次线性方程。
3.  **处理空值**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。我们可以使用`dropna`方法清理数据。

```python
# 清理数据中的空值
data.dropna(inplace=True)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_23.png)

# 使用polyfit计算回归系数 (k, b)
# x_data: 自变量数据
# y_data: 因变量数据
# 1: 指定构建一次线性方程
coefficients = np.polyfit(x_data, y_data, 1)
k, b = coefficients
```

![](img/f6cb38dc707ff433b89cbb56dc4d310c_25.png)

---

![](img/f6cb38dc707ff433b89cbb56dc4d310c_27.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_29.png)

## 可视化回归方程

![](img/f6cb38dc707ff433b89cbb56dc4d310c_31.png)

得到回归系数后，我们可以将回归线绘制在散点图上，直观地展示两个指标的关系。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_33.png)

以下是绘制散点图和回归线的步骤：

![](img/f6cb38dc707ff433b89cbb56dc4d310c_35.png)

1.  首先，使用`scatter`函数绘制原始数据的散点图。
2.  然后，根据回归方程`y = kx + b`，计算出一系列对应的`y`值。
3.  最后，使用`plot`函数将回归线绘制在同一个图中。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_37.png)

```python
import matplotlib.pyplot as plt

![](img/f6cb38dc707ff433b89cbb56dc4d310c_39.png)

# 1. 绘制散点图
plt.scatter(x_data, y_data, s=16)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_41.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_43.png)

# 2. 计算回归线对应的y值
y_pred = k * x_data + b

![](img/f6cb38dc707ff433b89cbb56dc4d310c_45.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_47.png)

# 3. 绘制回归线，设置为红色
plt.plot(x_data, y_pred, color=‘red’)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_49.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_51.png)

plt.show()
```

![](img/f6cb38dc707ff433b89cbb56dc4d310c_53.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_55.png)

通过可视化，我们可以清晰地看到标准普尔指数（S&P）与恐慌指数（VIX）之间的负相关关系。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_57.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_59.png)

---

![](img/f6cb38dc707ff433b89cbb56dc4d310c_61.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_63.png)

## 计算相关系数

![](img/f6cb38dc707ff433b89cbb56dc4d310c_65.png)

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_67.png)

在Pandas中，计算相关系数非常简单。对于一个包含多列数据的DataFrame，可以直接调用`.corr()`方法得到相关系数矩阵。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_69.png)

```python
# 假设df是一个包含‘S&P’和‘VIX’两列的DataFrame
correlation_matrix = df[[‘S&P‘, ‘VIX‘]].corr()
print(correlation_matrix)
```

![](img/f6cb38dc707ff433b89cbb56dc4d310c_71.png)

相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自身完全相关），非对角线上的值表示两个变量之间的相关系数，其值在-1到1之间。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_73.png)

---

![](img/f6cb38dc707ff433b89cbb56dc4d310c_75.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_77.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_79.png)

## 附加任务：计算滚动相关系数

为了更深入地分析，我们可以研究相关系数随时间的变化情况。这需要计算滚动窗口内的相关系数。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_81.png)

以下是计算滚动相关系数的思路和步骤：

![](img/f6cb38dc707ff433b89cbb56dc4d310c_83.png)

1.  **定义窗口**：选择一个时间窗口（例如250天），在时间序列上滑动。
2.  **滚动计算**：在每个窗口内，计算两个指标（如S&P和VIX）的相关系数。
3.  **结果展示**：将计算出的滚动相关系数序列绘制成图，观察其随时间变化的趋势。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_85.png)

```python
# 设置滚动窗口大小为250
window_size = 250

![](img/f6cb38dc707ff433b89cbb56dc4d310c_87.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_89.png)

# 计算S&P序列相对于VIX序列的滚动相关系数
rolling_corr = df[‘S&P‘].rolling(window=window_size).corr(df[‘VIX‘])

![](img/f6cb38dc707ff433b89cbb56dc4d310c_91.png)

# 绘制滚动相关系数变化图
rolling_corr.plot(figsize=(11, 6))
plt.show()
```

![](img/f6cb38dc707ff433b89cbb56dc4d310c_93.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_95.png)

通过这个图表，我们可以看到S&P指数与VIX指数之间的相关性并非恒定不变，而是随着市场环境的变化而波动。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_97.png)

---

![](img/f6cb38dc707ff433b89cbb56dc4d310c_99.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_101.png)

## 总结

![](img/f6cb38dc707ff433b89cbb56dc4d310c_103.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_105.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：**回归方程**与**相关系数**。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_107.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_109.png)

*   我们使用NumPy的`polyfit`函数构建了一次线性回归方程，并通过可视化直观展示了变量间的关系。
*   我们使用Pandas的`.corr()`方法快速计算了变量间的整体相关系数。
*   最后，我们通过计算**滚动相关系数**，探索了变量间关系随时间动态变化的特性，这是时间序列分析中一个非常有用的技巧。

![](img/f6cb38dc707ff433b89cbb56dc4d310c_111.png)

![](img/f6cb38dc707ff433b89cbb56dc4d310c_113.png)

建议大家在课后对照代码练习，理解每一步的作用，并尝试修改参数（如回归方程阶数、滚动窗口大小），以加深对金融时间序列处理和分析方法的理解。