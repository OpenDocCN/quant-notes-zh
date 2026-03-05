# 量化交易实战：P4：短均与长均计算实例 📈

![](img/ef9c196d72cd9547a94159a61844c374_0.png)

在本节课中，我们将学习如何计算股票的短期和长期移动平均线，并利用它们识别“黄金交叉”和“死亡交叉”这两种常见的交易信号。我们将通过Python代码实现计算与可视化，让分析过程一目了然。

![](img/ef9c196d72cd9547a94159a61844c374_2.png)

---

![](img/ef9c196d72cd9547a94159a61844c374_4.png)

## 计算短期与长期移动平均线

![](img/ef9c196d72cd9547a94159a61844c374_6.png)

上一节我们介绍了移动平均线的概念，本节中我们来看看如何用代码具体计算短期和长期移动平均线。

![](img/ef9c196d72cd9547a94159a61844c374_8.png)

首先，我们需要选定分析的股票数据。这里我们以苹果公司（AAPL）的股价为例。计算移动平均线的核心是使用Pandas的`.rolling()`方法，它可以创建一个滑动窗口并计算窗口内数据的统计值（如均值）。

![](img/ef9c196d72cd9547a94159a61844c374_10.png)

以下是计算短期（例如10天）和长期（例如30天）移动平均线的代码：

```python
# 假设 df 是包含苹果股价的DataFrame，其中‘AAPL’是股价列
# 计算短期移动平均线（例如10天窗口）
short_window = 10
df['M1'] = df['AAPL'].rolling(window=short_window).mean()

# 计算长期移动平均线（例如30天窗口）
long_window = 30
df['M2'] = df['AAPL'].rolling(window=long_window).mean()
```

代码解释：
*   `df[‘AAPL’].rolling(window=10)`：对苹果股价列创建一个窗口大小为10的滚动窗口对象。
*   `.mean()`：计算每个窗口内数据的平均值，结果就是10日移动平均线。
*   同理，`df[‘M2’]`存储的是30日移动平均线。

![](img/ef9c196d72cd9547a94159a61844c374_12.png)

计算完成后，我们可以查看一下包含原始股价和两条移动平均线的数据：

![](img/ef9c196d72cd9547a94159a61844c374_14.png)

```python
# 查看包含股价、短期均线和长期均线的数据前几行
df[['AAPL', 'M1', 'M2']].head()
```

![](img/ef9c196d72cd9547a94159a61844c374_16.png)

![](img/ef9c196d72cd9547a94159a61844c374_18.png)

---

## 可视化股价与移动平均线

![](img/ef9c196d72cd9547a94159a61844c374_20.png)

计算出指标后，最直观的方式就是将它们绘制在图表上。我们可以使用Matplotlib库来绘制折线图。

![](img/ef9c196d72cd9547a94159a61844c374_22.png)

以下是绘制股价、短期均线（M1）和长期均线（M2）的代码示例：

![](img/ef9c196d72cd9547a94159a61844c374_24.png)

```python
import matplotlib.pyplot as plt

![](img/ef9c196d72cd9547a94159a61844c374_26.png)

![](img/ef9c196d72cd9547a94159a61844c374_28.png)

# 设置图形大小
plt.figure(figsize=(14, 7))
# 绘制苹果股价
plt.plot(df['AAPL'], label='AAPL Price', alpha=0.5)
# 绘制短期移动平均线
plt.plot(df['M1'], label='Short MA (10 days)', color='green')
# 绘制长期移动平均线
plt.plot(df['M2'], label='Long MA (30 days)', color='red')

plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

为了使图表更清晰，特别是当数据时间跨度很长时，我们可以选择只绘制最近一段时间（例如250个交易日，约一年）的数据。

```python
# 绘制最近250个交易日的数据
recent_data = df[['AAPL', 'M1', 'M2']].tail(250)
plt.figure(figsize=(14, 7))
plt.plot(recent_data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(recent_data['M1'], label='Short MA (10 days)', color='green')
plt.plot(recent_data['M2'], label='Long MA (30 days)', color='red')
plt.title('AAPL Stock Price with Moving Averages (Last 250 Days)')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/ef9c196d72cd9547a94159a61844c374_30.png)

在生成的图表中，我们可以观察三条线的相对位置关系。

![](img/ef9c196d72cd9547a94159a61844c374_32.png)

![](img/ef9c196d72cd9547a94159a61844c374_34.png)

---

![](img/ef9c196d72cd9547a94159a61844c374_36.png)

