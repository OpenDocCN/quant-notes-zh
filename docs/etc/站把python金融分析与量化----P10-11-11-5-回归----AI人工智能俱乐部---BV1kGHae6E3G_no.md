# Python金融量化：P10：回归方程与相关系数实例 📈

![](img/e5867d217fe158a260656201e5c0eba3_1.png)

![](img/e5867d217fe158a260656201e5c0eba3_2.png)

![](img/e5867d217fe158a260656201e5c0eba3_3.png)

在本节课中，我们将学习如何使用Python的NumPy和Pandas库，通过构建回归方程和计算相关系数来分析两个金融指标（如股票价格与恐慌指数）之间的关系。我们将从数据处理开始，逐步完成模型构建、结果可视化以及更深入的相关性动态分析。

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建一个线性回归方程来描述两个变量之间的关系。构建回归方程的方法很多，我们可以使用NumPy库中的`polyfit`函数来完成。

![](img/e5867d217fe158a260656201e5c0eba3_5.png)

`polyfit`函数可以帮助我们计算回归系数，其核心是拟合一个多项式。对于一元线性回归，我们拟合的是一次多项式，形式为：
**Y = kX + b**
其中，`k`是斜率，`b`是截距。

![](img/e5867d217fe158a260656201e5c0eba3_6.png)

![](img/e5867d217fe158a260656201e5c0eba3_7.png)

![](img/e5867d217fe158a260656201e5c0eba3_8.png)

![](img/e5867d217fe158a260656201e5c0eba3_9.png)

以下是使用`polyfit`函数的关键步骤：

![](img/e5867d217fe158a260656201e5c0eba3_10.png)

![](img/e5867d217fe158a260656201e5c0eba3_11.png)

![](img/e5867d217fe158a260656201e5c0eba3_13.png)

1.  准备数据：确保用于回归分析的两个数据序列（例如`close`和`vix`）没有缺失值。
2.  调用函数：`np.polyfit(x, y, deg)`，其中`deg`参数指定多项式的次数。对于线性回归，`deg=1`。
3.  处理数据：在拟合前，通常需要对数据进行预处理（如计算增长率），以使分析更有意义。

![](img/e5867d217fe158a260656201e5c0eba3_15.png)

```python
# 假设 df 是包含 ‘close‘ 和 ‘vix‘ 列的DataFrame
# 首先处理缺失值
df.dropna(inplace=True)

# 使用 polyfit 进行线性回归拟合
# deg=1 表示拟合一次多项式，即线性方程
coefficients = np.polyfit(df[‘close‘], df[‘vix‘], deg=1)
# coefficients 返回 [k, b]
k, b = coefficients
```

---

![](img/e5867d217fe158a260656201e5c0eba3_17.png)

![](img/e5867d217fe158a260656201e5c0eba3_18.png)

![](img/e5867d217fe158a260656201e5c0eba3_19.png)

![](img/e5867d217fe158a260656201e5c0eba3_20.png)

## 可视化回归结果

得到回归系数后，我们可以将回归直线与原始数据的散点图一起绘制出来，直观地观察拟合效果。

![](img/e5867d217fe158a260656201e5c0eba3_22.png)

![](img/e5867d217fe158a260656201e5c0eba3_23.png)

![](img/e5867d217fe158a260656201e5c0eba3_24.png)

以下是绘制散点图和回归线的步骤：

1.  使用`matplotlib`绘制原始数据的散点图。
2.  根据回归方程 **Y = kX + b**，计算出一系列预测的Y值。
3.  将预测的Y值与对应的X值绘制成一条直线，叠加在散点图上。

![](img/e5867d217fe158a260656201e5c0eba3_26.png)

![](img/e5867d217fe158a260656201e5c0eba3_27.png)

```python
import matplotlib.pyplot as plt

![](img/e5867d217fe158a260656201e5c0eba3_28.png)

![](img/e5867d217fe158a260656201e5c0eba3_30.png)

# 绘制散点图
plt.scatter(x=df[‘close‘], y=df[‘vix‘], s=16)
# 计算回归线对应的y值
y_pred = k * df[‘close‘] + b
# 绘制回归线，颜色设为红色
plt.plot(df[‘close‘], y_pred, color=‘red‘)
plt.show()
```

![](img/e5867d217fe158a260656201e5c0eba3_32.png)

---

![](img/e5867d217fe158a260656201e5c0eba3_33.png)

![](img/e5867d217fe158a260656201e5c0eba3_34.png)

