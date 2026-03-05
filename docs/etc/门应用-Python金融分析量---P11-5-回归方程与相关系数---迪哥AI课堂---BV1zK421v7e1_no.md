# Python金融分析量化交易：P11：回归方程与相关系数实例 📈

![](img/6aa5917acdece9730f9b99e4ab1db5cd_1.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是分析两个金融指标之间关系的基础。我们将通过具体的代码实例，展示如何从数据处理到结果可视化的完整流程。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_5.png)

---

## 构建回归方程

上一节我们介绍了金融时间序列的基本处理方法，本节中我们来看看如何量化两个指标之间的关系。构建回归方程的方法有很多，我们可以使用多种工具包来完成。这里我们选择使用NumPy库来实现。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_7.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_9.png)

NumPy库中的`polyfit`函数可以帮助我们计算回归模型的系数。它并不是训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_11.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_13.png)

在计算回归方程之前，需要明确几个参数：
1.  需要分析的两个指标数据。
2.  回归方程的阶数（`deg`参数）。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_15.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_17.png)

例如，对于一元线性回归 `Y = kX + b`，它包含X的一次项和零次项（常数项）。`deg=1`表示我们只拟合一次项和常数项。如果将`deg`指定为2，则方程会包含X的二次项、一次项和常数项，可以拟合曲线关系。在本任务中，我们分析的是线性关系，因此指定`deg=1`即可。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_19.png)

以下是构建回归方程的核心代码步骤：

![](img/6aa5917acdece9730f9b99e4ab1db5cd_21.png)

```python
# 假设我们有两个指标的数据：X 和 Y
# 首先，确保数据中没有空值，否则计算会出错
X = X.dropna()
Y = Y.dropna()

![](img/6aa5917acdece9730f9b99e4ab1db5cd_23.png)

# 使用 numpy.polyfit 计算线性回归系数 (k, b)
coefficients = np.polyfit(X, Y, deg=1)
k, b = coefficients
```

执行上述代码后，我们就得到了回归方程的系数k和b，即解出了方程 `Y = kX + b`。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_25.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_27.png)

---

![](img/6aa5917acdece9730f9b99e4ab1db5cd_29.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_31.png)

## 可视化回归结果

得到回归方程后，我们可以将其可视化，以便更直观地观察两个指标之间的关系。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_33.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_35.png)

以下是绘制散点图和回归线的步骤：
1.  首先，绘制两个指标的散点图。
2.  然后，根据我们计算出的回归方程，生成对应的预测Y值，并将这条回归线画在散点图上。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_37.png)

```python
import matplotlib.pyplot as plt

# 1. 绘制散点图
plt.scatter(x=X, y=Y, s=16) # s参数控制点的大小

![](img/6aa5917acdece9730f9b99e4ab1db5cd_39.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_41.png)

# 2. 计算回归线对应的Y值
Y_pred = k * X + b  # 利用求得的系数k和b计算预测值

![](img/6aa5917acdece9730f9b99e4ab1db5cd_43.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_45.png)

# 3. 在同一张图上绘制回归线
plt.plot(X, Y_pred, color='red') # 将线设置为红色以便区分

![](img/6aa5917acdece9730f9b99e4ab1db5cd_47.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_49.png)

plt.show()
```

![](img/6aa5917acdece9730f9b99e4ab1db5cd_51.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_53.png)

通过这张图，我们可以一目了然地看到两个指标之间是正相关、负相关还是无明显线性关系。需要注意的是，在分析前对数据进行适当的预处理（例如，本案例中使用的是增长率数据而非原始价格）非常重要，这直接影响分析结论的合理性。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_55.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_57.png)

---

![](img/6aa5917acdece9730f9b99e4ab1db5cd_59.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_61.png)

## 计算与可视化相关系数

![](img/6aa5917acdece9730f9b99e4ab1db5cd_63.png)

除了回归方程，相关系数是衡量两个变量线性关系强度的另一个重要指标。它的计算和展示在Pandas中非常简单。

以下是计算并展示两个指标相关系数矩阵的代码：

![](img/6aa5917acdece9730f9b99e4ab1db5cd_65.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_67.png)

```python
# 将两个指标数据组合成DataFrame
data = pd.DataFrame({'指标A': X, '指标B': Y})

![](img/6aa5917acdece9730f9b99e4ab1db5cd_69.png)

# 计算相关系数矩阵
correlation_matrix = data.corr()
print(correlation_matrix)
```

相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自身完全相关），非对角线上的值表示两个指标间的相关系数，其值在-1到1之间。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_71.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_73.png)

---

![](img/6aa5917acdece9730f9b99e4ab1db5cd_75.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_77.png)

## 进阶分析：滑动窗口相关系数

为了更深入地分析，我们可以研究相关系数如何随时间变化。这可以通过计算滑动窗口内的相关系数来实现。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_79.png)

具体思路是：
1.  设定一个窗口大小（例如250天）。
2.  在时间序列上滑动这个窗口。
3.  在每个窗口内，计算两个指标的相关系数。
4.  最终得到一条随时间变化的相关系数曲线。

以下是实现代码：

![](img/6aa5917acdece9730f9b99e4ab1db5cd_81.png)

```python
# 设置窗口大小
window_size = 250

![](img/6aa5917acdece9730f9b99e4ab1db5cd_83.png)

# 计算指标A相对于指标B的滑动窗口相关系数
rolling_corr = X.rolling(window=window_size).corr(Y)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_85.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_87.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_89.png)

# 绘制随时间变化的相关系数曲线
rolling_corr.plot(figsize=(11, 6))
plt.show()
```

这张图展示了两个指标间关系的动态变化，比单一的静态相关系数包含了更多信息。我们可以尝试修改窗口大小（例如改为10天或50天），观察结果如何变化，从而更好地理解数据。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_91.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_93.png)

---

![](img/6aa5917acdece9730f9b99e4ab1db5cd_95.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_97.png)

## 总结

![](img/6aa5917acdece9730f9b99e4ab1db5cd_99.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_101.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_103.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_105.png)

我们掌握了以下内容：
*   使用 **`np.polyfit`** 构建一元线性回归方程，其形式为 `Y = kX + b`。
*   通过散点图和回归线对关系进行可视化分析。
*   使用 **`.corr()`** 方法快速计算指标间的相关系数矩阵。
*   利用 **`.rolling().corr()`** 方法计算滑动窗口相关系数，以分析关系强度的动态变化。

![](img/6aa5917acdece9730f9b99e4ab1db5cd_107.png)

![](img/6aa5917acdece9730f9b99e4ab1db5cd_109.png)

这些方法是量化分析的基础，建议大家在理解代码逻辑后，亲自动手练习，并通过修改参数（如回归阶数、窗口大小）来加深理解。