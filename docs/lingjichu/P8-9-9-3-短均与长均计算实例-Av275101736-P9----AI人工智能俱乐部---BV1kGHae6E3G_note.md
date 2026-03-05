# 股票分析策略：P8：短均与长均计算实例 📈

![](img/2f1fc1b6237382725e397af031dcf7fd_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。这个策略的核心是识别“黄金交叉”和“死亡交叉”，以辅助判断买入和卖出的时机。

![](img/2f1fc1b6237382725e397af031dcf7fd_3.png)

## 概述

![](img/2f1fc1b6237382725e397af031dcf7fd_5.png)

我们将使用苹果公司的股票数据作为示例。整个流程分为三步：首先计算短期移动平均线，然后计算长期移动平均线，最后将结果可视化，并识别关键的交叉点。

![](img/2f1fc1b6237382725e397af031dcf7fd_7.png)

---

![](img/2f1fc1b6237382725e397af031dcf7fd_9.png)

## 计算短期移动平均线

上一节我们介绍了策略的目标，本节中我们来看看如何计算第一个指标——短期移动平均线。

移动平均线的计算方法是取指定时间窗口内股价的平均值。对于短期均线，通常使用5天（即一周的交易日）作为窗口。但由于我们的示例数据时间跨度较长，为了更清晰地展示，我们将短期窗口设置为10天。

以下是计算短期移动平均线的步骤：

![](img/2f1fc1b6237382725e397af031dcf7fd_11.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_13.png)

1.  选择我们要分析的股票数据，这里使用苹果公司（AAPL）的股价。
2.  使用Pandas的`.rolling()`方法创建一个滚动窗口对象。
3.  对窗口内的数据计算平均值（`.mean()`），得到短期移动平均线。

![](img/2f1fc1b6237382725e397af031dcf7fd_15.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_17.png)

对应的核心代码如下：

```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘AAPL‘
short_window = 10
df[‘M1‘] = df[‘AAPL‘].rolling(window=short_window).mean()
```

![](img/2f1fc1b6237382725e397af031dcf7fd_19.png)

这段代码创建了一个名为 `M1` 的新列，它存储了以10天为窗口计算的股价移动平均值。

![](img/2f1fc1b6237382725e397af031dcf7fd_21.png)

---

![](img/2f1fc1b6237382725e397af031dcf7fd_23.png)

## 计算长期移动平均线

![](img/2f1fc1b6237382725e397af031dcf7fd_25.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_27.png)

在计算出短期均线后，接下来我们需要计算长期移动平均线作为对比基准。

长期移动平均线通常使用20天或30天（约一个月）作为窗口。为了与短期均线形成明显对比，我们这里选择30天作为长期窗口。

计算长期移动平均线的方法与短期类似：

1.  使用相同的股价数据列。
2.  创建一个更大的滚动窗口（例如30天）。
3.  计算该窗口内的平均值。

![](img/2f1fc1b6237382725e397af031dcf7fd_29.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_31.png)

对应的核心代码如下：

![](img/2f1fc1b6237382725e397af031dcf7fd_33.png)

```python
long_window = 30
df[‘M2‘] = df[‘AAPL‘].rolling(window=long_window).mean()
```

![](img/2f1fc1b6237382725e397af031dcf7fd_35.png)

这段代码创建了名为 `M2` 的新列，存储了30天长期移动平均值。

![](img/2f1fc1b6237382725e397af031dcf7fd_37.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_39.png)

---

## 数据可视化与交叉点识别

现在我们已经有了短期（`M1`）和长期（`M2`）两条移动平均线，本节我们将它们与原始股价一起绘制在图表中，并学习如何识别“黄金交叉”与“死亡交叉”。

![](img/2f1fc1b6237382725e397af031dcf7fd_41.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_43.png)

“死亡交叉”指短期均线从上方向下穿越长期均线，通常被视为下跌信号。“黄金交叉”则指短期均线从下方向上穿越长期均线，通常被视为上涨信号。

为了更直观地展示交叉点，我们还可以增加一个信号列。当短期均线大于长期均线时，标记为 `1`；反之则标记为 `-1`。这个信号的变化点就对应着交叉的发生。

![](img/2f1fc1b6237382725e397af031dcf7fd_45.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_47.png)

以下是生成信号和绘制综合图表的核心步骤：

![](img/2f1fc1b6237382725e397af031dcf7fd_49.png)

1.  使用 `np.where` 函数比较 `M1` 和 `M2`，生成信号列 `position`。
2.  使用Matplotlib绘制股价、两条均线以及信号线。为了让信号线（取值仅为1或-1）清晰可见，我们使用双Y轴进行绘制。

![](img/2f1fc1b6237382725e397af031dcf7fd_51.png)

对应的核心代码如下：

![](img/2f1fc1b6237382725e397af031dcf7fd_53.png)

```python
import numpy as np
import matplotlib.pyplot as plt

![](img/2f1fc1b6237382725e397af031dcf7fd_55.png)

# 生成交易信号
df[‘position‘] = np.where(df[‘M1‘] > df[‘M2‘], 1, -1)

# 创建图形和主Y轴
fig, ax1 = plt.subplots(figsize=(12,6))
ax1.plot(df[‘AAPL‘], label=‘AAPL Price‘, color=‘blue‘, alpha=0.5)
ax1.plot(df[‘M1‘], label=‘Short MA (10-day)‘, color=‘green‘)
ax1.plot(df[‘M2‘], label=‘Long MA (30-day)‘, color=‘red‘)
ax1.set_xlabel(‘Date‘)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)

# 创建次Y轴用于显示信号
ax2 = ax1.twinx()
ax2.plot(df[‘position‘], label=‘Signal (1/-1)‘, color=‘purple‘, linestyle=‘--‘)
ax2.set_ylabel(‘Signal‘)
ax2.legend(loc=‘upper right‘)

![](img/2f1fc1b6237382725e397af031dcf7fd_57.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_59.png)

plt.title(‘AAPL Stock Price with Moving Averages and Trading Signal‘)
plt.show()
```

![](img/2f1fc1b6237382725e397af031dcf7fd_61.png)

在生成的图表中，您可以观察到：
*   当绿色短期均线下穿红色长期均线，同时紫色信号线从 `1` 变为 `-1` 时，发生“死亡交叉”。
*   当绿色短期均线上穿红色长期均线，同时紫色信号线从 `-1` 变为 `1` 时，发生“黄金交叉”。

---

## 总结

![](img/2f1fc1b6237382725e397af031dcf7fd_63.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_65.png)

本节课中我们一起学习了如何构建一个基础的股票移动平均线分析策略。

![](img/2f1fc1b6237382725e397af031dcf7fd_67.png)

我们首先介绍了“黄金交叉”和“死亡交叉”的概念。接着，我们使用Pandas的滚动窗口功能，分别计算了短期（10日）和长期（30日）移动平均线。然后，我们通过Matplotlib将股价和两条均线可视化，并利用 `np.where` 函数生成交易信号，清晰地标识出了两个交叉点。

![](img/2f1fc1b6237382725e397af031dcf7fd_69.png)

![](img/2f1fc1b6237382725e397af031dcf7fd_71.png)

这个实例展示了如何利用Pandas进行简单的金融数据分析和计算，并通过可视化直观地呈现分析结果。在实际应用中，您可以调整窗口参数（如5日、20日），并尝试结合其他指标来优化策略。