# Python金融量化分析：P11：回归方程与相关系数实例 📈

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_1.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_3.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_5.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融量化分析中衡量两个变量间关系的基础工具。我们将通过具体的代码实例，展示如何从数据处理到结果可视化的完整流程。

---

## 构建回归方程

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_7.png)

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量间的线性关系。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_9.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_11.png)

构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_13.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_15.png)

**公式**：`y = kx + b`

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_17.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_19.png)

以下是使用`polyfit`函数的关键步骤：

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_21.png)

1.  传入两个需要计算关系的指标数据。
2.  指定回归的阶数（`deg`参数）。`deg=1`表示构建`y = kx + b`这样的一元一次线性方程；`deg=2`则会构建包含`x`二次项的曲线方程。在本任务中，我们使用线性关系，因此指定`deg=1`。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_23.png)

在计算前，必须确保数据中没有空值（NaN），否则在求逆矩阵时会报错。因此，我们需要先使用`.dropna()`方法清理数据。

```python
# 假设 df 是包含数据的DataFrame，x_col 和 y_col 是列名
# 清理数据
df_clean = df[[x_col, y_col]].dropna()

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_25.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_27.png)

# 使用 polyfit 计算回归系数 k 和 b
coefficients = np.polyfit(df_clean[x_col], df_clean[y_col], deg=1)
k, b = coefficients
```

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_29.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_31.png)

---

## 可视化回归方程

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_33.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_35.png)

得到回归系数后，我们可以在散点图上将这条回归直线画出来，使关系更直观。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_37.png)

以下是绘制散点图及回归线的步骤：

1.  首先，用散点图画出原始数据点。
2.  然后，根据计算出的回归方程 `y = kx + b`，生成对应的预测y值。
3.  最后，将这条回归线添加到图中。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_39.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_41.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_43.png)

```python
import matplotlib.pyplot as plt

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_45.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_47.png)

# 绘制原始数据散点图
plt.scatter(df_clean[x_col], df_clean[y_col], s=16, label='Data Points')

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_49.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_51.png)

# 生成回归线的x值范围（通常使用原始数据的x范围）
x_line = np.linspace(df_clean[x_col].min(), df_clean[x_col].max(), 100)
# 根据回归方程计算对应的y值
y_line = k * x_line + b

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_53.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_55.png)

# 绘制回归线
plt.plot(x_line, y_line, color='red', label='Regression Line')

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_57.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_59.png)

plt.xlabel(x_col)
plt.ylabel(y_col)
plt.legend()
plt.show()
```

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_61.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_63.png)

---

## 计算相关系数

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_65.png)

除了回归方程，相关系数是另一个衡量两个变量线性关系强度和方向的常用指标。在pandas中，计算相关系数非常简单。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_67.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_69.png)

以下是计算并展示相关系数矩阵的方法：

```python
# 计算两列数据之间的相关系数矩阵
correlation_matrix = df_clean.corr()
print(correlation_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（变量与自身的完全相关），非对角线上的值表示两个不同变量间的相关系数。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_71.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_73.png)

---

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_75.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_77.png)

## 附加任务：计算滚动相关系数

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_79.png)

现在，我们增加一点难度。在金融分析中，变量间的关系可能随时间变化。因此，我们可以计算滚动窗口内的相关系数，以观察其动态变化。

以下是计算滚动相关系数的思路和步骤：

1.  **定义窗口**：选择一个时间窗口（例如250个交易日）。
2.  **滚动计算**：对于第一个指标的每个滚动窗口，计算其与第二个指标在整个分析期内对应数据的相关系数。
3.  **可视化**：将计算出的滚动相关系数随时间的变化绘制出来。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_81.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_83.png)

```python
# 设置滚动窗口大小
window_size = 250

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_85.png)

# 计算指标A与指标B的滚动相关系数
rolling_corr = df['指标A'].rolling(window=window_size).corr(df['指标B'])

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_87.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_89.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(10, 6))
rolling_corr.plot()
plt.title(f'Rolling Correlation (Window={window_size}) between 指标A and 指标B')
plt.ylabel('Correlation Coefficient')
plt.xlabel('Date')
plt.axhline(y=0, color='black', linestyle='--', linewidth=0.5) # 添加y=0的参考线
plt.show()
```
通过这张图，我们可以清晰地看到两个指标间的关联性如何随着时间推移而增强或减弱。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_91.png)

---

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_93.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_95.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_97.png)

## 总结

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_99.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_101.png)

本节课中我们一起学习了金融量化分析中的两个核心关系分析工具：

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_103.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_105.png)

1.  **回归方程**：我们使用NumPy的`polyfit`函数构建了线性回归方程 `y = kx + b`，并通过绘图将模型结果可视化。
2.  **相关系数**：我们使用pandas快速计算了变量间的静态相关系数，并进一步通过**滚动窗口**的方法计算了动态的、随时间变化的相关系数，这能帮助我们捕捉市场关系中更细微的趋势。

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_107.png)

![](img/cb9e96ca0ac7ddf8668ea527fa543ed1_109.png)

这些方法是使用pandas和NumPy进行数据统计、分析与展示的基础。建议你在课后对照代码练习一遍，尝试修改参数（如滚动窗口的大小），以加深对每个步骤功能的理解。本章内容涵盖了时间序列分析的关键操作，熟练掌握将为后续更复杂的分析打下坚实基础。