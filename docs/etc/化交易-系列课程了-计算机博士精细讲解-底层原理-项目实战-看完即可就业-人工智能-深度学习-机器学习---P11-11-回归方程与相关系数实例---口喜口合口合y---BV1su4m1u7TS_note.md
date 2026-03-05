# Python金融分析：P11：回归方程与相关系数实例 📈

![](img/c9b5bab44062431449d819c693d1d6bf_1.png)

![](img/c9b5bab44062431449d819c693d1d6bf_3.png)

![](img/c9b5bab44062431449d819c693d1d6bf_5.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数。我们将通过一个金融时间序列的实例，演示如何利用NumPy和Pandas库进行数据处理、模型构建和结果可视化。

## 概述

上一节我们介绍了金融时间序列的基本处理方法。本节中，我们来看看如何具体构建回归方程来分析两个金融指标之间的关系，并计算它们的相关系数。我们将使用NumPy的`polyfit`函数来拟合回归线，并使用Pandas计算滑动窗口下的相关系数变化。

![](img/c9b5bab44062431449d819c693d1d6bf_7.png)

![](img/c9b5bab44062431449d819c693d1d6bf_9.png)

## 构建回归方程

![](img/c9b5bab44062431449d819c693d1d6bf_11.png)

![](img/c9b5bab44062431449d819c693d1d6bf_13.png)

![](img/c9b5bab44062431449d819c693d1d6bf_15.png)

构建回归方程的方法有很多。许多工具包都可以完成这个任务，这里我们选择使用NumPy库。NumPy中有一个`polyfit`函数，可以帮助我们计算回归模型的系数。

![](img/c9b5bab44062431449d819c693d1d6bf_17.png)

![](img/c9b5bab44062431449d819c693d1d6bf_19.png)

**代码：使用`np.polyfit`计算回归系数**
```python
# 假设我们有两个指标的数据：indicator_x 和 indicator_y
# 首先，需要处理数据中的空值
indicator_x = indicator_x.dropna()
indicator_y = indicator_y.dropna()

![](img/c9b5bab44062431449d819c693d1d6bf_21.png)

# 使用polyfit函数进行一元线性回归（deg=1）
# 函数返回系数，对于 deg=1，返回 [k, b]，对应方程 y = k*x + b
coefficients = np.polyfit(indicator_x, indicator_y, deg=1)
k, b = coefficients
```

![](img/c9b5bab44062431449d819c693d1d6bf_23.png)

`polyfit`函数需要传递几个参数：
1.  第一个参数（x）：自变量数据。
2.  第二个参数（y）：因变量数据。
3.  第三个参数（deg）：多项式的阶数。

![](img/c9b5bab44062431449d819c693d1d6bf_25.png)

参数`deg=1`表示我们构建的是一元线性回归方程，其形式为 **y = kx + b**。其中包含x的一次项（kx）和x的零次项（b）。理论上，我们可以指定更高的阶数（如2, 3）来拟合曲线，但在本例中，我们分析的关系近似线性，因此使用`deg=1`。

![](img/c9b5bab44062431449d819c693d1d6bf_27.png)

![](img/c9b5bab44062431449d819c693d1d6bf_29.png)

![](img/c9b5bab44062431449d819c693d1d6bf_31.png)

## 可视化回归结果

![](img/c9b5bab44062431449d819c693d1d6bf_33.png)

得到回归方程后，我们可以在散点图上将这条回归线画出来，使关系更直观。

![](img/c9b5bab44062431449d819c693d1d6bf_35.png)

以下是绘制散点图和回归线的步骤：

![](img/c9b5bab44062431449d819c693d1d6bf_37.png)

首先，绘制两个指标的散点图。

![](img/c9b5bab44062431449d819c693d1d6bf_39.png)

![](img/c9b5bab44062431449d819c693d1d6bf_41.png)

**代码：绘制散点图**
```python
import matplotlib.pyplot as plt

![](img/c9b5bab44062431449d819c693d1d6bf_43.png)

![](img/c9b5bab44062431449d819c693d1d6bf_45.png)

![](img/c9b5bab44062431449d819c693d1d6bf_47.png)

plt.figure(figsize=(16, 9))
plt.scatter(x=indicator_x, y=indicator_y, s=10) # s参数控制点的大小
plt.title(‘指标X与指标Y的散点图’)
plt.xlabel(‘指标X’)
plt.ylabel(‘指标Y’)
```

![](img/c9b5bab44062431449d819c693d1d6bf_49.png)

![](img/c9b5bab44062431449d819c693d1d6bf_51.png)

接着，在散点图的基础上绘制回归线。回归线由一系列点连接而成，我们需要根据回归方程计算每个x对应的y值。

![](img/c9b5bab44062431449d819c693d1d6bf_53.png)

![](img/c9b5bab44062431449d819c693d1d6bf_55.png)

**代码：计算并绘制回归线**
```python
# 计算回归线对应的y值
y_pred = k * indicator_x + b  # 使用求得的系数k和b

![](img/c9b5bab44062431449d819c693d1d6bf_57.png)

![](img/c9b5bab44062431449d819c693d1d6bf_59.png)

![](img/c9b5bab44062431449d819c693d1d6bf_61.png)

# 在之前的图上继续绘制回归线
plt.plot(indicator_x, y_pred, color=‘red’, linewidth=2) # 设置为红色线条
plt.show()
```

![](img/c9b5bab44062431449d819c693d1d6bf_63.png)

![](img/c9b5bab44062431449d819c693d1d6bf_65.png)

现在，回归方程的结果就一目了然地展示在图中了，我们可以清晰地看到两个指标之间的线性关系趋势。

## 计算相关系数

![](img/c9b5bab44062431449d819c693d1d6bf_67.png)

![](img/c9b5bab44062431449d819c693d1d6bf_69.png)

除了回归方程，分析两个变量之间关系强度的另一个重要指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/c9b5bab44062431449d819c693d1d6bf_71.png)

