# Python金融量化交易：P11：回归方程与相关系数实例 📈

![](img/14c67e75737d9d29af0fec6cc92043cd_1.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，以分析金融时间序列数据中两个指标之间的关系。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化来直观地展示分析结果。

![](img/14c67e75737d9d29af0fec6cc92043cd_5.png)

---

## 构建回归方程

上一节我们介绍了金融时间序列的基本处理方法，本节中我们来看看如何构建回归方程来描述两个指标之间的关系。

![](img/14c67e75737d9d29af0fec6cc92043cd_7.png)

构建回归方程的方法有很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/14c67e75737d9d29af0fec6cc92043cd_9.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_11.png)

以下是构建回归方程的核心步骤：

![](img/14c67e75737d9d29af0fec6cc92043cd_13.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_15.png)

1.  **准备数据**：确保用于回归分析的两个指标数据中没有空值。
2.  **调用函数**：使用`np.polyfit(x, y, deg)`函数，其中`x`和`y`是数据序列，`deg`指定回归方程的次数。
3.  **理解参数**：`deg=1`表示构建`y = kx + b`形式的一元一次线性方程。`deg`值越高，方程包含的项次越高（如二次项、三次项），可以拟合曲线，但本任务中我们使用一次线性关系即可。

![](img/14c67e75737d9d29af0fec6cc92043cd_17.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_19.png)

在计算前，需要处理数据中的空值。我们可以使用Pandas的`dropna`方法。

![](img/14c67e75737d9d29af0fec6cc92043cd_21.png)

```python
# 假设df是包含‘spx’和‘vix’列的DataFrame
df[['spx', 'vix']].dropna(inplace=True)

![](img/14c67e75737d9d29af0fec6cc92043cd_23.png)

# 使用numpy的polyfit函数计算回归系数 (k, b)
coefficients = np.polyfit(df['spx'], df['vix'], deg=1)
k, b = coefficients
```

执行上述代码后，我们就得到了回归方程的系数`k`（斜率）和`b`（截距），从而确定了方程 `y = kx + b`。

![](img/14c67e75737d9d29af0fec6cc92043cd_25.png)

---

![](img/14c67e75737d9d29af0fec6cc92043cd_27.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_29.png)

## 可视化回归方程

![](img/14c67e75737d9d29af0fec6cc92043cd_31.png)

得到回归方程后，我们可以将其与原始数据的散点图一同绘制出来，以便直观地观察关系。

以下是绘制散点图和回归线的步骤：

![](img/14c67e75737d9d29af0fec6cc92043cd_33.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_35.png)

1.  首先，使用Matplotlib绘制原始数据的散点图。
2.  然后，根据回归方程，计算出一系列对应的预测`y`值。
3.  最后，将这些点连接起来，在散点图上画出回归直线。

![](img/14c67e75737d9d29af0fec6cc92043cd_37.png)

```python
import matplotlib.pyplot as plt

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(df['spx'], df['vix'], s=16, label='Data Points')

![](img/14c67e75737d9d29af0fec6cc92043cd_39.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_41.png)

# 生成回归线的x值范围（这里直接用原始数据的x值）
x_line = df['spx']
# 根据回归方程 y = k*x + b 计算对应的y值
y_line = k * x_line + b

![](img/14c67e75737d9d29af0fec6cc92043cd_43.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_45.png)

# 绘制回归线
plt.plot(x_line, y_line, color='red', label='Regression Line')

![](img/14c67e75737d9d29af0fec6cc92043cd_47.png)

plt.xlabel('SPX')
plt.ylabel('VIX')
plt.legend()
plt.show()
```

![](img/14c67e75737d9d29af0fec6cc92043cd_49.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_51.png)

通过这张图，我们可以清晰地看到标准普尔指数(SPX)与恐慌指数(VIX)之间的负相关关系，以及回归直线对数据趋势的拟合情况。

![](img/14c67e75737d9d29af0fec6cc92043cd_53.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_55.png)

需要注意的是，本例中使用的数据是增长率数据。在实际分析中，根据目标选择合适的原始数据或预处理后的数据（如价格、收益率、波动率等）至关重要。

![](img/14c67e75737d9d29af0fec6cc92043cd_57.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_59.png)

