# Python金融时间序列分析与量化交易实战教程：P8：7.回归方程与相关系数实例 📈

![](img/5e32cb08ebc21dc6b3c08528d68a8937_1.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_3.png)

在本节课中，我们将学习如何使用Python构建两个金融指标之间的回归方程，并计算它们的相关系数。我们将通过实际代码演示，从数据预处理、模型构建到结果可视化的完整流程，并探讨如何分析相关系数随时间的变化情况。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_5.png)

---

![](img/5e32cb08ebc21dc6b3c08528d68a8937_7.png)

## 构建回归方程

上一节我们介绍了金融指标的基本分析，本节中我们来看看如何量化两个指标之间的关系。构建回归方程的方法很多，可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_9.png)

`polyfit`函数可以帮助我们计算一个回归模型的系数。具体来说，它用于拟合一个多项式。函数需要传入几个参数：首先是指标X和指标Y的数据，用于计算它们之间的关系；其次是多项式的阶数`deg`。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_11.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_13.png)

例如，对于一元线性回归方程 **Y = KX + B**，其中包含X的一次项和零次项（常数项）。如果我们设置`deg=1`，则只拟合X的一次项和零次项。如果设置`deg=2`，则会拟合X的二次项、一次项和零次项，从而可以描述曲线关系。在本任务中，我们只研究线性关系，因此指定`deg=1`即可。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_15.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_17.png)

以下是构建回归方程的关键步骤代码：

![](img/5e32cb08ebc21dc6b3c08528d68a8937_19.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_21.png)

```python
import numpy as np

![](img/5e32cb08ebc21dc6b3c08528d68a8937_23.png)

# 假设 df[‘Close‘] 是指标X， df[‘Volume‘] 是指标Y
# 首先，确保数据中没有空值
df.dropna(inplace=True)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_25.png)

# 使用 polyfit 计算线性回归系数 (K, B)
coefficients = np.polyfit(df[‘Close‘], df[‘Volume‘], deg=1)
# coefficients[0] 是斜率 K， coefficients[1] 是截距 B
K, B = coefficients[0], coefficients[1]
print(f“回归方程为: Y = {K:.4f} * X + {B:.4f}“)
```

![](img/5e32cb08ebc21dc6b3c08528d68a8937_27.png)

在计算过程中，如果数据包含空值，求逆矩阵时可能会报错。因此，在拟合前进行数据清洗（如使用`dropna`）是必要的步骤。

---

![](img/5e32cb08ebc21dc6b3c08528d68a8937_29.png)

## 可视化回归结果

![](img/5e32cb08ebc21dc6b3c08528d68a8937_31.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_33.png)

得到回归方程后，我们可以在散点图的基础上将拟合的直线画出来，使关系更直观。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_35.png)

以下是绘制散点图和回归线的步骤：

![](img/5e32cb08ebc21dc6b3c08528d68a8937_37.png)

1.  首先，绘制两个指标的散点图。
2.  然后，根据回归方程 **Y = KX + B**，计算出一系列对应于X值的预测Y值。
3.  最后，将这些点连接成线，绘制在同一个图上。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_39.png)

具体实现代码如下：

![](img/5e32cb08ebc21dc6b3c08528d68a8937_41.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_43.png)

```python
import matplotlib.pyplot as plt

![](img/5e32cb08ebc21dc6b3c08528d68a8937_45.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(df[‘Close‘], df[‘Volume‘], s=16, label=‘数据点‘)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_47.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_49.png)

# 生成回归线的点
x_line = df[‘Close‘]
# 根据回归方程计算对应的y值
y_line = K * x_line + B

![](img/5e32cb08ebc21dc6b3c08528d68a8937_51.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_53.png)

# 绘制回归线
plt.plot(x_line, y_line, color=‘red‘, label=‘回归线‘)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_55.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_57.png)

plt.xlabel(‘收盘价 (Close)‘)
plt.ylabel(‘成交量 (Volume)‘)
plt.title(‘收盘价与成交量的回归分析‘)
plt.legend()
plt.show()
```

![](img/5e32cb08ebc21dc6b3c08528d68a8937_59.png)

现在，回归方程的结果就一目了然地展示在图中了。需要注意的是，本例中使用的数据是指标的增长率，而非原始价格。在实际分析中，根据目标选择合适的预处理数据（如原始值、收益率、波动率等）非常重要。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_61.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_63.png)

