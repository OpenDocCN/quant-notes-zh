# Python金融量化分析：P11：回归方程与相关系数实例 📈

![](img/9a5316deb870c4feb6fc0471a5d82b9e_1.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_2.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_3.png)

在本节课中，我们将学习如何使用Python进行金融数据分析，核心内容包括构建回归方程、计算相关系数以及可视化分析结果。我们将使用NumPy和Pandas等工具包，通过实例操作来掌握这些核心技能。

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个金融指标之间的关系。

构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy来完成。NumPy中的`polyfit`函数可以帮助我们计算回归系数，即拟合一个多项式模型。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_5.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_6.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_7.png)

以下是构建回归方程的关键步骤：

![](img/9a5316deb870c4feb6fc0471a5d82b9e_8.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_9.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_10.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_11.png)

1.  **准备数据**：确保用于计算的两个指标数据中没有空值。
2.  **调用函数**：使用`np.polyfit(x, y, deg)`函数，其中`x`和`y`是数据序列，`deg`指定多项式的阶数。
3.  **理解参数**：`deg=1`表示拟合一个线性方程 `y = kx + b`，它包含`x`的一次项和常数项。如果`deg=2`，则拟合的方程会包含`x`的二次项。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_13.png)

在本次任务中，我们分析两个指标之间的线性关系，因此设置`deg=1`。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_15.png)

**核心代码**：
```python
# 假设 df[‘指标A‘] 和 df[‘指标B‘] 是我们的数据
# 首先处理可能存在的空值
df.dropna(inplace=True)

# 使用np.polyfit进行线性拟合，返回系数k和b
coefficients = np.polyfit(df[‘指标A‘], df[‘指标B‘], deg=1)
k, b = coefficients
```
执行上述代码后，我们就得到了回归方程的系数`k`（斜率）和`b`（截距），从而确定了方程 `y = kx + b`。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_17.png)

---

![](img/9a5316deb870c4feb6fc0471a5d82b9e_18.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_19.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_20.png)

## 可视化回归方程

得到回归方程后，我们可以将其可视化，与原始数据散点图叠加，直观地观察拟合效果。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_22.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_23.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_24.png)

以下是绘制回归线的步骤：

1.  **绘制散点图**：首先用`plt.scatter`画出两个指标的原始数据点。
2.  **生成回归线**：利用上一步得到的系数`k`和`b`，为`x`值序列计算对应的回归线`y`值。
3.  **绘制回归线**：使用`plt.plot`将回归线添加到图中。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_26.png)

**核心代码**：
```python
import matplotlib.pyplot as plt

![](img/9a5316deb870c4feb6fc0471a5d82b9e_27.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_28.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_30.png)

# 1. 绘制原始数据散点图
plt.scatter(df[‘指标A‘], df[‘指标B‘], s=16)

# 2. 生成回归线的y值
# 使用np.poly1d根据系数创建多项式函数，方便计算
regression_func = np.poly1d(coefficients)
y_regression = regression_func(df[‘指标A‘])

![](img/9a5316deb870c4feb6fc0471a5d82b9e_32.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_33.png)

# 3. 绘制回归线
plt.plot(df[‘指标A‘], y_regression, color=‘red‘)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_34.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_35.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_36.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_37.png)

plt.show()
```
通过这张图，我们可以清晰地看到两个指标之间的线性关系以及回归模型的拟合情况。需要注意的是，在分析前对数据进行适当的预处理（例如，本案例中使用的是增长率数据）非常重要。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_38.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_40.png)

---

![](img/9a5316deb870c4feb6fc0471a5d82b9e_41.png)

## 计算相关系数

除了回归方程，衡量两个变量之间线性关系强度的常用指标是相关系数。计算相关系数在Pandas中非常简单。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_43.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_44.png)

以下是计算相关系数的步骤：

![](img/9a5316deb870c4feb6fc0471a5d82b9e_46.png)

直接使用Pandas DataFrame的`.corr()`方法，可以计算所有数值列两两之间的相关系数矩阵。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_48.png)

**核心代码**：
```python
# 计算两个指标之间的相关系数矩阵
correlation_matrix = df[[‘指标A‘, ‘指标B‘]].corr()
print(correlation_matrix)
```
矩阵对角线上的值均为1（变量与自身的完全相关），非对角线上的值就是两个指标之间的相关系数，其值在-1到1之间。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_49.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_50.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_52.png)

---

## 附加任务：分析相关系数随时间的变化

![](img/9a5316deb870c4feb6fc0471a5d82b9e_54.png)

为了更深入地分析，我们可以研究两个指标间的相关系数如何随时间变化。这需要通过滑动窗口来计算。

以下是实现滑动窗口相关系数的步骤：

![](img/9a5316deb870c4feb6fc0471a5d82b9e_56.png)

1.  **定义窗口**：选择一个窗口大小（例如250个数据点）。
2.  **滚动计算**：使用Pandas Series的`.rolling()`方法创建滑动窗口对象。
3.  **应用计算**：对每个窗口内的数据，计算其与另一个固定序列的相关系数。

**核心代码**：
```python
# 计算‘指标A‘与‘指标B‘在250天滑动窗口内的相关系数变化
rolling_corr = df[‘指标A‘].rolling(window=250).corr(df[‘指标B‘])

![](img/9a5316deb870c4feb6fc0471a5d82b9e_58.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_59.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_60.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_61.png)

# 可视化结果
rolling_corr.plot(figsize=(11, 6))
plt.show()
```
这张图展示了两个指标间关系的动态变化，比单一的静态相关系数包含了更多信息，有助于发现特定时间段内的关联模式。

---

![](img/9a5316deb870c4feb6fc0471a5d82b9e_63.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_64.png)

## 总结

![](img/9a5316deb870c4feb6fc0471a5d82b9e_65.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_66.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_67.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_68.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_70.png)

*   我们使用**NumPy的`polyfit`函数**构建了线性回归方程，并通过可视化验证了拟合效果。
*   我们使用**Pandas的`.corr()`方法**快速计算了指标间的静态相关系数。
*   最后，我们通过**滑动窗口技术**计算并可视化了相关系数随时间的变化趋势，进行了更动态的分析。

![](img/9a5316deb870c4feb6fc0471a5d82b9e_71.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_72.png)

![](img/9a5316deb870c4feb6fc0471a5d82b9e_73.png)

建议大家在课后对照代码实践一遍，理解每一步的作用，并尝试修改参数（如窗口大小、多项式阶数）来观察不同的结果。这些技能是进行金融时间序列分析和量化研究的基础。