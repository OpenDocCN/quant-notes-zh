# 金融数据分析：P11：回归方程与相关系数实例 📈

![](img/12f8a60b4c5cd0f9825a874efa94b805_1.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_3.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_5.png)

在本节课中，我们将学习如何使用Python工具包构建回归方程、计算相关系数，并实现数据的可视化。我们将通过一个金融时间序列的实例，演示从数据处理到模型构建与结果展示的完整流程。

---

## 构建回归方程

![](img/12f8a60b4c5cd0f9825a874efa94b805_7.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_9.png)

上一节我们介绍了数据处理的基本方法，本节中我们来看看如何构建回归方程。构建回归方程的方法很多，许多工具包都可以完成。这里我们选择使用NumPy的`polyfit`函数来计算回归系数。

![](img/12f8a60b4c5cd0f9825a874efa94b805_11.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_13.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_15.png)

`polyfit`函数可以帮助我们计算回归系数。它需要传入几个参数。

![](img/12f8a60b4c5cd0f9825a874efa94b805_17.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_19.png)

以下是调用`polyfit`函数的关键参数：
*   **x**: 自变量数据。
*   **y**: 因变量数据。
*   **deg**: 回归方程的阶数。`deg=1`表示方程包含X的一次项和零次项（即 `y = kx + b`）。`deg=2`则表示方程包含X的二次项、一次项和零次项。

![](img/12f8a60b4c5cd0f9825a874efa94b805_21.png)

本任务不涉及曲线拟合，因此将阶数`deg`指定为1即可。

![](img/12f8a60b4c5cd0f9825a874efa94b805_23.png)

在构建回归方程前，需要确保数据中没有空值。我们可以使用`dropna`方法处理数据。

![](img/12f8a60b4c5cd0f9825a874efa94b805_25.png)

```python
# 假设 df 是包含数据的DataFrame，x_col 和 y_col 是列名
x = df[x_col].dropna()
y = df[y_col].dropna()
coeff = np.polyfit(x, y, deg=1)
```
执行上述代码后，我们就得到了回归方程 `y = kx + b` 中的系数 `k` 和 `b`。

![](img/12f8a60b4c5cd0f9825a874efa94b805_27.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_29.png)

---

![](img/12f8a60b4c5cd0f9825a874efa94b805_31.png)

## 可视化回归方程

![](img/12f8a60b4c5cd0f9825a874efa94b805_33.png)

现在我们已经求解出回归方程，接下来可以在图中将结果画出来。

![](img/12f8a60b4c5cd0f9825a874efa94b805_35.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_37.png)

首先，我们需要画出原始数据的散点图。

```python
import matplotlib.pyplot as plt
plt.figure(figsize=(10, 6))
plt.scatter(x, y, s=16) # 绘制散点图
```

![](img/12f8a60b4c5cd0f9825a874efa94b805_39.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_41.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_43.png)

然后，我们需要在这张图上继续绘制回归直线。回归直线由一系列点构成，我们需要计算这些点的Y值。

![](img/12f8a60b4c5cd0f9825a874efa94b805_45.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_47.png)

利用之前求得的回归系数 `coeff`，我们可以根据X值计算出对应的预测Y值。

![](img/12f8a60b4c5cd0f9825a874efa94b805_49.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_51.png)

```python
# 计算回归直线上的y值
y_pred = np.polyval(coeff, x)
# 绘制回归直线
plt.plot(x, y_pred, color='red')
plt.show()
```

![](img/12f8a60b4c5cd0f9825a874efa94b805_53.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_55.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_57.png)

现在，我们就把回归方程在图中画出来了。结果一目了然，可以清晰地看到两个指标之间的关系。

![](img/12f8a60b4c5cd0f9825a874efa94b805_59.png)

需要注意的是，在构建回归方程或进行统计前，最好对数据进行适当的预处理，例如本实例中使用的就是增长率数据。

![](img/12f8a60b4c5cd0f9825a874efa94b805_61.png)

---

![](img/12f8a60b4c5cd0f9825a874efa94b805_63.png)

## 计算相关系数

![](img/12f8a60b4c5cd0f9825a874efa94b805_65.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_67.png)

除了回归方程，两个数值之间还可以计算相关系数。相关系数的计算和展示也非常简单。

如果数据集中只有两个指标，可以直接计算它们的相关系数矩阵。

![](img/12f8a60b4c5cd0f9825a874efa94b805_69.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_71.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_73.png)

```python
# 假设 df 是包含两列数据的DataFrame
correlation_matrix = df.corr()
print(correlation_matrix)
```

![](img/12f8a60b4c5cd0f9825a874efa94b805_75.png)

这个表格会显示相关系数。自己与自己的相关系数是1，非对角线上的值是两两指标间的相关系数，并且矩阵是对称的。

![](img/12f8a60b4c5cd0f9825a874efa94b805_77.png)

---

## 附加任务：随时间变化的相关系数

![](img/12f8a60b4c5cd0f9825a874efa94b805_79.png)

现在，我们增加一道附加题：分析随着年份变化，两个指标间相关系数的变化情况，并进行展示。

![](img/12f8a60b4c5cd0f9825a874efa94b805_81.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_83.png)

这需要考虑时间窗口。例如，我们可以设定一个窗口大小（如250天），然后在时间序列上滑动这个窗口。对于每一个窗口内的数据，计算两个指标在该时间段内的相关系数。

![](img/12f8a60b4c5cd0f9825a874efa94b805_85.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_87.png)

以下是实现代码的关键步骤：

![](img/12f8a60b4c5cd0f9825a874efa94b805_89.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_91.png)

```python
# 假设 df[‘col1‘] 和 df[‘col2‘] 是两个指标的时间序列数据
window_size = 250
# 计算滚动窗口相关系数
rolling_corr = df[‘col1‘].rolling(window=window_size).corr(df[‘col2‘])
# 可视化结果
rolling_corr.plot(figsize=(11, 6))
plt.show()
```

![](img/12f8a60b4c5cd0f9825a874efa94b805_93.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_95.png)

通过以上操作，我们就能计算出相关系数随时间的变化情况，并将其可视化。这比计算一个整体的相关系数能提供更多动态信息。

![](img/12f8a60b4c5cd0f9825a874efa94b805_97.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_99.png)

---

![](img/12f8a60b4c5cd0f9825a874efa94b805_101.png)

![](img/12f8a60b4c5cd0f9825a874efa94b805_103.png)

本节课中我们一起学习了针对金融时间序列的基本处理方法。我们利用Pandas和NumPy工具包进行了统计分析、模型计算与结果展示。建议大家在课后对照练习，理解代码的用途，并尝试修改参数（如窗口大小）来巩固所学知识。本节内容较为丰富，涵盖了时间序列分析的多个实用技巧。