# Python金融量化：P8：回归方程与相关系数实例 📈

![](img/6e844b20c5665e34d198f1966967a1cb_1.png)

![](img/6e844b20c5665e34d198f1966967a1cb_3.png)

![](img/6e844b20c5665e34d198f1966967a1cb_5.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是量化分析中理解变量间关系的基础技能。我们将通过实例演示，从数据处理到结果可视化的完整流程。

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建两个金融指标之间的回归方程。构建回归方程的方法很多，我们可以使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/6e844b20c5665e34d198f1966967a1cb_7.png)

![](img/6e844b20c5665e34d198f1966967a1cb_9.png)

![](img/6e844b20c5665e34d198f1966967a1cb_11.png)

该函数的核心是拟合一个多项式。对于线性回归，我们拟合的是一阶多项式，即 `y = kx + b`。在`polyfit`函数中，我们需要传入三个主要参数：自变量X、因变量Y以及多项式的阶数（degree）。

![](img/6e844b20c5665e34d198f1966967a1cb_13.png)

![](img/6e844b20c5665e34d198f1966967a1cb_15.png)

![](img/6e844b20c5665e34d198f1966967a1cb_17.png)

以下是使用`polyfit`函数计算线性回归系数的代码：

![](img/6e844b20c5665e34d198f1966967a1cb_19.png)

![](img/6e844b20c5665e34d198f1966967a1cb_21.png)

```python
import numpy as np

![](img/6e844b20c5665e34d198f1966967a1cb_23.png)

# 假设 df[‘close_px‘] 是自变量X， df[‘vix‘] 是因变量Y
# 在进行回归计算前，必须处理数据中的空值
df.dropna(inplace=True)

# 使用polyfit计算一阶线性回归的系数k和b
# 参数：X数据， Y数据， 多项式阶数（1表示线性）
coefficients = np.polyfit(df[‘close_px‘], df[‘vix‘], 1)
k, b = coefficients
print(f“回归方程为: y = {k:.4f} * x + {b:.4f}“)
```

![](img/6e844b20c5665e34d198f1966967a1cb_25.png)

![](img/6e844b20c5665e34d198f1966967a1cb_27.png)

![](img/6e844b20c5665e34d198f1966967a1cb_29.png)

## 可视化回归结果

![](img/6e844b20c5665e34d198f1966967a1cb_31.png)

得到回归方程后，我们可以将其可视化，与原始数据散点图进行对比，直观地观察拟合效果。

![](img/6e844b20c5665e34d198f1966967a1cb_33.png)

![](img/6e844b20c5665e34d198f1966967a1cb_35.png)

以下是绘制散点图及回归线的代码：

![](img/6e844b20c5665e34d198f1966967a1cb_37.png)

```python
import matplotlib.pyplot as plt

![](img/6e844b20c5665e34d198f1966967a1cb_39.png)

![](img/6e844b20c5665e34d198f1966967a1cb_41.png)

# 绘制原始数据散点图
plt.figure(figsize=(16, 9))
plt.scatter(df[‘close_px‘], df[‘vix‘], alpha=0.5, label=‘原始数据‘)

![](img/6e844b20c5665e34d198f1966967a1cb_43.png)

![](img/6e844b20c5665e34d198f1966967a1cb_45.png)

![](img/6e844b20c5665e34d198f1966967a1cb_47.png)

# 根据回归方程计算拟合线的Y值
x_line = df[‘close_px‘]
y_line = k * x_line + b  # 使用求得的k和b

![](img/6e844b20c5665e34d198f1966967a1cb_49.png)

![](img/6e844b20c5665e34d198f1966967a1cb_51.png)

# 绘制回归线
plt.plot(x_line, y_line, color=‘red‘, linewidth=2, label=‘回归线‘)

![](img/6e844b20c5665e34d198f1966967a1cb_53.png)

![](img/6e844b20c5665e34d198f1966967a1cb_55.png)

![](img/6e844b20c5665e34d198f1966967a1cb_57.png)

plt.xlabel(‘收盘价‘)
plt.ylabel(‘恐慌指数‘)
plt.title(‘收盘价与恐慌指数的回归分析‘)
plt.legend()
plt.grid(True)
plt.show()
```