**代码：计算相关系数矩阵**
```python
# 假设df是一个包含多个指标的DataFrame
correlation_matrix = df.corr()
print(correlation_matrix)
```

![](img/c9b5bab44062431449d819c693d1d6bf_73.png)

![](img/c9b5bab44062431449d819c693d1d6bf_75.png)

相关系数矩阵是一个对称矩阵，对角线上的值均为1（表示自己与自己的完全相关）。非对角线上的值表示不同指标两两之间的相关系数。

![](img/c9b5bab44062431449d819c693d1d6bf_77.png)

![](img/c9b5bab44062431449d819c693d1d6bf_79.png)

## 附加任务：分析相关系数随时间的变化

![](img/c9b5bab44062431449d819c693d1d6bf_81.png)

在实际分析中，我们常常关心关系是否随时间稳定。下面我们增加一个附加任务：计算两个指标在滑动窗口下的相关系数，并观察其随时间的变化情况。

思路是：设定一个窗口（例如250天），在时间序列上滑动这个窗口，在每个窗口内计算两个指标的相关系数。

![](img/c9b5bab44062431449d819c693d1d6bf_83.png)

**代码：计算滑动窗口相关系数**
```python
# 设置窗口大小
window_size = 250

![](img/c9b5bab44062431449d819c693d1d6bf_85.png)

![](img/c9b5bab44062431449d819c693d1d6bf_87.png)

![](img/c9b5bab44062431449d819c693d1d6bf_89.png)

# 计算指标A与指标B在滑动窗口下的相关系数
rolling_corr = indicator_A.rolling(window=window_size).corr(other=indicator_B)

![](img/c9b5bab44062431449d819c693d1d6bf_91.png)

# 绘制相关系数随时间的变化图
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f‘指标A与指标B的{window_size}日滚动相关系数’)
plt.xlabel(‘时间’)
plt.ylabel(‘相关系数’)
plt.axhline(y=0, color=‘black’, linestyle=‘--’, linewidth=0.5) # 添加y=0的参考线
plt.show()
```

![](img/c9b5bab44062431449d819c693d1d6bf_93.png)

![](img/c9b5bab44062431449d819c693d1d6bf_95.png)

![](img/c9b5bab44062431449d819c693d1d6bf_97.png)

通过这张图，我们可以清晰地看到两个指标之间的关联强度如何随着时间推移而演变。

![](img/c9b5bab44062431449d819c693d1d6bf_99.png)

![](img/c9b5bab44062431449d819c693d1d6bf_101.png)

## 总结

![](img/c9b5bab44062431449d819c693d1d6bf_103.png)

![](img/c9b5bab44062431449d819c693d1d6bf_105.png)

![](img/c9b5bab44062431449d819c693d1d6bf_107.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：回归方程与相关系数。
1.  我们使用NumPy的`polyfit`函数构建了一元线性回归方程，形式为 **y = kx + b**，并通过可视化将分析结果直观呈现。
2.  我们使用Pandas便捷地计算了指标间的相关系数矩阵。
3.  最后，我们通过一个附加任务，探索了如何使用滑动窗口计算动态的相关系数，以分析关系随时间的变化情况。

![](img/c9b5bab44062431449d819c693d1d6bf_109.png)

![](img/c9b5bab44062431449d819c693d1d6bf_111.png)

建议大家在课后对照练习，理解代码的每一步在做什么。可以尝试修改参数（如回归方程的阶数`deg`、滑动窗口的`window_size`），观察结果的变化，从而加深对金融时间序列分析方法的理解。