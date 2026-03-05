# Python金融分析与量化交易实战：P6：05-5-短均与长均计算实例 📈

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来识别“黄金交叉”与“死亡交叉”这两种重要的交易信号。我们将使用Python的Pandas库进行计算，并用Matplotlib进行可视化展示。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_1.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_3.png)

上一节我们介绍了数据获取与基础处理，本节中我们来看看如何计算移动平均线并识别交叉信号。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_5.png)

## 计算短期与长期移动平均线

![](img/c1d34a6aa6172f8199dabfe397cbd92b_7.png)

要识别黄金交叉和死亡交叉，我们需要两个核心指标：短期移动平均线和长期移动平均线。交叉点就是这两个指标在图表上相交的位置。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_9.png)

以下是计算移动平均线的步骤：

1.  **选择目标股票**：我们将以苹果公司（AAPL）的股票数据为例进行分析。
2.  **定义计算窗口**：短期移动平均线通常使用较小的窗口（如5日、10日），长期移动平均线则使用较大的窗口（如20日、30日）。由于我们的数据集时间跨度较长（2010-2018年），为了在图表上更清晰地展示交叉点，我们将短期窗口设为10日，长期窗口设为30日。
3.  **执行计算**：使用Pandas的`.rolling()`和`.mean()`方法计算指定窗口内的股价平均值。

核心的计算公式和代码如下：

![](img/c1d34a6aa6172f8199dabfe397cbd92b_11.png)

```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘AAPL‘
# 计算10日短期移动平均线
short_window = 10
df[‘M1‘] = df[‘AAPL‘].rolling(window=short_window).mean()

![](img/c1d34a6aa6172f8199dabfe397cbd92b_13.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_15.png)

# 计算30日长期移动平均线
long_window = 30
df[‘M2‘] = df[‘AAPL‘].rolling(window=long_window).mean()
```

![](img/c1d34a6aa6172f8199dabfe397cbd92b_17.png)

## 可视化移动平均线与股价

![](img/c1d34a6aa6172f8199dabfe397cbd92b_19.png)

计算完成后，我们可以将股价、短期均线和长期均线绘制在同一张图上进行观察。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_21.png)

以下是绘图的关键代码：

![](img/c1d34a6aa6172f8199dabfe397cbd92b_23.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_25.png)

```python
import matplotlib.pyplot as plt

![](img/c1d34a6aa6172f8199dabfe397cbd92b_27.png)

# 选择要绘制的列：股价、短期均线、长期均线
plot_columns = [‘AAPL‘, ‘M1‘, ‘M2‘]
df[plot_columns].plot(figsize=(12, 6))
plt.show()
```

为了更清晰地观察交叉细节，我们可以截取一段时间的数据（例如250个交易日，约一年）进行绘图。

在生成的图表中，通常：
*   蓝色线代表股价。
*   绿色线代表短期移动平均线（M1）。
*   红色线代表长期移动平均线（M2）。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_29.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_31.png)

## 识别黄金交叉与死亡交叉

![](img/c1d34a6aa6172f8199dabfe397cbd92b_33.png)

现在，我们可以在图表上寻找交叉信号：

![](img/c1d34a6aa6172f8199dabfe397cbd92b_35.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_37.png)

*   **死亡交叉**：当短期均线从上方向下穿越长期均线时，通常被视为下跌信号，提示可能不适合买入或应考虑卖出。
*   **黄金交叉**：当短期均线从下方向上穿越长期均线时，通常被视为上涨信号，提示可能是买入时机。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_39.png)

通过观察图表，我们可以定位这些交叉点，并理解其市场含义。

## 使用信号指标增强分析

![](img/c1d34a6aa6172f8199dabfe397cbd92b_41.png)

为了更直观地展示交叉点，我们可以创建一个信号指标，明确标出短期均线高于或低于长期均线的区域。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_43.png)

以下是创建信号指标的步骤：

![](img/c1d34a6aa6172f8199dabfe397cbd92b_45.png)

1.  **定义信号规则**：当短期均线（M1）大于长期均线（M2）时，标记为1；否则标记为-1。
2.  **计算信号列**：使用NumPy的`where`函数实现条件判断。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_47.png)

核心代码如下：

![](img/c1d34a6aa6172f8199dabfe397cbd92b_49.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_51.png)

```python
import numpy as np

![](img/c1d34a6aa6172f8199dabfe397cbd92b_53.png)

# 创建信号列
df[‘position‘] = np.where(df[‘M1‘] > df[‘M2‘], 1, -1)
```

![](img/c1d34a6aa6172f8199dabfe397cbd92b_55.png)

## 绘制双坐标轴图表

由于信号值（1和-1）与股价数值范围差异巨大，直接绘制在同一坐标轴上会难以观察。因此，我们使用双坐标轴进行绘图。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_57.png)

以下是绘制带信号指标的双轴图表代码：

![](img/c1d34a6aa6172f8199dabfe397cbd92b_59.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_61.png)

```python
fig, ax1 = plt.subplots(figsize=(12, 6))

# 左Y轴：绘制股价和移动平均线
ax1.plot(df[‘AAPL‘], label=‘Price‘, color=‘blue‘, alpha=0.5)
ax1.plot(df[‘M1‘], label=‘Short MA (10)‘, color=‘green‘)
ax1.plot(df[‘M2‘], label=‘Long MA (30)‘, color=‘red‘)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)

# 右Y轴：绘制信号指标
ax2 = ax1.twinx()
ax2.plot(df[‘position‘], label=‘Signal (1/-1)‘, color=‘purple‘, linestyle=‘--‘)
ax2.set_ylabel(‘Signal‘)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right‘)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_63.png)

plt.title(‘Stock Price with Moving Averages and Trading Signal‘)
plt.show()
```

![](img/c1d34a6aa6172f8199dabfe397cbd92b_65.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_67.png)

在这张图上：
*   左Y轴显示价格和均线。
*   右Y轴显示信号指标（紫色虚线）。当信号为1时，表示短期均线高于长期均线（潜在看涨区域）；当信号为-1时，表示短期均线低于长期均线（潜在看跌区域）。
*   **信号线从-1变为1的交叉点，对应图表上的“黄金交叉”**。
*   **信号线从1变为-1的交叉点，对应图表上的“死亡交叉”**。

![](img/c1d34a6aa6172f8199dabfe397cbd92b_69.png)

![](img/c1d34a6aa6172f8199dabfe397cbd92b_71.png)

本节课中我们一起学习了如何计算股票的短期和长期移动平均线，并通过可视化方法识别“黄金交叉”与“死亡交叉”这两种关键的交易信号。我们还通过创建信号指标和绘制双坐标轴图表，使分析结果更加清晰直观。这些方法是量化交易策略中技术分析的基础。