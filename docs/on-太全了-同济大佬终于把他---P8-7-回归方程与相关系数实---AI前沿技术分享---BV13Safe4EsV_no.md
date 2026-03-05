# 金融数据分析与Python应用：P8：回归方程与相关系数实例 📈

![](img/378a257277986a06797a043cb8e84cb3_1.png)

![](img/378a257277986a06797a043cb8e84cb3_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，以分析两个金融指标之间的关系。我们将使用NumPy和Pandas库来完成数据处理、模型构建和结果可视化。

![](img/378a257277986a06797a043cb8e84cb3_5.png)

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

构建回归方程的方法很多，许多工具包都可以完成。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/378a257277986a06797a043cb8e84cb3_7.png)

![](img/378a257277986a06797a043cb8e84cb3_9.png)

![](img/378a257277986a06797a043cb8e84cb3_11.png)

以下是构建回归方程的核心步骤：

![](img/378a257277986a06797a043cb8e84cb3_13.png)

![](img/378a257277986a06797a043cb8e84cb3_15.png)

1.  **准备数据**：首先，我们需要两个指标的数据。例如，我们有两个变量`X`和`Y`，分别代表两个金融指标。
2.  **调用函数**：使用`np.polyfit(X, Y, deg)`函数。其中`deg`参数指定了回归方程的多项式次数。
    *   `deg=1`：表示构建一个**一次线性方程**，形式为 **Y = kX + b**。
    *   `deg=2`：表示构建一个**二次曲线方程**，形式为 **Y = aX² + bX + c**。
3.  **处理数据问题**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。我们可以使用`dropna()`方法清理数据。

![](img/378a257277986a06797a043cb8e84cb3_17.png)

![](img/378a257277986a06797a043cb8e84cb3_19.png)

以下是构建一次线性回归方程的代码示例：

![](img/378a257277986a06797a043cb8e84cb3_21.png)

```python
import numpy as np
import pandas as pd

![](img/378a257277986a06797a043cb8e84cb3_23.png)

# 假设 df 是一个包含 ‘指标A‘ 和 ‘指标B‘ 列的DataFrame
# 清理数据中的空值
df.dropna(inplace=True)

# 准备X和Y的数据
X = df[‘指标A‘].values
Y = df[‘指标B‘].values

![](img/378a257277986a06797a043cb8e84cb3_25.png)

![](img/378a257277986a06797a043cb8e84cb3_27.png)

# 使用polyfit计算一次线性回归的系数 (k, b)
coefficients = np.polyfit(X, Y, deg=1)
k, b = coefficients
print(f“回归方程为: Y = {k:.4f} * X + {b:.4f}“)
```

![](img/378a257277986a06797a043cb8e84cb3_29.png)

![](img/378a257277986a06797a043cb8e84cb3_31.png)

## 可视化回归结果

得到回归方程后，我们可以将其可视化，以便更直观地观察变量间的关系。

![](img/378a257277986a06797a043cb8e84cb3_33.png)

![](img/378a257277986a06797a043cb8e84cb3_35.png)

以下是绘制散点图和回归线的步骤：

![](img/378a257277986a06797a043cb8e84cb3_37.png)

1.  使用Matplotlib绘制原始数据的散点图。
2.  根据回归方程 **Y = kX + b**，计算出一系列对应的Y值。
3.  将这些(X, Y)点连接起来，绘制出回归直线。

以下是实现可视化的代码：

![](img/378a257277986a06797a043cb8e84cb3_39.png)

![](img/378a257277986a06797a043cb8e84cb3_41.png)

![](img/378a257277986a06797a043cb8e84cb3_43.png)

```python
import matplotlib.pyplot as plt

![](img/378a257277986a06797a043cb8e84cb3_45.png)

![](img/378a257277986a06797a043cb8e84cb3_47.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(X, Y, alpha=0.5, label=‘原始数据点‘)

![](img/378a257277986a06797a043cb8e84cb3_49.png)

![](img/378a257277986a06797a043cb8e84cb3_51.png)

# 生成用于绘制回归线的X值范围
X_line = np.linspace(X.min(), X.max(), 100)
# 根据回归方程计算对应的Y值
Y_line = k * X_line + b

![](img/378a257277986a06797a043cb8e84cb3_53.png)

![](img/378a257277986a06797a043cb8e84cb3_55.png)

# 绘制回归线
plt.plot(X_line, Y_line, color=‘red‘, linewidth=2, label=‘回归直线‘)

![](img/378a257277986a06797a043cb8e84cb3_57.png)

![](img/378a257277986a06797a043cb8e84cb3_59.png)

plt.xlabel(‘指标A‘)
plt.ylabel(‘指标B‘)
plt.title(‘指标A与指标B的回归分析‘)
plt.legend()
plt.grid(True)
plt.show()
```