![](img/e5867d217fe158a260656201e5c0eba3_35.png)

![](img/e5867d217fe158a260656201e5c0eba3_36.png)

## 计算相关系数

![](img/e5867d217fe158a260656201e5c0eba3_37.png)

![](img/e5867d217fe158a260656201e5c0eba3_38.png)

除了回归方程，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。Pandas提供了非常简便的方法来计算整个DataFrame中所有数值列两两之间的相关系数矩阵。

![](img/e5867d217fe158a260656201e5c0eba3_40.png)

![](img/e5867d217fe158a260656201e5c0eba3_41.png)

相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示变量与自身的完全相关），非对角线上的值表示对应两个变量之间的皮尔逊相关系数。

![](img/e5867d217fe158a260656201e5c0eba3_43.png)

```python
# 计算 DataFrame 中数值列之间的相关系数矩阵
correlation_matrix = df[[‘close‘, ‘vix‘]].corr()
print(correlation_matrix)
```

![](img/e5867d217fe158a260656201e5c0eba3_45.png)

![](img/e5867d217fe158a260656201e5c0eba3_46.png)

---

![](img/e5867d217fe158a260656201e5c0eba3_48.png)

## 附加任务：分析相关系数的动态变化

![](img/e5867d217fe158a260656201e5c0eba3_50.png)

在实际分析中，两个指标间的相关性可能随时间而变化。因此，我们可以通过计算滚动窗口内的相关系数来观察这种动态关系。

![](img/e5867d217fe158a260656201e5c0eba3_51.png)

![](img/e5867d217fe158a260656201e5c0eba3_52.png)

![](img/e5867d217fe158a260656201e5c0eba3_54.png)

这个任务要求我们：**随着年份（时间）的变化，计算两个指标之间相关系数的变化情况，并进行可视化展示。**

实现思路是使用滚动窗口。对于时间序列数据，我们设定一个固定大小的窗口（例如250个交易日），让这个窗口在时间轴上滑动。在每个窗口内，计算两个指标的相关系数。

![](img/e5867d217fe158a260656201e5c0eba3_56.png)

以下是实现代码：

```python
# 设置滚动窗口大小，例如250个数据点（代表大约一年的交易日）
window_size = 250
# 计算‘close‘相对于‘vix‘的滚动相关系数
rolling_corr = df[‘close‘].rolling(window=window_size).corr(df[‘vix‘])

![](img/e5867d217fe158a260656201e5c0eba3_58.png)

# 绘制滚动相关系数随时间变化的曲线
plt.figure(figsize=(11, 6))
rolling_corr.plot()
plt.title(‘Rolling Correlation between Close Price and VIX‘)
plt.show()
```

![](img/e5867d217fe158a260656201e5c0eba3_60.png)

![](img/e5867d217fe158a260656201e5c0eba3_61.png)

通过这张图，我们可以清晰地看到两个指标间的相关性是如何随着市场环境的变化而增强或减弱的。

![](img/e5867d217fe158a260656201e5c0eba3_62.png)

![](img/e5867d217fe158a260656201e5c0eba3_63.png)

---

![](img/e5867d217fe158a260656201e5c0eba3_65.png)

## 总结

![](img/e5867d217fe158a260656201e5c0eba3_66.png)

![](img/e5867d217fe158a260656201e5c0eba3_67.png)

![](img/e5867d217fe158a260656201e5c0eba3_68.png)

![](img/e5867d217fe158a260656201e5c0eba3_69.png)

本节课中我们一起学习了金融数据分析中的两个核心技能：

![](img/e5867d217fe158a260656201e5c0eba3_70.png)

1.  **构建与可视化回归方程**：我们使用NumPy的`polyfit`函数拟合线性模型，并通过Matplotlib将回归线可视化，从而量化并展示两个变量间的线性关系。
2.  **计算与分析相关系数**：我们使用Pandas快速计算了静态的相关系数矩阵，并进一步通过滚动窗口计算了动态的相关系数，揭示了变量间关系随时间变化的趋势。

![](img/e5867d217fe158a260656201e5c0eba3_72.png)

![](img/e5867d217fe158a260656201e5c0eba3_73.png)

![](img/e5867d217fe158a260656201e5c0eba3_74.png)

![](img/e5867d217fe158a260656201e5c0eba3_75.png)

这些方法是金融量化分析的基础，建议大家在理解代码的基础上，尝试修改参数（如窗口大小、多项式次数）或更换不同的指标数据进行练习，以加深理解。