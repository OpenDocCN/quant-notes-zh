# 人工智能金融实战：P23：07-7-回归方程与相关系数实例 📈

![](img/13e91888e01d2f5f55f0d6e2cd0428af_1.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_3.png)

在本节课中，我们将学习如何使用Python构建两个金融指标之间的回归方程，并计算它们的相关系数。我们将通过具体的代码实例，展示如何利用NumPy和Pandas库进行数据处理、模型构建和结果可视化。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_5.png)

---

## 构建回归方程

上一节我们介绍了金融指标的基本分析，本节中我们来看看如何量化两个指标之间的关系。构建回归方程是描述变量间线性关系的一种方法。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_7.png)

构建回归方程的方法很多，许多工具包都可以完成。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_9.png)

以下是使用`np.polyfit`函数的关键步骤：

![](img/13e91888e01d2f5f55f0d6e2cd0428af_11.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_13.png)

1.  **准备数据**：需要传入两个指标的数据序列。
2.  **指定阶数**：确定回归方程中自变量的最高次幂。阶数为1表示我们构建一个一次线性方程 `Y = KX + B`。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_15.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_17.png)

```python
# 假设 df 是包含数据的DataFrame，'column_x' 和 'column_y' 是两个指标列
# 首先处理数据中的空值
df.dropna(inplace=True)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_19.png)

x = df['column_x'].values
y = df['column_y'].values

![](img/13e91888e01d2f5f55f0d6e2cd0428af_21.png)

# 使用 np.polyfit 计算回归系数，1 表示线性回归
coefficients = np.polyfit(x, y, 1)
# coefficients 是一个数组，其中 coefficients[0] 是斜率 K，coefficients[1] 是截距 B
```

![](img/13e91888e01d2f5f55f0d6e2cd0428af_23.png)

在计算前，必须确保数据中没有空值（NaN），否则可能导致计算错误。通过`dropna()`方法可以清理数据。

得到系数K和B后，我们就得到了回归方程 `Y = K * X + B`。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_25.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_27.png)

---

![](img/13e91888e01d2f5f55f0d6e2cd0428af_29.png)

## 可视化回归结果

![](img/13e91888e01d2f5f55f0d6e2cd0428af_31.png)

得到回归方程后，我们可以将其与原始数据的散点图一起绘制出来，直观地观察拟合效果。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_33.png)

以下是绘制散点图和回归线的步骤：

![](img/13e91888e01d2f5f55f0d6e2cd0428af_35.png)

1.  绘制原始数据的散点图。
2.  根据回归方程，生成对应的预测Y值。
3.  将回归线绘制在同一个图上。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_37.png)

```python
import matplotlib.pyplot as plt

# 绘制散点图
plt.figure(figsize=(16, 8))
plt.scatter(x, y, label='原始数据')

![](img/13e91888e01d2f5f55f0d6e2cd0428af_39.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_41.png)

# 根据回归方程计算预测值
# 使用 np.poly1d 根据系数生成一个多项式函数
regression_func = np.poly1d(coefficients)
y_pred = regression_func(x)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_43.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_45.png)

# 绘制回归线
plt.plot(x, y_pred, color='red', linewidth=2, label='回归线')

![](img/13e91888e01d2f5f55f0d6e2cd0428af_47.png)

plt.xlabel('指标X')
plt.ylabel('指标Y')
plt.legend()
plt.show()
```

![](img/13e91888e01d2f5f55f0d6e2cd0428af_49.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_51.png)

通过可视化，我们可以清晰地看到两个指标之间的线性趋势以及回归线对数据的拟合程度。需要注意的是，如果数据是增长率等经过处理的值，在解释回归结果时要考虑其实际经济含义。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_53.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_55.png)

---

![](img/13e91888e01d2f5f55f0d6e2cd0428af_57.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_59.png)

## 计算相关系数

![](img/13e91888e01d2f5f55f0d6e2cd0428af_61.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_63.png)

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。它的取值范围在-1到1之间。

在Pandas中，计算两个序列的相关系数非常简单。

```python
# 计算两个数据列的相关系数
correlation = df['column_x'].corr(df['column_y'])
print(f"指标X与指标Y的相关系数为: {correlation}")
```

![](img/13e91888e01d2f5f55f0d6e2cd0428af_65.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_67.png)

如果数据包含多个指标，可以直接计算整个DataFrame的相关系数矩阵。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_69.png)

```python
# 计算相关系数矩阵
corr_matrix = df[['column_x', 'column_y']].corr()
print(corr_matrix)
```

矩阵对角线上的值均为1（变量与自身的相关性），非对角线上的值就是两两变量间的相关系数，矩阵是对称的。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_71.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_73.png)

---

![](img/13e91888e01d2f5f55f0d6e2cd0428af_75.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_77.png)

## 附加任务：滑动窗口相关系数分析

为了更深入地分析，我们可以研究相关系数随时间的变化情况。这可以通过计算滑动窗口内的相关系数来实现。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_79.png)

以下是实现滑动窗口相关系数分析的步骤：

1.  定义一个窗口大小（例如250个交易日）。
2.  对其中一个指标序列，滚动计算其与另一个固定指标在窗口内的相关系数。
3.  将计算结果按时间绘制出来，观察相关性的动态变化。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_81.png)

```python
# 设置窗口大小
window_size = 250

![](img/13e91888e01d2f5f55f0d6e2cd0428af_83.png)

# 计算滚动相关系数
rolling_corr = df['column_x'].rolling(window=window_size).corr(df['column_y'])

![](img/13e91888e01d2f5f55f0d6e2cd0428af_85.png)

# 绘制结果
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f'{window_size}日滚动相关系数变化')
plt.xlabel('时间')
plt.ylabel('相关系数')
plt.grid(True)
plt.show()
```

![](img/13e91888e01d2f5f55f0d6e2cd0428af_87.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_89.png)

这个分析能帮助我们理解两个指标间的关联性是否稳定，以及在市场不同阶段（如牛市、熊市）关联性如何变化。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_91.png)

---

![](img/13e91888e01d2f5f55f0d6e2cd0428af_93.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_95.png)

## 总结

![](img/13e91888e01d2f5f55f0d6e2cd0428af_97.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_99.png)

*   我们使用NumPy的`polyfit`函数构建了线性回归方程，并可视化展示了拟合效果。
*   我们利用Pandas简便地计算了两个指标之间的相关系数。
*   最后，我们通过一个附加的滑动窗口分析任务，探索了相关系数如何随时间动态变化，这能提供更深入的市场洞察。

![](img/13e91888e01d2f5f55f0d6e2cd0428af_101.png)

![](img/13e91888e01d2f5f55f0d6e2cd0428af_103.png)

建议大家在课后亲自动手实践代码，尝试修改参数（如窗口大小、回归阶数），以加深对金融时间序列处理和分析方法的理解。这些基础技能是进行更复杂量化分析的重要基石。