---

![](img/14c67e75737d9d29af0fec6cc92043cd_61.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_63.png)

## 计算相关系数

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。

![](img/14c67e75737d9d29af0fec6cc92043cd_65.png)

计算相关系数非常简单，可以直接使用Pandas DataFrame的`.corr()`方法。

![](img/14c67e75737d9d29af0fec6cc92043cd_67.png)

```python
# 计算‘spx’和‘vix’两列数据的相关系数矩阵
correlation_matrix = df[['spx', 'vix']].corr()
print(correlation_matrix)
```

![](img/14c67e75737d9d29af0fec6cc92043cd_69.png)

该矩阵的对角线为1（变量与自身的完全相关），非对角线上的值就是两个指标间的相关系数，其值在-1到1之间。负值表示负相关，正值表示正相关。

![](img/14c67e75737d9d29af0fec6cc92043cd_71.png)

---

![](img/14c67e75737d9d29af0fec6cc92043cd_73.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_75.png)

## 附加任务：滑动窗口相关系数

![](img/14c67e75737d9d29af0fec6cc92043cd_77.png)

为了更深入地分析，我们可以观察相关系数随时间的变化情况。这可以通过计算滑动窗口相关系数来实现。

![](img/14c67e75737d9d29af0fec6cc92043cd_79.png)

核心思路是：随着时间窗口的滑动，在每个窗口内计算两个指标的相关系数，从而得到一个随时间变化的相关系数序列。

以下是实现步骤：

1.  为其中一个指标序列（如‘spx’）创建一个滑动窗口。
2.  在每个窗口内，计算该窗口数据与另一个指标（‘vix’）对应数据的相关系数。
3.  将每个窗口计算出的相关系数按时间顺序连接起来。

![](img/14c67e75737d9d29af0fec6cc92043cd_81.png)

```python
# 设置窗口大小，例如250个交易日
window_size = 250

![](img/14c67e75737d9d29af0fec6cc92043cd_83.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_85.png)

# 计算‘spx’滚动窗口与‘vix’的相关系数
rolling_corr = df['spx'].rolling(window=window_size).corr(df['vix'])

![](img/14c67e75737d9d29af0fec6cc92043cd_87.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_89.png)

# 绘制滚动相关系数随时间的变化
plt.figure(figsize=(12, 6))
rolling_corr.plot(figsize=(12, 6))
plt.title(f'Rolling Correlation (Window={window_size}) between SPX and VIX')
plt.ylabel('Correlation Coefficient')
plt.xlabel('Date')
plt.axhline(y=0, color='black', linestyle='--', linewidth=0.5) # 添加y=0的参考线
plt.show()
```

这张图展示了SPX与VIX之间相关性的动态变化，比单一的静态相关系数包含了更多信息，有助于我们发现特定时期（如市场危机期间）相关性的结构性变化。

![](img/14c67e75737d9d29af0fec6cc92043cd_91.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_93.png)

---

![](img/14c67e75737d9d29af0fec6cc92043cd_95.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_97.png)

## 总结

![](img/14c67e75737d9d29af0fec6cc92043cd_99.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_101.png)

本节课中我们一起学习了金融量化分析中的两个核心工具：回归方程与相关系数。

![](img/14c67e75737d9d29af0fec6cc92043cd_103.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_105.png)

*   **回归方程**：我们使用`np.polyfit`函数构建了一元线性回归方程 `y = kx + b`，并通过可视化将回归直线与数据散点图结合，直观展示了变量间的关系。
*   **相关系数**：我们使用`.corr()`方法快速计算了两个变量的静态相关系数，并通过滑动窗口技术计算了动态的滚动相关系数，从而能够观察关系随时间的变化趋势。

![](img/14c67e75737d9d29af0fec6cc92043cd_107.png)

![](img/14c67e75737d9d29af0fec6cc92043cd_109.png)

这些方法是金融时间序列分析的基础。建议大家在课后亲自动手实践代码，尝试修改参数（如窗口大小、回归方程次数），以加深对概念和代码逻辑的理解。通过熟练运用Pandas和NumPy等工具，我们可以高效地完成金融数据的统计、分析与可视化工作。