## 识别黄金交叉与死亡交叉

![](img/ef9c196d72cd9547a94159a61844c374_38.png)

在图表上，我们可以手动寻找“黄金交叉”和“死亡交叉”的信号点。

![](img/ef9c196d72cd9547a94159a61844c374_40.png)

![](img/ef9c196d72cd9547a94159a61844c374_42.png)

以下是这两种交叉信号的定义：
*   **黄金交叉**：短期移动平均线从下方**上穿**长期移动平均线。这通常被视为**买入信号**，预示着股价可能开始上涨趋势。
*   **死亡交叉**：短期移动平均线从上方**下穿**长期移动平均线。这通常被视为**卖出信号**，预示着股价可能开始下跌趋势。

![](img/ef9c196d72cd9547a94159a61844c374_44.png)

在图表中，当绿色的短期均线向上穿过红色的长期均线时，就形成了黄金交叉；反之，当绿色线向下穿过红色线时，则形成死亡交叉。

---

## 使用代码自动标记交叉信号

![](img/ef9c196d72cd9547a94159a61844c374_46.png)

![](img/ef9c196d72cd9547a94159a61844c374_48.png)

手动看图找交叉点效率较低。我们可以用代码自动计算并标记出这些信号区域。思路是创建一个新列，用于标识短期均线是高于还是低于长期均线。

以下是使用NumPy的`where`函数来创建信号列的代码：

![](img/ef9c196d72cd9547a94159a61844c374_50.png)

![](img/ef9c196d72cd9547a94159a61844c374_52.png)

```python
import numpy as np

![](img/ef9c196d72cd9547a94159a61844c374_54.png)

# 当短期均线大于长期均线时，标记为1；否则标记为-1
df['Position'] = np.where(df['M1'] > df['M2'], 1, -1)
```

`df[‘Position’]`列的值在短期均线高于长期均线时为`1`，反之为`-1`。这个`1`和`-1`的切换点，正好对应了交叉的发生。

![](img/ef9c196d72cd9547a94159a61844c374_56.png)

为了更清晰地展示这个信号，我们可以将其绘制在股价图的右侧Y轴上。

![](img/ef9c196d72cd9547a94159a61844c374_58.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

# 左侧Y轴：绘制股价和均线
ax1.plot(df['AAPL'], label='AAPL Price', color='blue', alpha=0.3)
ax1.plot(df['M1'], label='Short MA (10 days)', color='green')
ax1.plot(df['M2'], label='Long MA (30 days)', color='red')
ax1.set_xlabel('Date')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')

![](img/ef9c196d72cd9547a94159a61844c374_60.png)

# 创建右侧Y轴，用于绘制信号线
ax2 = ax1.twinx()
ax2.plot(df['Position'], label='Signal (1/-1)', color='purple', alpha=0.7, linestyle='--')
ax2.set_ylabel('Signal')
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc='upper right')

![](img/ef9c196d72cd9547a94159a61844c374_62.png)

plt.title('AAPL with Moving Averages and Trading Signals')
plt.show()
```

![](img/ef9c196d72cd9547a94159a61844c374_64.png)

在这张图中，紫色的虚线（信号线）在`1`和`-1`之间切换。
*   当信号线从`-1`**上升到**`1`时，对应**黄金交叉**（买入信号）。
*   当信号线从`1`**下降到**`-1`时，对应**死亡交叉**（卖出信号）。

这样，我们就能清晰地通过代码定位每一个潜在的交易时机。

---

![](img/ef9c196d72cd9547a94159a61844c374_66.png)

## 总结

![](img/ef9c196d72cd9547a94159a61844c374_68.png)

本节课中我们一起学习了移动平均线在量化分析中的实际应用。

![](img/ef9c196d72cd9547a94159a61844c374_70.png)

我们首先使用Pandas的`.rolling().mean()`方法计算了股票的短期（如10日）和长期（如30日）移动平均线。接着，通过Matplotlib将股价与两条均线可视化，并解释了如何根据图表识别“黄金交叉”（买入信号）和“死亡交叉”（卖出信号）。最后，我们利用NumPy自动生成了交易信号列，并通过双Y轴图表将价格走势与买卖信号同时展示出来，使得分析更加直观和自动化。

![](img/ef9c196d72cd9547a94159a61844c374_72.png)

![](img/ef9c196d72cd9547a94159a61844c374_74.png)

通过这个实例，你可以掌握使用Python进行基础技术指标计算和可视化分析的基本流程，这是构建更复杂量化交易策略的重要一步。