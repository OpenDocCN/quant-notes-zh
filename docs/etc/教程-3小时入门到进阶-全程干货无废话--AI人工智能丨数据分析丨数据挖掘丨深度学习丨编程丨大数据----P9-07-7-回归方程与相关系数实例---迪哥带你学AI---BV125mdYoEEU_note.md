# Python金融分析与量化交易实战教程：P9：07-7-回归方程与相关系数实例 📈

![](img/c56ec3eb83a0fad4facbc8f6e1342143_1.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是分析两个金融指标之间关系的基础。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化来直观地展示分析结果。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_7.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_9.png)

构建回归方程的方法有很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非用于训练复杂的机器学习模型，而是直接计算线性回归的系数。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_11.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_13.png)

`polyfit`函数需要传入几个参数：
1.  第一个参数是自变量X的数据。
2.  第二个参数是因变量Y的数据。
3.  第三个参数`deg`指定回归方程的多项式阶数。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_15.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_17.png)

例如，对于方程 **Y = KX + B**，它包含X的一次项（`deg=1`）和常数项（X的零次项）。如果指定`deg=2`，则方程会包含X的二次项、一次项和常数项，可以拟合曲线。在本任务中，我们只分析线性关系，因此指定`deg=1`。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_19.png)

以下是构建回归方程的代码示例：

![](img/c56ec3eb83a0fad4facbc8f6e1342143_21.png)

```python
# 假设 df 是包含数据的DataFrame，且已处理空值
# df[‘指标A‘] 作为X， df[‘指标B‘] 作为Y
coefficients = np.polyfit(df[‘指标A‘], df[‘指标B‘], deg=1)
# coefficients 返回 [K, B]，即斜率和截距
```

![](img/c56ec3eb83a0fad4facbc8f6e1342143_23.png)

在计算前，必须确保数据中没有空值（NaN），否则会导致计算错误。可以使用`dropna`方法进行清理。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_25.png)

```python
df.dropna(inplace=True)
```

![](img/c56ec3eb83a0fad4facbc8f6e1342143_27.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_29.png)

---

![](img/c56ec3eb83a0fad4facbc8f6e1342143_31.png)

## 可视化回归方程

![](img/c56ec3eb83a0fad4facbc8f6e1342143_33.png)

得到回归系数后，我们可以将回归线画在散点图上，直观地展示两个指标的关系。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_35.png)

以下是绘制散点图和回归线的步骤：

![](img/c56ec3eb83a0fad4facbc8f6e1342143_37.png)

首先，绘制两个指标的散点图。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_39.png)

```python
plt.figure(figsize=(16, 9))
plt.scatter(x=df[‘指标A‘], y=df[‘指标B‘])
```

![](img/c56ec3eb83a0fad4facbc8f6e1342143_41.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_43.png)

然后，根据回归方程计算对应的Y值，并绘制回归线。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_45.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_47.png)

```python
# 计算回归线对应的Y值
k, b = coefficients
y_fit = k * df[‘指标A‘] + b

![](img/c56ec3eb83a0fad4facbc8f6e1342143_49.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_51.png)

# 绘制红色的回归线
plt.plot(df[‘指标A‘], y_fit, color=‘red‘)
plt.show()
```

![](img/c56ec3eb83a0fad4facbc8f6e1342143_53.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_55.png)

通过图表，可以清晰地看到指标间的线性关系趋势。需要注意的是，如果数据是增长率等经过处理的值，在解释回归结果时应考虑数据的实际含义。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_57.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_59.png)

---

![](img/c56ec3eb83a0fad4facbc8f6e1342143_61.png)

## 计算相关系数

![](img/c56ec3eb83a0fad4facbc8f6e1342143_63.png)

除了回归方程，衡量两个变量之间线性关系强度的常用指标是相关系数。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_65.png)

在Pandas中，计算两个序列的相关系数非常简单。以下是计算并展示相关系数矩阵的方法：

![](img/c56ec3eb83a0fad4facbc8f6e1342143_67.png)

```python
# 计算两个指标间的相关系数
correlation_matrix = df[[‘指标A‘, ‘指标B‘]].corr()
print(correlation_matrix)
```

![](img/c56ec3eb83a0fad4facbc8f6e1342143_69.png)

结果是一个对称矩阵，对角线上的值为1（变量与自身的完全相关），非对角线上的值就是两个指标之间的皮尔逊相关系数。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_71.png)

---

![](img/c56ec3eb83a0fad4facbc8f6e1342143_73.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_75.png)

## 附加任务：分析相关系数随时间的变化

![](img/c56ec3eb83a0fad4facbc8f6e1342143_77.png)

为了更深入地分析，我们可以观察两个指标间的相关系数如何随时间变化。这需要通过滑动窗口来计算。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_79.png)

思路是：定义一个固定大小的窗口（例如250个数据点），让这个窗口在时间序列上滑动。在每个窗口内，计算两个指标的相关系数。这样就能得到一条随时间变化的相关系数曲线。

以下是实现代码：

![](img/c56ec3eb83a0fad4facbc8f6e1342143_81.png)

```python
# 定义窗口大小
window_size = 250

![](img/c56ec3eb83a0fad4facbc8f6e1342143_83.png)

# 计算指标A与指标B在滑动窗口内的相关系数
rolling_corr = df[‘指标A‘].rolling(window=window_size).corr(df[‘指标B‘])

![](img/c56ec3eb83a0fad4facbc8f6e1342143_85.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_87.png)

# 绘制相关系数随时间变化的曲线
plt.figure(figsize=(11, 6))
rolling_corr.plot()
plt.show()
```

![](img/c56ec3eb83a0fad4facbc8f6e1342143_89.png)

这张图展示了两个指标间关系的动态变化，比单一的静态相关系数包含了更多信息。你可以尝试调整窗口大小，观察不同时间尺度下关系的变化。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_91.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_93.png)

---

![](img/c56ec3eb83a0fad4facbc8f6e1342143_95.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_97.png)

## 总结

![](img/c56ec3eb83a0fad4facbc8f6e1342143_99.png)

本节课中我们一起学习了金融数据分析中的两个核心技能：
1.  **构建与可视化回归方程**：使用`np.polyfit`计算线性关系，并通过绘图展示。
2.  **计算与分析相关系数**：包括静态的总体相关系数和动态的滚动窗口相关系数，以揭示关系随时间的变化。

![](img/c56ec3eb83a0fad4facbc8f6e1342143_101.png)

![](img/c56ec3eb83a0fad4facbc8f6e1342143_103.png)

这些是理解指标间关联性的基础。建议你在理解代码逻辑后，动手修改参数（如窗口大小、分析不同的指标对）进行练习，以加深对时间序列分析方法的掌握。