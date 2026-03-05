# 金融量化分析：P10：7-回归方程与相关系数实例 📈

![](img/76afa94ee344904abf58bf369d80e4c3_1.png)

![](img/76afa94ee344904abf58bf369d80e4c3_2.png)

![](img/76afa94ee344904abf58bf369d80e4c3_3.png)

在本节课中，我们将学习如何使用Python工具包构建回归方程、计算相关系数，并可视化分析结果。我们将通过一个金融时间序列数据的实例，手把手演示从数据处理到结果展示的全过程。

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个金融指标之间的关系。构建回归方程的方法很多，我们可以使用多种工具包。这里我们选择使用NumPy来完成。

NumPy中有一个`polyfit`函数，可以帮助我们计算回归系数。它本质上是通过最小二乘法来拟合一个多项式。

![](img/76afa94ee344904abf58bf369d80e4c3_5.png)

![](img/76afa94ee344904abf58bf369d80e4c3_6.png)

![](img/76afa94ee344904abf58bf369d80e4c3_7.png)

**公式**：对于一元线性回归，其方程为 `Y = kX + b`。其中，`k`是斜率，`b`是截距。

![](img/76afa94ee344904abf58bf369d80e4c3_8.png)

![](img/76afa94ee344904abf58bf369d80e4c3_9.png)

![](img/76afa94ee344904abf58bf369d80e4c3_10.png)

![](img/76afa94ee344904abf58bf369d80e4c3_11.png)

以下是构建回归方程的关键步骤：

![](img/76afa94ee344904abf58bf369d80e4c3_12.png)

1.  **准备数据**：首先，我们需要两个指标的数据。假设我们有两个指标：`指标A`和`指标Y`。
2.  **处理空值**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。我们可以使用`dropna`方法清理数据。
3.  **调用`polyfit`函数**：该函数需要传入三个主要参数：
    *   `x`: 自变量数据序列。
    *   `y`: 因变量数据序列。
    *   `deg`: 多项式的阶数。`deg=1`表示拟合一条直线（一次多项式），`deg=2`则表示拟合一条二次曲线，以此类推。在本例中，我们使用线性关系，因此设定`deg=1`。

![](img/76afa94ee344904abf58bf369d80e4c3_14.png)

**代码示例**：
```python
import numpy as np
# 假设 df[‘指标A‘] 和 df[‘指标Y‘] 是我们的数据
# 1. 清理数据中的空值
df.dropna(inplace=True)
# 2. 构建回归方程 (deg=1 表示线性回归)
coefficients = np.polyfit(df[‘指标A‘], df[‘指标Y‘], deg=1)
# coefficients 是一个数组，例如 [k, b]
k, b = coefficients[0], coefficients[1]
print(f“回归方程为: Y = {k:.4f} * X + {b:.4f}“)
```

运行上述代码后，我们就得到了回归方程的系数`k`和`b`，即解出了方程。

![](img/76afa94ee344904abf58bf369d80e4c3_16.png)

![](img/76afa94ee344904abf58bf369d80e4c3_17.png)

---

![](img/76afa94ee344904abf58bf369d80e4c3_18.png)

![](img/76afa94ee344904abf58bf369d80e4c3_19.png)

## 可视化回归结果

![](img/76afa94ee344904abf58bf369d80e4c3_21.png)

得到回归方程后，我们可以将原始数据点和拟合的回归线一同绘制在图上，使关系一目了然。

![](img/76afa94ee344904abf58bf369d80e4c3_22.png)

![](img/76afa94ee344904abf58bf369d80e4c3_23.png)

以下是绘制散点图和回归线的步骤：

1.  **绘制原始数据散点图**：使用`matplotlib`的`scatter`函数绘制两个指标的散点图。
2.  **生成回归线的数据点**：根据回归方程`Y = kX + b`，利用自变量`X`的序列计算出对应的预测值`Y_pred`。
3.  **绘制回归线**：使用`plot`函数将`(X, Y_pred)`连接成线，并覆盖在散点图上。

![](img/76afa94ee344904abf58bf369d80e4c3_25.png)

![](img/76afa94ee344904abf58bf369d80e4c3_26.png)

![](img/76afa94ee344904abf58bf369d80e4c3_27.png)

**代码示例**：
```python
import matplotlib.pyplot as plt
# 1. 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(df[‘指标A‘], df[‘指标Y‘], label=‘原始数据点‘)
# 2. 计算回归线对应的Y值
x_line = df[‘指标A‘]
y_line = k * x_line + b  # 使用上一步计算出的k和b
# 3. 绘制回归线
plt.plot(x_line, y_line, color=‘red‘, linewidth=2, label=‘回归线‘)
plt.xlabel(‘指标A‘)
plt.ylabel(‘指标Y‘)
plt.legend()
plt.title(‘指标A与指标Y的回归分析‘)
plt.show()
```

![](img/76afa94ee344904abf58bf369d80e4c3_29.png)

通过这张图，我们可以直观地看到两个指标之间的线性关系以及回归线的拟合效果。需要注意的是，在分析前对数据进行适当的预处理（例如，本例中使用的是增长率而非原始价格）会让结果更具解释性。

![](img/76afa94ee344904abf58bf369d80e4c3_31.png)

