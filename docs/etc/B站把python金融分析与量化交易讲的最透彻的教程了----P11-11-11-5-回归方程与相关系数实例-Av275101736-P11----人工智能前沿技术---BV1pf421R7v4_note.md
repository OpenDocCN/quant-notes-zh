# Python金融量化与股票交易：P11：回归方程与相关系数实例 📈

![](img/5020e1b6abaea2c0d9770db14ff1196c_1.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，以分析两个金融指标（如标普500指数与恐慌指数VIX）之间的关系。我们将使用NumPy和Pandas库来完成这些任务，并通过可视化展示结果。

![](img/5020e1b6abaea2c0d9770db14ff1196c_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。

![](img/5020e1b6abaea2c0d9770db14ff1196c_7.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_9.png)

构建回归方程的方法有很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。该函数并非训练一个复杂的模型，而是直接计算线性回归的系数。

![](img/5020e1b6abaea2c0d9770db14ff1196c_11.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_13.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_15.png)

回归方程的基本形式是 **Y = KX + B**，其中K是斜率，B是截距。`polyfit`函数允许我们指定多项式的阶数（degree）。阶数为1表示我们只使用X的一次项和零次项（即常数项），拟合一条直线。如果指定阶数为2，则会包含X的二次项，用于拟合曲线。

![](img/5020e1b6abaea2c0d9770db14ff1196c_17.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_19.png)

以下是构建回归方程的关键步骤：

![](img/5020e1b6abaea2c0d9770db14ff1196c_21.png)

1.  准备数据：确保用于计算的数据中没有空值（NaN），否则可能导致计算错误。
2.  调用`np.polyfit`函数，传入X值、Y值以及指定的多项式阶数（此处为1）。
3.  函数将返回一个包含系数（K, B）的数组。

![](img/5020e1b6abaea2c0d9770db14ff1196c_23.png)

```python
# 假设 df[‘SPX’] 是X， df[‘VIX’] 是Y
# 首先处理数据中的空值
df.dropna(inplace=True)

# 使用np.polyfit计算回归系数，阶数为1（线性回归）
coefficients = np.polyfit(df[‘SPX’], df[‘VIX’], deg=1)
# coefficients 现在包含 [K, B]
```

![](img/5020e1b6abaea2c0d9770db14ff1196c_25.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_27.png)

---

![](img/5020e1b6abaea2c0d9770db14ff1196c_29.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_31.png)

## 可视化回归方程

得到回归系数后，我们可以将回归线绘制在散点图上，直观地展示两个指标的关系。

![](img/5020e1b6abaea2c0d9770db14ff1196c_33.png)

以下是绘制散点图和回归线的步骤：

![](img/5020e1b6abaea2c0d9770db14ff1196c_35.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_37.png)

1.  首先，使用Matplotlib绘制原始数据的散点图。
2.  然后，根据回归方程 **Y = KX + B**，为X序列中的每个点计算对应的预测Y值。
3.  最后，将计算出的（X, Y_pred）点连接成线，覆盖在散点图上。

```python
import matplotlib.pyplot as plt

![](img/5020e1b6abaea2c0d9770db14ff1196c_39.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_41.png)

# 绘制散点图
plt.scatter(x=df[‘SPX’], y=df[‘VIX’], s=16)

![](img/5020e1b6abaea2c0d9770db14ff1196c_43.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_45.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_47.png)

# 计算回归线对应的Y值
K, B = coefficients
y_pred = K * df[‘SPX’] + B

![](img/5020e1b6abaea2c0d9770db14ff1196c_49.png)

# 绘制回归线，设置为红色以便区分
plt.plot(df[‘SPX’], y_pred, color=‘red’)

![](img/5020e1b6abaea2c0d9770db14ff1196c_51.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_53.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_55.png)

plt.show()
```

![](img/5020e1b6abaea2c0d9770db14ff1196c_57.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_59.png)

通过可视化，我们可以清晰地看到标普500指数（SPX）与恐慌指数（VIX）之间存在的负相关关系。

![](img/5020e1b6abaea2c0d9770db14ff1196c_61.png)

---

![](img/5020e1b6abaea2c0d9770db14ff1196c_63.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_65.png)

## 计算相关系数

除了回归分析，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/5020e1b6abaea2c0d9770db14ff1196c_67.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_69.png)

我们可以直接调用DataFrame的`.corr()`方法，它会计算所有列两两之间的皮尔逊相关系数，并返回一个相关系数矩阵。矩阵的对角线是每个变量与自身的相关系数（总是1），非对角线上的值是对称的。

![](img/5020e1b6abaea2c0d9770db14ff1196c_71.png)

```python
# 计算SPX和VIX两列数据的相关系数矩阵
correlation_matrix = df[[‘SPX‘， ’VIX‘]].corr()
print(correlation_matrix)
```

![](img/5020e1b6abaea2c0d9770db14ff1196c_73.png)

---

![](img/5020e1b6abaea2c0d9770db14ff1196c_75.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_77.png)

## 附加任务：计算滚动相关系数

![](img/5020e1b6abaea2c0d9770db14ff1196c_79.png)

为了更深入地分析，我们可以研究相关系数如何随时间变化。这可以通过计算滚动窗口（滑动窗口）相关系数来实现。

![](img/5020e1b6abaea2c0d9770db14ff1196c_81.png)

以下是计算滚动相关系数的思路：

![](img/5020e1b6abaea2c0d9770db14ff1196c_83.png)

1.  定义一个窗口大小（例如250天，代表一年的交易天数）。
2.  在时间序列上滑动这个窗口。
3.  对于每个窗口内的数据，计算两个指标（如SPX和VIX）的相关系数。
4.  这样，我们就得到了一个随时间变化的相关系数序列。

![](img/5020e1b6abaea2c0d9770db14ff1196c_85.png)

```python
# 设置滚动窗口大小为250
window_size = 250

![](img/5020e1b6abaea2c0d9770db14ff1196c_87.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_89.png)

# 计算SPX与VIX之间的滚动相关系数
rolling_corr = df[‘SPX’].rolling(window=window_size).corr(df[‘VIX’])

![](img/5020e1b6abaea2c0d9770db14ff1196c_91.png)

# 绘制滚动相关系数随时间的变化
rolling_corr.plot(figsize=(11， 6))
plt.show()
```

![](img/5020e1b6abaea2c0d9770db14ff1196c_93.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_95.png)

这个图表展示了SPX与VIX之间关系的动态变化，比单一的静态相关系数包含了更多信息。

![](img/5020e1b6abaea2c0d9770db14ff1196c_97.png)

---

![](img/5020e1b6abaea2c0d9770db14ff1196c_99.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_101.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_103.png)

## 总结

![](img/5020e1b6abaea2c0d9770db14ff1196c_105.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_107.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。

![](img/5020e1b6abaea2c0d9770db14ff1196c_109.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_111.png)

我们首先使用NumPy的`polyfit`函数构建了线性回归方程，并通过可视化将回归线叠加在散点图上，直观展示了变量间的关系。接着，我们使用Pandas快速计算了变量间的静态相关系数。最后，我们通过一个附加任务，探索了如何计算并可视化滚动相关系数，以观察关系强度随时间的变化趋势。

![](img/5020e1b6abaea2c0d9770db14ff1196c_113.png)

![](img/5020e1b6abaea2c0d9770db14ff1196c_115.png)

这些方法是金融时间序列分析的基础，掌握它们能帮助我们更好地理解市场指标间的相互作用。建议大家在课后亲自动手实践代码，并尝试修改参数（如窗口大小），以加深理解。