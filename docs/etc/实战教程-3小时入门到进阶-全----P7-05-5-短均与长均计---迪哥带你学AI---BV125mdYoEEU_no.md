# Python金融分析与量化交易实战教程：P7：05-5-短均与长均计算实例 📈

![](img/12ca26e764bb6bef7c16718a80ceab9e_1.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_3.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的技术分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种关键信号，并利用Python进行可视化分析。

![](img/12ca26e764bb6bef7c16718a80ceab9e_5.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_7.png)

## 计算短期与长期移动平均线

![](img/12ca26e764bb6bef7c16718a80ceab9e_9.png)

上一节我们介绍了移动平均线的概念，本节中我们来看看如何具体计算并应用它们。核心策略是计算两个不同时间窗口的移动平均线：短期均线和长期均线。当短期均线从下方上穿长期均线时，形成“黄金交叉”，通常被视为买入信号；反之，当短期均线从上方下穿长期均线时，形成“死亡交叉”，通常被视为卖出信号。

以下是计算步骤：

![](img/12ca26e764bb6bef7c16718a80ceab9e_11.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_13.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_15.png)

1.  **选择股票数据**：我们将使用苹果公司（AAPL）的历史股价数据作为分析对象。
2.  **定义时间窗口**：通常，短期窗口可设为5天（一周交易日），长期窗口可设为20或30天（约一个月交易日）。由于我们的数据时间跨度较长，为让图表更清晰，我们将短期窗口设为10天，长期窗口设为30天。
3.  **计算移动平均**：使用Pandas的滚动窗口函数 `.rolling()` 配合 `.mean()` 方法来计算指定窗口内的平均价格。

![](img/12ca26e764bb6bef7c16718a80ceab9e_17.png)

核心计算公式如下：
`短期均线 = 股价序列.rolling(window=短期窗口).mean()`
`长期均线 = 股价序列.rolling(window=长期窗口).mean()`

![](img/12ca26e764bb6bef7c16718a80ceab9e_19.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_21.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_23.png)

对应的Python代码如下：
```python
# 假设 df[‘AAPL‘] 是苹果公司的股价序列
short_window = 10
long_window = 30

![](img/12ca26e764bb6bef7c16718a80ceab9e_25.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_27.png)

df[‘M1‘] = df[‘AAPL‘].rolling(window=short_window).mean()  # 短期均线
df[‘M2‘] = df[‘AAPL‘].rolling(window=long_window).mean()   # 长期均线
```

## 可视化均线与交叉信号

![](img/12ca26e764bb6bef7c16718a80ceab9e_29.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_31.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_33.png)

计算完成后，我们需要将结果可视化，以便直观地观察交叉点。

![](img/12ca26e764bb6bef7c16718a80ceab9e_35.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_37.png)

以下是绘图与分析步骤：

![](img/12ca26e764bb6bef7c16718a80ceab9e_39.png)

1.  **绘制基础图表**：在同一张图上绘制股价曲线、短期均线（M1）和长期均线（M2）。
2.  **识别交叉点**：在图表上观察M1与M2的交叉情况。M1上穿M2为黄金交叉点，M1下穿M2为死亡交叉点。
3.  **优化图表显示**：由于数据周期长，均线波动频繁，可以截取特定时间段（如一年）的数据进行绘图，使交叉信号更清晰。

![](img/12ca26e764bb6bef7c16718a80ceab9e_41.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_43.png)

通过观察图表，我们可以手动定位交叉点，并理解其市场含义：黄金交叉点之后，股价往往呈上升趋势；死亡交叉点之后，股价往往呈下跌趋势。

![](img/12ca26e764bb6bef7c16718a80ceab9e_45.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_47.png)

## 使用信号指标进行增强分析

![](img/12ca26e764bb6bef7c16718a80ceab9e_49.png)

为了更精确、自动化地标识交叉区域，我们可以创建一个信号指标。

![](img/12ca26e764bb6bef7c16718a80ceab9e_51.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_53.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_55.png)

以下是创建信号指标的步骤：

1.  **定义信号规则**：我们使用NumPy的 `where` 函数来创建一个人工信号。当短期均线（M1）大于长期均线（M2）时，信号值为1，代表“持有”或“看涨”区域；当M1小于M2时，信号值为-1，代表“卖出”或“看跌”区域。
    `信号值 = np.where(短期均线 > 长期均线, 1, -1)`
2.  **绘制双轴图表**：由于信号值（1和-1）与股价、均线的数值范围差异巨大，直接绘制在同一坐标轴上会难以观察。因此，我们使用双Y轴图表，左边主Y轴显示股价和均线，右边次Y轴显示信号指标线。

![](img/12ca26e764bb6bef7c16718a80ceab9e_57.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_59.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_61.png)

对应的Python代码如下：
```python
import numpy as np

# 生成信号列
df[‘position‘] = np.where(df[‘M1‘] > df[‘M2‘], 1, -1)

![](img/12ca26e764bb6bef7c16718a80ceab9e_63.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_65.png)

# 创建带次坐标轴的图表
ax = df[[‘AAPL‘, ‘M1‘, ‘M2‘]].plot(figsize=(12,6), secondary_y=[‘position‘])
ax.set_ylabel(‘Price‘)
ax.right_ax.set_ylabel(‘Signal (1/-1)‘)
```
在这张增强图表中，信号线在1和-1之间切换。信号线从-1向上突破到1的转折点，对应着图表中的“黄金交叉”；从1向下跌破到-1的转折点，则对应着“死亡交叉”。这使得买卖信号的识别变得一目了然。

![](img/12ca26e764bb6bef7c16718a80ceab9e_67.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_69.png)

![](img/12ca26e764bb6bef7c16718a80ceab9e_71.png)

本节课中我们一起学习了如何计算股票的短期和长期移动平均线，并通过可视化方法识别“黄金交叉”与“死亡交叉”。我们还进一步创建了一个量化的信号指标，利用双轴图表清晰展示了趋势的强弱转换区域。这是构建量化交易策略最基础也是最重要的一步。