---

![](img/76afa94ee344904abf58bf369d80e4c3_32.png)

![](img/76afa94ee344904abf58bf369d80e4c3_33.png)

![](img/76afa94ee344904abf58bf369d80e4c3_34.png)

![](img/76afa94ee344904abf58bf369d80e4c3_35.png)

## 计算与可视化相关系数

![](img/76afa94ee344904abf58bf369d80e4c3_36.png)

![](img/76afa94ee344904abf58bf369d80e4c3_37.png)

除了回归方程，衡量两个变量之间线性关系强度的常用指标是相关系数。在Pandas中，这可以非常简便地完成。

![](img/76afa94ee344904abf58bf369d80e4c3_39.png)

![](img/76afa94ee344904abf58bf369d80e4c3_40.png)

以下是计算相关系数的步骤：

*   对于包含多个指标的DataFrame，可以直接使用`.corr()`方法计算所有指标两两之间的相关系数矩阵。
*   对于两个特定的序列，也可以直接计算它们之间的相关系数。

![](img/76afa94ee344904abf58bf369d80e4c3_42.png)

![](img/76afa94ee344904abf58bf369d80e4c3_43.png)

**代码示例**：
```python
# 计算整个DataFrame的相关系数矩阵
correlation_matrix = df[[‘指标A‘, ‘指标Y‘]].corr()
print(correlation_matrix)
# 矩阵对角线上的值均为1（自己与自己的完全相关），非对角线上的值就是两个指标间的相关系数。
```

![](img/76afa94ee344904abf58bf369d80e4c3_45.png)

---

## 进阶分析：滑动窗口相关系数

![](img/76afa94ee344904abf58bf369d80e4c3_47.png)

![](img/76afa94ee344904abf58bf369d80e4c3_48.png)

![](img/76afa94ee344904abf58bf369d80e4c3_49.png)

为了更深入地分析，我们可以研究相关系数如何随时间变化。这引入了“滑动窗口”的概念。

![](img/76afa94ee344904abf58bf369d80e4c3_51.png)

**核心思路**：我们不计算整个时间序列的单一相关系数，而是设置一个固定大小的窗口（例如250个交易日）。这个窗口在时间轴上滑动，每滑动一次，就计算窗口内两个指标数据的相关系数。这样，我们就得到了一个随时间变化的相关系数序列。

![](img/76afa94ee344904abf58bf369d80e4c3_53.png)

以下是实现滑动窗口相关系数的步骤：

1.  **选择窗口大小**：例如，`window=250`。
2.  **使用`.rolling()`方法**：在Pandas Series上调用`.rolling(window)`可以创建一个滑动窗口对象。
3.  **计算窗口内相关系数**：对滑动窗口对象调用`.corr(other_series)`，其中`other_series`是另一个指标序列。这将为每个窗口位置计算一个相关系数值。

![](img/76afa94ee344904abf58bf369d80e4c3_55.png)

**代码示例**：
```python
# 计算指标A与指标Y的滑动窗口相关系数 (窗口大小=250)
rolling_corr = df[‘指标A‘].rolling(window=250).corr(df[‘指标Y‘])
# 可视化结果
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.xlabel(‘时间‘)
plt.ylabel(‘相关系数‘)
plt.title(‘指标A与指标Y的滑动窗口相关系数变化 (窗口=250)‘)
plt.axhline(y=0, color=‘black‘, linestyle=‘--‘, linewidth=0.5) # 添加y=0的参考线
plt.show()
```

这张图展示了两个指标间关系的动态变化，比单一的静态相关系数包含了更多信息，例如可以观察在特定市场时期关系是增强还是减弱。

![](img/76afa94ee344904abf58bf369d80e4c3_57.png)

![](img/76afa94ee344904abf58bf369d80e4c3_58.png)

---

![](img/76afa94ee344904abf58bf369d80e4c3_59.png)

![](img/76afa94ee344904abf58bf369d80e4c3_60.png)

![](img/76afa94ee344904abf58bf369d80e4c3_61.png)

## 总结

![](img/76afa94ee344904abf58bf369d80e4c3_63.png)

![](img/76afa94ee344904abf58bf369d80e4c3_64.png)

本节课中我们一起学习了金融量化分析中的两个核心工具：**回归方程**与**相关系数**。

![](img/76afa94ee344904abf58bf369d80e4c3_65.png)

![](img/76afa94ee344904abf58bf369d80e4c3_66.png)

*   我们使用NumPy的`polyfit`函数构建了**一元线性回归方程**，并可视化展示了数据点与拟合线。
*   我们使用Pandas的`.corr()`方法便捷地计算了指标间的**相关系数**。
*   作为进阶应用，我们引入了**滑动窗口**的概念，计算并可视化了相关系数随时间的变化情况，从而能进行更动态的分析。

![](img/76afa94ee344904abf58bf369d80e4c3_68.png)

![](img/76afa94ee344904abf58bf369d80e4c3_69.png)

这些方法是金融时间序列分析的基础。建议大家在课后亲自动手，尝试修改代码中的参数（如回归方程的阶数、滑动窗口的大小），以加深理解并探索不同的分析结果。扎实掌握这些基本处理方法，是后续进行更复杂量化建模的重要一步。