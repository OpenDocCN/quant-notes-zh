# 量化交易教程：P11：回归方程与相关系数实例 📈

![](img/778e5bf73deedc2c5a9013f4a3184772_1.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_3.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_5.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是量化分析中衡量两个金融指标间关系的基础方法。我们将通过实际代码演示，从数据处理到结果可视化的完整流程。

## 构建回归方程

上一节我们介绍了金融时间序列的基本处理方法，本节中我们来看看如何量化两个指标间的线性关系。构建回归方程的方法很多，我们可以使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/778e5bf73deedc2c5a9013f4a3184772_7.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_9.png)

`polyfit`函数并非训练一个复杂的模型，而是直接计算线性回归的系数。其基本公式为：
**Y = kX + b**
其中，`k`是斜率，`b`是截距。

![](img/778e5bf73deedc2c5a9013f4a3184772_11.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_13.png)

以下是使用`polyfit`函数的关键步骤：

![](img/778e5bf73deedc2c5a9013f4a3184772_15.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_17.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_19.png)

1.  **准备数据**：确保用于计算的两个指标数据中没有空值（NaN），否则会导致计算错误。
2.  **调用函数**：传入自变量X、因变量Y以及多项式的阶数（degree）。
3.  **理解参数**：
    *   `degree=1`：表示拟合一个一次多项式，即直线 `Y = kX + b`。
    *   `degree=2`：表示拟合一个二次多项式，即曲线 `Y = aX² + bX + c`。

![](img/778e5bf73deedc2c5a9013f4a3184772_21.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_23.png)

在本例中，我们研究两个指标间的线性关系，因此设置 `degree=1`。

![](img/778e5bf73deedc2c5a9013f4a3184772_25.png)

```python
# 假设 df 是包含数据的DataFrame，且 ‘X_column‘ 和 ‘Y_column‘ 是列名
# 1. 删除空值
df.dropna(inplace=True)

# 2. 提取X和Y的数据
x_data = df[‘X_column‘].values
y_data = df[‘Y_column‘].values

![](img/778e5bf73deedc2c5a9013f4a3184772_27.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_29.png)

# 3. 使用polyfit计算回归系数 (k, b)
coefficients = np.polyfit(x_data, y_data, deg=1)
k, b = coefficients
print(f“回归方程为: Y = {k:.4f} * X + {b:.4f}“)
```

![](img/778e5bf73deedc2c5a9013f4a3184772_31.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_33.png)

## 可视化回归线

![](img/778e5bf73deedc2c5a9013f4a3184772_35.png)

得到回归方程后，我们可以将其绘制在散点图上，直观地观察拟合效果。

![](img/778e5bf73deedc2c5a9013f4a3184772_37.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_39.png)

以下是绘制散点图及回归线的步骤：

1.  首先绘制两个指标的散点图。
2.  根据回归方程，计算出一系列对应于X值的预测Y值。
3.  将这些点连接起来，绘制出回归直线。

![](img/778e5bf73deedc2c5a9013f4a3184772_41.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_43.png)

```python
import matplotlib.pyplot as plt

![](img/778e5bf73deedc2c5a9013f4a3184772_45.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_47.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_49.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(x_data, y_data, s=16, label=‘数据点‘)

![](img/778e5bf73deedc2c5a9013f4a3184772_51.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_53.png)

# 计算回归线对应的Y值
# np.poly1d(coefficients) 可以根据系数生成一个多项式函数
regression_line = np.poly1d(coefficients)
y_pred = regression_line(x_data)

![](img/778e5bf73deedc2c5a9013f4a3184772_55.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_57.png)

# 绘制回归线
plt.plot(x_data, y_pred, color=‘red‘, linewidth=2, label=‘回归线‘)

![](img/778e5bf73deedc2c5a9013f4a3184772_59.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_61.png)

plt.xlabel(‘X指标‘)
plt.ylabel(‘Y指标‘)
plt.legend()
plt.title(‘指标关系与回归线‘)
plt.show()
```

![](img/778e5bf73deedc2c5a9013f4a3184772_63.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_65.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_67.png)

## 计算相关系数

除了回归方程，相关系数是另一个衡量两个变量间线性关系强度和方向的常用指标。在Pandas中，这可以非常简便地完成。

![](img/778e5bf73deedc2c5a9013f4a3184772_69.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_71.png)

以下是计算相关系数的方法：

![](img/778e5bf73deedc2c5a9013f4a3184772_73.png)

```python
# 计算整个DataFrame的相关系数矩阵
correlation_matrix = df.corr()
print(correlation_matrix)

![](img/778e5bf73deedc2c5a9013f4a3184772_75.png)

# 直接计算两列之间的相关系数
correlation_value = df[‘X_column‘].corr(df[‘Y_column‘])
print(f“X_column 与 Y_column 的相关系数为: {correlation_value:.4f}“)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（变量与自身的完全相关）。非对角线上的值表示不同变量间的相关系数。

![](img/778e5bf73deedc2c5a9013f4a3184772_77.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_79.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_81.png)

## 附加任务：滑动窗口相关系数

![](img/778e5bf73deedc2c5a9013f4a3184772_83.png)

在实际分析中，指标间的关系可能随时间变化。我们可以计算滑动窗口内的相关系数来观察这种动态变化。

以下是计算滑动窗口相关系数的思路与代码：

![](img/778e5bf73deedc2c5a9013f4a3184772_85.png)

1.  **定义窗口**：选择一个时间窗口长度（例如250天）。
2.  **滑动计算**：对于每一个时间点，取其与之前窗口长度内的数据。
3.  **动态关联**：计算该窗口内第一个指标数据与第二个指标**同期**数据的相关系数。

![](img/778e5bf73deedc2c5a9013f4a3184772_87.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_89.png)

```python
# 设置窗口大小
window_size = 250

![](img/778e5bf73deedc2c5a9013f4a3184772_91.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_93.png)

# 计算第一个指标相对于第二个指标的滚动相关系数
rolling_corr = df[‘X_column‘].rolling(window=window_size).corr(df[‘Y_column‘])

![](img/778e5bf73deedc2c5a9013f4a3184772_95.png)

# 可视化结果
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.xlabel(‘时间‘)
plt.ylabel(‘滚动相关系数‘)
plt.title(f‘{window_size}天滑动窗口相关系数变化‘)
plt.axhline(y=0, color=‘black‘, linestyle=‘--‘, linewidth=0.5) # 添加零线
plt.show()
```
这张图可以清晰地展示出两个指标间的关联性如何随着市场环境的变化而增强或减弱。

![](img/778e5bf73deedc2c5a9013f4a3184772_97.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_99.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_101.png)

## 总结

![](img/778e5bf73deedc2c5a9013f4a3184772_103.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_105.png)

本节课中我们一起学习了量化分析中的两个核心工具：回归方程与相关系数。

![](img/778e5bf73deedc2c5a9013f4a3184772_107.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_109.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_111.png)

*   我们使用NumPy的`polyfit`函数构建了线性回归方程，并可视化拟合直线。
*   我们利用Pandas的`.corr()`方法计算了指标间的静态相关系数。
*   我们进一步通过滚动窗口计算了动态的相关系数时间序列，以观察关系如何随时间演变。

![](img/778e5bf73deedc2c5a9013f4a3184772_113.png)

![](img/778e5bf73deedc2c5a9013f4a3184772_115.png)

这些方法是金融时间序列分析和量化策略研究的基础。建议你在理解代码的基础上，尝试修改参数（如窗口大小），或将其应用到不同的数据集中，以加深对概念和工具的理解。