---

![](img/5e32cb08ebc21dc6b3c08528d68a8937_65.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_67.png)

## 计算相关系数

![](img/5e32cb08ebc21dc6b3c08528d68a8937_69.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_71.png)

除了回归方程，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。在Pandas中，计算相关系数非常简单。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_73.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_75.png)

我们可以直接调用DataFrame的`.corr()`方法来计算所有列之间的相关系数矩阵。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_77.png)

以下是计算和展示相关系数的代码：

```python
# 计算相关系数矩阵
correlation_matrix = df[[‘Close‘, ‘Volume‘]].corr()
print(correlation_matrix)
```

![](img/5e32cb08ebc21dc6b3c08528d68a8937_79.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_81.png)

这个矩阵的对角线是变量与自身的相关系数（总是1），非对角线上的值则是对称的，表示两个变量之间的相关系数。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_83.png)

---

![](img/5e32cb08ebc21dc6b3c08528d68a8937_85.png)

## 附加任务：分析相关系数随时间的变化

![](img/5e32cb08ebc21dc6b3c08528d68a8937_87.png)

为了更深入地理解指标间关系的动态特性，我们可以研究相关系数如何随时间变化。这需要我们引入时间窗口的概念。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_89.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_91.png)

具体思路是：随着时间推移，我们不是计算整个时间段的单一相关系数，而是计算一个滑动窗口内（例如最近250天）两个指标的相关系数。这样，我们就得到了一个随时间变化的相关系数序列。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_93.png)

以下是实现该分析的步骤：

![](img/5e32cb08ebc21dc6b3c08528d68a8937_95.png)

1.  对于时间序列数据，使用`.rolling()`方法创建指定大小的窗口。
2.  在每个窗口内，计算两个指标（例如`Close`和`Volume`）的相关系数。
3.  将计算出的相关系数序列可视化。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_97.png)

实现代码如下：

![](img/5e32cb08ebc21dc6b3c08528d68a8937_99.png)

```python
# 设置窗口大小，例如250天
window_size = 250

![](img/5e32cb08ebc21dc6b3c08528d68a8937_101.png)

# 计算滚动相关系数
rolling_corr = df[‘Close‘].rolling(window=window_size).corr(df[‘Volume‘])

![](img/5e32cb08ebc21dc6b3c08528d68a8937_103.png)

# 绘制滚动相关系数变化图
plt.figure(figsize=(12, 6))
rolling_corr.plot(figsize=(12, 6))
plt.title(f‘{window_size}天滚动窗口下的收盘价与成交量相关系数变化‘)
plt.xlabel(‘日期‘)
plt.ylabel(‘相关系数‘)
plt.axhline(y=0, color=‘grey‘, linestyle=‘--‘) # 添加y=0的参考线
plt.show()
```

![](img/5e32cb08ebc21dc6b3c08528d68a8937_105.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_107.png)

通过这张图，我们可以观察到两个指标间的关联强度在不同市场时期是如何演变的，这比单一的静态相关系数包含了更多的信息。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_109.png)

---

![](img/5e32cb08ebc21dc6b3c08528d68a8937_111.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_113.png)

## 总结

![](img/5e32cb08ebc21dc6b3c08528d68a8937_115.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_117.png)

本节课中我们一起学习了金融时间序列分析中的两个核心关系量化工具。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_119.png)

我们首先介绍了如何使用**回归方程**（**Y = KX + B**）来描述两个变量间的线性关系，并利用`np.polyfit`函数进行计算和可视化。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_121.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_123.png)

接着，我们探讨了如何使用**相关系数**来度量变量间线性关系的强度和方向，并通过Pandas轻松实现了计算。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_125.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_127.png)

最后，我们完成了一道附加题，展示了如何通过计算**滚动窗口相关系数**来观察关系强度随时间的变化，这有助于我们捕捉动态的市场关联特征。

![](img/5e32cb08ebc21dc6b3c08528d68a8937_129.png)

![](img/5e32cb08ebc21dc6b3c08528d68a8937_131.png)

建议大家在课后对照代码练习一遍，理解每一步的作用。可以尝试修改参数（如窗口大小、多项式阶数），以加深对概念和方法的理解。本节内容涵盖了从基础统计到动态分析的重要技能，是金融数据分析的实用基础。