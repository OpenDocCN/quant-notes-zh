# 量化交易教程：P11：回归方程与相关系数实例 📈

![](img/6ceb24cf31ba995b3f9fe57746f5f947_1.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_2.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_3.png)

在本节课中，我们将学习如何使用Python的NumPy和Pandas库，对金融时间序列数据进行回归分析和相关系数计算。我们将通过一个实例，构建两个指标之间的回归方程，并将其可视化，同时探讨如何计算并分析相关系数随时间的变化情况。

---

## 构建回归方程

上一节我们介绍了数据处理的基础，本节中我们来看看如何构建两个金融指标之间的回归方程。构建回归方程的方法有很多，可以使用多种工具包。这里我们使用NumPy库来完成。

NumPy库中的`polyfit`函数可以帮助我们计算回归系数，即拟合一个回归模型。其核心是求解方程 **Y = KX + B** 中的系数K和B。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_5.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_6.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_7.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_8.png)

以下是使用`polyfit`函数的关键步骤：

![](img/6ceb24cf31ba995b3f9fe57746f5f947_9.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_10.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_11.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_12.png)

1.  传入需要计算关系的两个指标数据。
2.  指定回归方程的阶数（degree）。阶数为1表示我们拟合的是一次线性方程（Y = KX + B）。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_14.png)

在计算之前，需要确保数据中没有空值（NaN），否则可能导致计算错误。我们可以使用Pandas的`dropna`方法清理数据。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_16.png)

```python
# 假设 df[‘指标A‘] 和 df[‘指标B‘] 是我们的数据
# 清理空值
df.dropna(inplace=True)

# 使用 numpy 的 polyfit 进行线性拟合，degree=1 表示一次线性回归
coefficients = np.polyfit(df[‘指标A‘], df[‘指标B‘], deg=1)
# coefficients 返回一个数组，其中第一个元素是斜率 K，第二个是截距 B
K, B = coefficients[0], coefficients[1]
```

![](img/6ceb24cf31ba995b3f9fe57746f5f947_18.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_19.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_20.png)

---

![](img/6ceb24cf31ba995b3f9fe57746f5f947_21.png)

## 可视化回归方程

![](img/6ceb24cf31ba995b3f9fe57746f5f947_23.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_24.png)

得到回归系数后，我们可以将回归直线与原始数据的散点图一同绘制出来，以便直观地观察两者关系。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_25.png)

绘制过程分为两步：
1.  绘制原始数据的散点图。
2.  根据回归方程计算对应的Y值，并绘制出回归直线。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_27.png)

以下是关键代码示例：

![](img/6ceb24cf31ba995b3f9fe57746f5f947_28.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_29.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_31.png)

```python
import matplotlib.pyplot as plt

# 1. 绘制散点图
plt.scatter(x=df[‘指标A‘], y=df[‘指标B‘], s=16)
plt.title(‘指标关系散点图与回归线‘)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_33.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_34.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_35.png)

# 2. 绘制回归线
# 生成一系列X值（这里直接用原始数据的X值范围）
x_line = df[‘指标A‘]
# 根据回归方程 Y = K*X + B 计算对应的Y值
y_line = K * x_line + B
# 绘制红色回归线
plt.plot(x_line, y_line, color=‘red‘)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_36.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_37.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_38.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_39.png)

plt.show()
```

![](img/6ceb24cf31ba995b3f9fe57746f5f947_41.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_42.png)

---

## 计算相关系数

![](img/6ceb24cf31ba995b3f9fe57746f5f947_44.png)

除了回归分析，衡量两个变量之间线性关系强度的常用指标是相关系数。在Pandas中，这可以非常简便地完成。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_46.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_47.png)

我们可以直接调用DataFrame的`corr()`方法来计算所有列之间的相关系数矩阵。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_49.png)

```python
# 计算两个指标之间的相关系数矩阵
correlation_matrix = df[[‘指标A‘, ‘指标B‘]].corr()
print(correlation_matrix)
```
对角线上的值均为1（变量与自身的完全相关），非对角线上的值就是两个指标之间的相关系数，其值在-1到1之间。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_51.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_52.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_53.png)

---

![](img/6ceb24cf31ba995b3f9fe57746f5f947_55.png)

## 附加任务：分析相关系数随时间的变化

为了更深入地分析，我们可以观察两个指标之间的相关系数如何随时间窗口滑动而变化。这能帮助我们理解两者关系的动态稳定性。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_57.png)

思路是定义一个固定大小的滑动窗口（例如250个数据点），在时间序列上滑动，计算每个窗口内两个指标的相关系数。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_59.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_60.png)

以下是实现代码：

![](img/6ceb24cf31ba995b3f9fe57746f5f947_62.png)

```python
# 设置窗口大小
window_size = 250

![](img/6ceb24cf31ba995b3f9fe57746f5f947_64.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_65.png)

# 计算指标A相对于指标B的滚动相关系数
rolling_corr = df[‘指标A‘].rolling(window=window_size).corr(df[‘指标B‘])

![](img/6ceb24cf31ba995b3f9fe57746f5f947_66.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_67.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f‘{window_size}日滚动相关系数变化‘)
plt.ylabel(‘相关系数‘)
plt.grid(True)
plt.show()
```
通过这张图，我们可以清晰地看到两个指标间的关联强度在不同时期是如何波动的。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_69.png)

---

![](img/6ceb24cf31ba995b3f9fe57746f5f947_70.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_71.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_72.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_73.png)

## 总结

![](img/6ceb24cf31ba995b3f9fe57746f5f947_74.png)

本节课中我们一起学习了针对金融时间序列的两种基本分析方法：
1.  **回归分析**：我们使用NumPy的`polyfit`函数构建了线性回归方程 **Y = KX + B**，并学会了如何将结果可视化。
2.  **相关分析**：我们使用Pandas计算了简单的相关系数，并进一步通过滚动窗口计算，分析了相关系数随时间的变化趋势。

![](img/6ceb24cf31ba995b3f9fe57746f5f947_76.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_77.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_78.png)

![](img/6ceb24cf31ba995b3f9fe57746f5f947_79.png)

这些方法是金融数据分析和量化策略研究的基础。建议大家在理解代码的基础上，尝试修改参数（如窗口大小、回归阶数）或更换不同的指标数据进行练习，以加深理解。