![](img/378a257277986a06797a043cb8e84cb3_61.png)

![](img/378a257277986a06797a043cb8e84cb3_63.png)

## 计算相关系数

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。其值介于-1到1之间。

![](img/378a257277986a06797a043cb8e84cb3_65.png)

在Pandas中，计算相关系数非常简单。以下是计算两个指标相关系数的方法：

![](img/378a257277986a06797a043cb8e84cb3_67.png)

![](img/378a257277986a06797a043cb8e84cb3_69.png)

```python
# 计算整个DataFrame所有列之间的相关系数矩阵
correlation_matrix = df.corr()
print(correlation_matrix)

# 直接计算两个特定序列的相关系数
correlation_value = df[‘指标A‘].corr(df[‘指标B‘])
print(f“指标A与指标B的相关系数为: {correlation_value:.4f}“)
```

![](img/378a257277986a06797a043cb8e84cb3_71.png)

![](img/378a257277986a06797a043cb8e84cb3_73.png)

## 附加任务：分析相关系数随时间的变化

![](img/378a257277986a06797a043cb8e84cb3_75.png)

![](img/378a257277986a06797a043cb8e84cb3_77.png)

在实际分析中，两个指标的关系可能不是一成不变的。我们可以通过计算滚动窗口的相关系数，来观察其随时间的变化趋势。

![](img/378a257277986a06797a043cb8e84cb3_79.png)

以下是实现该分析的步骤：

1.  **定义滚动窗口**：例如，定义一个包含250个数据点的窗口。
2.  **计算滚动相关系数**：对其中一个指标序列应用滚动窗口，并在每个窗口内计算其与另一个固定指标的相关系数。
3.  **可视化结果**：将计算出的滚动相关系数绘制成折线图。

以下是实现代码：

![](img/378a257277986a06797a043cb8e84cb3_81.png)

![](img/378a257277986a06797a043cb8e84cb3_83.png)

```python
# 设置滚动窗口大小
window_size = 250

![](img/378a257277986a06797a043cb8e84cb3_85.png)

![](img/378a257277986a06797a043cb8e84cb3_87.png)

# 计算指标A相对于指标B的滚动相关系数
rolling_corr = df[‘指标A‘].rolling(window=window_size).corr(df[‘指标B‘])

![](img/378a257277986a06797a043cb8e84cb3_89.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.title(f‘{window_size}日滚动相关系数变化趋势‘)
plt.xlabel(‘时间‘)
plt.ylabel(‘相关系数‘)
plt.axhline(y=0, color=‘black‘, linestyle=‘--‘, linewidth=0.5) # 添加y=0的参考线
plt.grid(True)
plt.show()
```

![](img/378a257277986a06797a043cb8e84cb3_91.png)

![](img/378a257277986a06797a043cb8e84cb3_93.png)

## 总结

![](img/378a257277986a06797a043cb8e84cb3_95.png)

![](img/378a257277986a06797a043cb8e84cb3_97.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/378a257277986a06797a043cb8e84cb3_99.png)

*   我们使用NumPy的`polyfit`函数构建了线性回归方程 **Y = kX + b**，并通过可视化将数据关系直观呈现。
*   我们学习了如何使用Pandas快速计算变量间的**相关系数**，以量化其线性关联程度。
*   最后，我们通过一个附加任务，探索了如何计算**滚动窗口相关系数**，从而分析两个指标间关系随时间动态变化的情况。

![](img/378a257277986a06797a043cb8e84cb3_101.png)

![](img/378a257277986a06797a043cb8e84cb3_103.png)

这些方法是金融时间序列分析的基础。建议大家在理解代码逻辑后，亲自动手实践，例如尝试改变滚动窗口的大小，观察结果如何变化，以加深对概念和工具的理解。