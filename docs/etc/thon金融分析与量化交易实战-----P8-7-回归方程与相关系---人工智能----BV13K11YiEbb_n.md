# Python金融分析与量化交易实战：P8：7-回归方程与相关系数实例 📈

![](img/40dd899b985d01b30eb8fa263c1597dc_1.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_3.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_5.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融数据分析中衡量两个变量关系的基础工具。我们将通过实际代码演示，从数据预处理到结果可视化的完整流程。

---

## 构建回归方程

![](img/40dd899b985d01b30eb8fa263c1597dc_7.png)

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/40dd899b985d01b30eb8fa263c1597dc_9.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_11.png)

构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练模型，而是直接计算线性回归的系数。

![](img/40dd899b985d01b30eb8fa263c1597dc_13.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_15.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_17.png)

以下是构建回归方程的关键步骤：

![](img/40dd899b985d01b30eb8fa263c1597dc_19.png)

1.  **准备数据**：首先需要明确分析哪两个指标之间的关系。例如，我们分析指标`PX`和`VX`。
2.  **调用函数**：使用`np.polyfit(x, y, deg)`函数。其中`x`和`y`是数据序列，`deg`参数指定回归方程的次数。
    *   `deg=1`：表示构建`y = kx + b`形式的一次线性方程（包含x的一次项和零次项）。
    *   `deg=2`：表示构建包含x二次项的曲线方程。
3.  **处理数据问题**：在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。可以使用`dropna()`方法清理数据。

![](img/40dd899b985d01b30eb8fa263c1597dc_21.png)

核心代码公式如下：
```python
# 清理数据中的空值
data.dropna(inplace=True)

![](img/40dd899b985d01b30eb8fa263c1597dc_23.png)

# 使用polyfit计算一次线性回归的系数k和b
# x_data: 自变量数据序列
# y_data: 因变量数据序列
coefficients = np.polyfit(x_data, y_data, deg=1)
k, b = coefficients  # k为斜率，b为截距
```

![](img/40dd899b985d01b30eb8fa263c1597dc_25.png)

---

![](img/40dd899b985d01b30eb8fa263c1597dc_27.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_29.png)

## 可视化回归结果

![](img/40dd899b985d01b30eb8fa263c1597dc_31.png)

得到回归方程后，我们可以将其可视化，与原始数据散点图叠加，直观地观察拟合效果。

![](img/40dd899b985d01b30eb8fa263c1597dc_33.png)

以下是绘制回归线的步骤：

![](img/40dd899b985d01b30eb8fa263c1597dc_35.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_37.png)

1.  **绘制散点图**：首先用`plt.scatter()`画出原始数据点。
2.  **生成回归线**：利用上一步计算出的系数`k`和`b`，为每个x值计算对应的回归y值，公式为 `y_fit = k * x_data + b`。
3.  **绘制回归线**：使用`plt.plot()`将计算出的`(x_data, y_fit)`点连接成线，并覆盖在散点图上。

```python
import matplotlib.pyplot as plt

![](img/40dd899b985d01b30eb8fa263c1597dc_39.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_41.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_43.png)

# 1. 绘制原始数据散点图
plt.figure(figsize=(16, 9))
plt.scatter(x_data, y_data, label=‘原始数据’)

![](img/40dd899b985d01b30eb8fa263c1597dc_45.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_47.png)

# 2. 计算回归线对应的y值
y_fit = k * x_data + b

![](img/40dd899b985d01b30eb8fa263c1597dc_49.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_51.png)

# 3. 绘制回归线
plt.plot(x_data, y_fit, color=‘red’, label=‘回归线’)

![](img/40dd899b985d01b30eb8fa263c1597dc_53.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_55.png)

plt.legend()
plt.show()
```
通过图表，可以清晰地看到两个指标间的线性关系趋势。

![](img/40dd899b985d01b30eb8fa263c1597dc_57.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_59.png)

---

![](img/40dd899b985d01b30eb8fa263c1597dc_61.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_63.png)

## 计算与可视化相关系数

除了回归方程，相关系数是另一个衡量变量间线性关系强度和方向的指标。

![](img/40dd899b985d01b30eb8fa263c1597dc_65.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_67.png)

在Pandas中，计算相关系数非常简单。对于一个包含多列的数据框，可以直接使用`.corr()`方法得到相关系数矩阵。

![](img/40dd899b985d01b30eb8fa263c1597dc_69.png)

```python
# 计算两个数据序列的相关系数矩阵
correlation_matrix = data[[‘PX‘, ‘VX‘]].corr()
print(correlation_matrix)
```
矩阵中，对角线上的值均为1（变量与自身的完全相关），非对角线上的值则表示两个变量间的相关系数，其值在-1到1之间。

![](img/40dd899b985d01b30eb8fa263c1597dc_71.png)

---

![](img/40dd899b985d01b30eb8fa263c1597dc_73.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_75.png)

## 附加任务：分析相关系数随时间的变化

![](img/40dd899b985d01b30eb8fa263c1597dc_77.png)

在实际分析中，变量间的关系可能并非一成不变。我们可以通过计算滚动窗口相关系数，来观察关系如何随时间演变。

![](img/40dd899b985d01b30eb8fa263c1597dc_79.png)

以下是实现思路：

1.  **定义滚动窗口**：使用`.rolling(window)`方法。例如，`window=250`表示以250个数据点为一个窗口。
2.  **计算窗口相关系数**：对滚动窗口应用`.corr()`计算，并指定另一个固定序列作为相关对象。这样，每个窗口都会计算一次它与另一个指标的相关系数。
3.  **可视化结果**：将计算出的随时间变化的相关系数序列绘制成折线图。

![](img/40dd899b985d01b30eb8fa263c1597dc_81.png)

```python
# 计算PX与VX在250天滚动窗口下的相关系数变化
rolling_corr = data[‘PX‘].rolling(window=250).corr(other=data[‘VX‘])

![](img/40dd899b985d01b30eb8fa263c1597dc_83.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_85.png)

# 可视化
plt.figure(figsize=(11, 6))
rolling_corr.plot()
plt.title(‘PX与VX相关系数随时间变化（250天窗口）‘)
plt.show()
```
这张图能揭示两个金融指标间的动态关联性，比如在某些市场阶段关联性增强或减弱。

![](img/40dd899b985d01b30eb8fa263c1597dc_87.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_89.png)

---

![](img/40dd899b985d01b30eb8fa263c1597dc_91.png)

## 总结

![](img/40dd899b985d01b30eb8fa263c1597dc_93.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_95.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_97.png)

本节课中我们一起学习了金融数据分析中的两个核心关系度量工具。

![](img/40dd899b985d01b30eb8fa263c1597dc_99.png)

*   **回归方程**：用于量化并预测一个变量如何随另一个变量变化，我们使用`np.polyfit`构建方程并通过绘图进行验证。
*   **相关系数**：用于衡量两个变量线性关系的强度与方向，我们使用`.corr()`快速计算，并进一步通过滚动窗口分析了关系的动态变化。

![](img/40dd899b985d01b30eb8fa263c1597dc_101.png)

![](img/40dd899b985d01b30eb8fa263c1597dc_103.png)

建议大家在课后对照代码实践一遍，理解每一步的操作目的。可以尝试修改参数（如回归方程的次数、滚动窗口的大小），观察结果如何变化，这能帮助深化理解。这些技能是进行更复杂金融建模与量化分析的重要基础。