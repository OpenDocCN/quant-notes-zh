# Python金融分析与量化交易实战教程：P9：3-短均与长均计算实例 📈

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_1.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将识别“黄金交叉”和“死亡交叉”这两个关键信号，并通过可视化清晰地展示它们。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_3.png)

## 概述

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_5.png)

我们将分析苹果公司的股票数据。核心策略是计算两个移动平均线：一个短期（例如10天）和一个长期（例如30天）。通过观察这两条线的交叉点，我们可以判断市场的潜在买入或卖出信号。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_7.png)

## 计算短期与长期移动平均线

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_9.png)

首先，我们需要从数据中计算出短期和长期的移动平均值。移动平均线是技术分析中用于平滑价格数据、识别趋势的常用工具。

以下是计算步骤的核心代码：

```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘AAPL’
# 计算10日短期移动平均线
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_11.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_13.png)

# 计算30日长期移动平均线
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_15.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_17.png)

**代码解释**：
*   `df[‘AAPL’].rolling(window=10)` 创建了一个窗口大小为10的滚动窗口对象。
*   `.mean()` 对这个滚动窗口内的数据计算平均值，从而得到移动平均线。
*   结果分别存储在新列 `M1`（短期均线）和 `M2`（长期均线）中。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_19.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_21.png)

## 可视化移动平均线与股价

计算出指标后，最直观的方法是将其与原始股价一同绘制出来进行观察。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_23.png)

我们使用Matplotlib库来绘制折线图：

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_25.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_27.png)

```python
import matplotlib.pyplot as plt

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_29.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_31.png)

# 绘制图表
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘10-Day MA (Short)’)
plt.plot(df[‘M2’], label=‘30-Day MA (Long)’)
plt.legend()
plt.show()
```

**图表分析**：
在生成的图表中，我们可以清晰地看到三条线：股价线、短期均线（M1）和长期均线（M2）。交叉点就是我们需要重点观察的区域。

## 识别黄金交叉与死亡交叉

上一节我们绘制了均线图，本节中我们来看看如何定义并识别关键的交叉信号。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_33.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_35.png)

“黄金交叉”和“死亡交叉”是移动平均线策略中的两个核心概念：

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_37.png)

*   **黄金交叉**：短期移动平均线从下方向上穿越长期移动平均线。这通常被视为**买入信号**，预示着趋势可能由跌转涨。
*   **死亡交叉**：短期移动平均线从上方向下穿越长期移动平均线。这通常被视为**卖出信号**，预示着趋势可能由涨转跌。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_39.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_41.png)

在刚才的图表中，你可以尝试寻找这样的交叉点：当绿色的短期均线（M1）上穿红色的长期均线（M2）时，对应区域可能就是黄金交叉；反之则为死亡交叉。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_43.png)

## 使用信号指标进行增强分析

仅靠肉眼观察交叉点可能不够精确。我们可以创建一个新的信号指标，来量化显示短期均线相对于长期均线的位置。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_45.png)

以下是创建信号指标的步骤：

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_47.png)

1.  **比较大小**：我们逐点比较短期均线（M1）和长期均线（M2）的值。
2.  **生成信号**：当 M1 > M2 时，我们认为处于“看涨”区域，标记为 `1`；当 M1 < M2 时，我们认为处于“看跌”区域，标记为 `-1`。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_49.png)

我们可以使用NumPy的 `where` 函数来实现：

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_51.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_53.png)

```python
import numpy as np

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_55.png)

# 生成交易信号位置列
df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
```

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_57.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_59.png)

**代码解释**：
*   `np.where(condition, x, y)` 是一个条件赋值函数。
*   当条件 `df[‘M1’] > df[‘M2’]` 为真时，`position` 列赋值为 `1`。
*   当条件为假时，赋值为 `-1`。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_61.png)

## 绘制双坐标轴图表进行信号验证

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_63.png)

为了更清晰地展示股价、均线与信号指标的关系，我们可以使用双坐标轴进行绘图。主坐标轴（左侧）显示股价和均线，次坐标轴（右侧）显示我们的信号指标。

以下是绘图代码：

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_65.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_67.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_69.png)

# 在主坐标轴（ax1）上绘制股价和均线
ax1.plot(df[‘AAPL’], label=‘Stock Price’, color=‘blue’, alpha=0.3)
ax1.plot(df[‘M1’], label=‘10-Day MA’, color=‘green’)
ax1.plot(df[‘M2’], label=‘30-Day MA’, color=‘red’)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

# 创建次坐标轴（ax2）
ax2 = ax1.twinx()
# 在次坐标轴上绘制信号指标，使用阶梯图更清晰
ax2.plot(df[‘position’], label=‘Signal (1/-1)’, color=‘purple’, linestyle=‘--’, alpha=0.7)
ax2.set_ylabel(‘Signal’)
ax2.set_ylim([-1.5, 1.5]) # 限制信号轴范围
ax2.legend(loc=‘upper right’)

plt.title(‘Apple Stock with Moving Averages and Trading Signal’)
plt.show()
```

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_71.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_73.png)

**图表解读**：
在最终的图表中，紫色虚线（信号线）在 `1` 和 `-1` 之间切换。当信号线从 `-1` 跃升到 `1` 时，对应着图表中的“黄金交叉”区域，提示潜在的买入机会。当信号线从 `1` 下降到 `-1` 时，则对应着“死亡交叉”区域，提示风险。

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_75.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_77.png)

## 总结

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_79.png)

![](img/de7457a8a8ec4aa1b5f38c69c58d9cea_81.png)

本节课中我们一起学习了如何利用Pandas计算移动平均线，并基于此构建一个简单的趋势跟踪策略。我们掌握了两个关键概念：**黄金交叉（买入信号）** 和 **死亡交叉（卖出信号）**。通过使用 `np.where` 函数生成清晰的交易信号，并利用Matplotlib的双坐标轴功能进行可视化，我们能够更直观、更量化地识别这些交易时机。这个方法为后续构建更复杂的量化交易模型奠定了基础。