![](img/6e844b20c5665e34d198f1966967a1cb_59.png)

![](img/6e844b20c5665e34d198f1966967a1cb_61.png)

## 计算相关系数

![](img/6e844b20c5665e34d198f1966967a1cb_63.png)

除了回归分析，衡量两个变量之间线性关系强度的另一个重要指标是相关系数。在Pandas中，这可以通过`.corr()`方法轻松实现。

![](img/6e844b20c5665e34d198f1966967a1cb_65.png)

![](img/6e844b20c5665e34d198f1966967a1cb_67.png)

以下是计算并展示相关系数矩阵的代码：

![](img/6e844b20c5665e34d198f1966967a1cb_69.png)

```python
# 计算两个序列的相关系数矩阵
correlation_matrix = df[[‘close_px‘, ‘vix‘]].corr()
print(correlation_matrix)
```

![](img/6e844b20c5665e34d198f1966967a1cb_71.png)

## 进阶分析：滑动窗口相关系数

![](img/6e844b20c5665e34d198f1966967a1cb_73.png)

![](img/6e844b20c5665e34d198f1966967a1cb_75.png)

![](img/6e844b20c5665e34d198f1966967a1cb_77.png)

在实际分析中，变量间的关系可能随时间变化。为了观察这种动态关系，我们可以计算滑动窗口下的相关系数。

![](img/6e844b20c5665e34d198f1966967a1cb_79.png)

以下是计算随时间变化的滑动窗口相关系数并绘图的代码：

```python
# 设置窗口大小，例如250个交易日
window_size = 250

![](img/6e844b20c5665e34d198f1966967a1cb_81.png)

# 计算close_px与vix在滑动窗口下的相关系数
rolling_corr = df[‘close_px‘].rolling(window=window_size).corr(df[‘vix‘])

![](img/6e844b20c5665e34d198f1966967a1cb_83.png)

![](img/6e844b20c5665e34d198f1966967a1cb_85.png)

![](img/6e844b20c5665e34d198f1966967a1cb_87.png)

# 绘制滑动窗口相关系数变化图
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f‘{window_size}日滑动窗口相关系数变化‘)
plt.xlabel(‘日期‘)
plt.ylabel(‘相关系数‘)
plt.axhline(y=0, color=‘grey‘, linestyle=‘--‘)
plt.grid(True)
plt.show()
```

![](img/6e844b20c5665e34d198f1966967a1cb_89.png)

## 总结

![](img/6e844b20c5665e34d198f1966967a1cb_91.png)

![](img/6e844b20c5665e34d198f1966967a1cb_93.png)

![](img/6e844b20c5665e34d198f1966967a1cb_95.png)

本节课中我们一起学习了金融量化分析中的两个核心关系分析工具。

![](img/6e844b20c5665e34d198f1966967a1cb_97.png)

![](img/6e844b20c5665e34d198f1966967a1cb_99.png)

我们首先使用NumPy的`polyfit`函数构建了线性回归方程 **`y = kx + b`**，并通过可视化将拟合线与原始数据对比。接着，我们使用Pandas的`.corr()`方法快速计算了两个变量的**相关系数**。最后，我们进行了进阶实践，计算了**滑动窗口相关系数**，以观察变量间关系随时间的动态变化。

![](img/6e844b20c5665e34d198f1966967a1cb_101.png)

![](img/6e844b20c5665e34d198f1966967a1cb_103.png)

建议大家在课后亲自动手练习代码，尝试改变窗口大小或选择不同的指标进行分析，以加深对这些强大工具的理解和应用能力。