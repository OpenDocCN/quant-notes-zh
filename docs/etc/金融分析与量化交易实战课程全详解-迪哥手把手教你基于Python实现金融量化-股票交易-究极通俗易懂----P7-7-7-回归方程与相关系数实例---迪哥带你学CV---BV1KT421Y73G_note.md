# Python金融分析与量化交易实战课程：P7：7.7-回归方程与相关系数实例 📈

![](img/4b166e1f2da292d888b5bf9d37c2f824_1.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_3.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_5.png)

在本节课中，我们将学习如何使用Python构建回归方程和计算相关系数，这是分析两个金融指标之间关系的基础。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化来直观地展示分析结果。

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/4b166e1f2da292d888b5bf9d37c2f824_7.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_9.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_11.png)

构建回归方程的方法有很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/4b166e1f2da292d888b5bf9d37c2f824_13.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_15.png)

以下是使用`polyfit`函数的关键步骤：

![](img/4b166e1f2da292d888b5bf9d37c2f824_17.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_19.png)

1.  **准备数据**：需要传入两个指标的数据，分别作为自变量X和因变量Y。
2.  **指定阶数**：通过`deg`参数指定回归方程的阶数。`deg=1`表示构建一个线性方程 `Y = KX + B`，其中包含X的一次项和常数项。如果指定`deg=2`，则会构建包含X二次项的曲线方程。
3.  **处理数据**：在计算前，需要确保数据中没有空值（NaN），否则可能导致计算错误（如求逆矩阵失败）。我们可以使用Pandas的`dropna`方法清理数据。

![](img/4b166e1f2da292d888b5bf9d37c2f824_21.png)

核心代码公式如下：
```python
# 清理数据中的空值
data.dropna(inplace=True)

![](img/4b166e1f2da292d888b5bf9d37c2f824_23.png)

# 使用 numpy.polyfit 计算线性回归系数 (deg=1 表示一次线性回归)
# 返回的系数中，第一个是斜率K，第二个是截距B
K, B = np.polyfit(X, Y, deg=1)
```

得到系数K和B后，我们就得到了回归方程 `Y = K * X + B`。

![](img/4b166e1f2da292d888b5bf9d37c2f824_25.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_27.png)

---

![](img/4b166e1f2da292d888b5bf9d37c2f824_29.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_31.png)

## 可视化回归方程

在计算出回归方程后，我们可以将其绘制在散点图上，以便直观地观察变量间的关系。

![](img/4b166e1f2da292d888b5bf9d37c2f824_33.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_35.png)

以下是绘制回归线的步骤：

![](img/4b166e1f2da292d888b5bf9d37c2f824_37.png)

1.  **绘制散点图**：首先使用Matplotlib绘制两个指标的原始数据散点图。
2.  **生成回归线**：根据回归方程 `Y = K * X + B`，利用计算出的系数K和B，为每个X值计算对应的预测Y值。
3.  **绘制回归线**：将计算出的(X, Y_pred)点连接起来，在散点图上绘制出回归直线。

核心代码如下：
```python
import matplotlib.pyplot as plt

![](img/4b166e1f2da292d888b5bf9d37c2f824_39.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_41.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_43.png)

# 1. 绘制原始数据散点图
plt.figure(figsize=(16, 9))
plt.scatter(X, Y, label=‘原始数据’)

![](img/4b166e1f2da292d888b5bf9d37c2f824_45.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_47.png)

# 2. 根据回归方程计算预测值
Y_pred = K * X + B

![](img/4b166e1f2da292d888b5bf9d37c2f824_49.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_51.png)

# 3. 绘制回归直线
plt.plot(X, Y_pred, color=‘red’, linewidth=2, label=‘回归直线’)

![](img/4b166e1f2da292d888b5bf9d37c2f824_53.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_55.png)

plt.legend()
plt.show()
```
通过可视化，我们可以清晰地看到两个指标之间的线性趋势。需要注意的是，本例中使用的数据是增长率，在分析时应注意数据的预处理和实际含义。

![](img/4b166e1f2da292d888b5bf9d37c2f824_57.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_59.png)

---

![](img/4b166e1f2da292d888b5bf9d37c2f824_61.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_63.png)

## 计算相关系数

除了回归方程，相关系数是衡量两个变量线性关系强度和方向的另一个重要指标。

![](img/4b166e1f2da292d888b5bf9d37c2f824_65.png)

计算相关系数非常简单，在Pandas中可以直接调用`.corr()`方法。对于一个包含多个指标的数据框，此方法会计算所有两两指标之间的相关系数矩阵。

![](img/4b166e1f2da292d888b5bf9d37c2f824_67.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_69.png)

以下是计算相关系数的代码：
```python
# 计算两个序列的相关系数
correlation = X.corr(Y)
print(f“相关系数为：{correlation}”)

# 或者，如果数据在DataFrame中，计算整个相关系数矩阵
corr_matrix = data[[‘指标A’, ‘指标B’]].corr()
print(corr_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自身完全相关），非对角线上的值表示两两指标间的相关系数。

![](img/4b166e1f2da292d888b5bf9d37c2f824_71.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_73.png)

---

![](img/4b166e1f2da292d888b5bf9d37c2f824_75.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_77.png)

## 附加任务：滑动窗口相关系数分析

![](img/4b166e1f2da292d888b5bf9d37c2f824_79.png)

为了更深入地分析，我们可以研究相关系数随时间的变化情况。这可以通过计算滑动窗口内的相关系数来实现。

具体思路如下：
1.  **定义窗口**：选择一个固定大小的窗口（例如250个数据点）。
2.  **滑动计算**：让这个窗口在时间序列上滑动。
3.  **窗口内计算**：对于每一个窗口位置，计算窗口内两个指标数据的相关系数。
4.  **结果序列**：将所有窗口计算出的相关系数按时间顺序排列，就得到了相关系数随时间变化的序列。

以下是实现代码：
```python
# 设置滑动窗口大小
window_size = 250

![](img/4b166e1f2da292d888b5bf9d37c2f824_81.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_83.png)

# 计算指标A与指标B在滑动窗口内的相关系数
rolling_corr = 指标A.rolling(window=window_size).corr(其他指标)

![](img/4b166e1f2da292d888b5bf9d37c2f824_85.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_87.png)

# 绘制相关系数随时间的变化图
rolling_corr.plot(figsize=(16, 9))
plt.title(‘滑动窗口相关系数变化图’)
plt.show()
```
通过这个分析，我们可以观察两个指标间的关联性在不同时期是增强、减弱还是保持稳定，这比单一的全局相关系数包含了更丰富的信息。

![](img/4b166e1f2da292d888b5bf9d37c2f824_89.png)

---

![](img/4b166e1f2da292d888b5bf9d37c2f824_91.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_93.png)

## 总结

![](img/4b166e1f2da292d888b5bf9d37c2f824_95.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_97.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/4b166e1f2da292d888b5bf9d37c2f824_99.png)

*   我们使用NumPy的`polyfit`函数构建了线性回归方程，并学会了如何将其可视化。
*   我们掌握了使用Pandas快速计算两变量间相关系数的方法。
*   最后，我们通过一个附加的滑动窗口分析任务，探索了相关系数如何随时间动态变化。

![](img/4b166e1f2da292d888b5bf9d37c2f824_101.png)

![](img/4b166e1f2da292d888b5bf9d37c2f824_103.png)

这些方法是量化分析的基础，建议大家在理解代码逻辑后，亲自动手练习，例如尝试改变窗口大小、使用不同的指标，以加深对概念和工具的理解。