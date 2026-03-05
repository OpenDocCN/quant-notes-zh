# Python金融分析与量化交易实战：P10：2-买点与卖点可视化分析 📈

![](img/110b26c795a0dd071217a800a50175d2_0.png)

![](img/110b26c795a0dd071217a800a50175d2_2.png)

![](img/110b26c795a0dd071217a800a50175d2_4.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点和卖点。我们将从数据预处理开始，逐步计算技术指标，并最终通过图表直观地展示交易信号。

![](img/110b26c795a0dd071217a800a50175d2_6.png)

---

![](img/110b26c795a0dd071217a800a50175d2_8.png)

![](img/110b26c795a0dd071217a800a50175d2_10.png)

## 数据准备与预处理

![](img/110b26c795a0dd071217a800a50175d2_12.png)

首先，我们需要导入必要的工具包并加载数据。以下是实现这一步骤的代码。

![](img/110b26c795a0dd071217a800a50175d2_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/110b26c795a0dd071217a800a50175d2_16.png)

![](img/110b26c795a0dd071217a800a50175d2_18.png)

# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/110b26c795a0dd071217a800a50175d2_20.png)

![](img/110b26c795a0dd071217a800a50175d2_22.png)

数据加载完成后，我们选择苹果公司的股票数据作为分析示例，并处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/110b26c795a0dd071217a800a50175d2_24.png)

```python
# 选择苹果公司的股票数据列
apple_data = data[['AAPL']].copy()

![](img/110b26c795a0dd071217a800a50175d2_26.png)

# 删除缺失值
apple_data.dropna(inplace=True)

![](img/110b26c795a0dd071217a800a50175d2_28.png)

![](img/110b26c795a0dd071217a800a50175d2_30.png)

# 查看处理后的数据前几行
print(apple_data.head())
```

![](img/110b26c795a0dd071217a800a50175d2_32.png)

---

![](img/110b26c795a0dd071217a800a50175d2_34.png)

## 计算移动平均线

上一节我们完成了数据的基本清洗。本节中，我们来看看如何计算技术分析中常用的指标——移动平均线。

![](img/110b26c795a0dd071217a800a50175d2_36.png)

移动平均线能平滑股价波动，帮助我们识别趋势。通常，我们会计算短期和长期两条均线，并通过它们的交叉点来判断买卖时机。

以下是计算短期和长期简单移动平均线（SMA）的步骤：

![](img/110b26c795a0dd071217a800a50175d2_38.png)

![](img/110b26c795a0dd071217a800a50175d2_40.png)

1.  **定义窗口期**：首先，我们需要设定短期和长期均线所涵盖的天数。考虑到我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察，我们将短期窗口设为40天，长期窗口设为100天。
    ```python
    short_window = 40
    long_window = 100
    ```
2.  **计算移动平均值**：使用Pandas的`rolling`和`mean`方法，在指定的窗口期内计算收盘价的平均值。
    ```python
    # 计算短期移动平均线
    apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
    # 计算长期移动平均线
    apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
    ```
3.  **处理初始缺失值**：由于移动平均计算需要足够的历史数据，序列前 `short_window-1` 和 `long_window-1` 个位置会产生`NaN`值。我们再次删除这些缺失值。
    ```python
    apple_data.dropna(inplace=True)
    # 查看添加了均线后的数据
    print(apple_data.tail())
    ```

![](img/110b26c795a0dd071217a800a50175d2_42.png)

![](img/110b26c795a0dd071217a800a50175d2_44.png)

---

![](img/110b26c795a0dd071217a800a50175d2_46.png)

## 可视化均线与交叉点

![](img/110b26c795a0dd071217a800a50175d2_48.png)

![](img/110b26c795a0dd071217a800a50175d2_50.png)

计算完均线后，我们可以通过绘图来直观地观察股价走势以及两条均线的交叉情况。

![](img/110b26c795a0dd071217a800a50175d2_52.png)

![](img/110b26c795a0dd071217a800a50175d2_54.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/110b26c795a0dd071217a800a50175d2_56.png)

在生成的图表中，我们可以寻找短期均线（红色）与长期均线（绿色）的交叉点：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

![](img/110b26c795a0dd071217a800a50175d2_58.png)

---

## 标记买卖信号位置

![](img/110b26c795a0dd071217a800a50175d2_60.png)

为了更系统地进行策略分析，我们需要在数据中明确标记出这些信号。本节我们将创建一个标志位来记录短期均线是否高于长期均线。

![](img/110b26c795a0dd071217a800a50175d2_62.png)

![](img/110b26c795a0dd071217a800a50175d2_64.png)

我们可以使用`numpy`的`where`函数来创建这个标志列：
*   当短期均线大于长期均线时，标记为 `1`（代表持有或买入状态）。
*   否则，标记为 `0`（代表卖出或空仓状态）。

![](img/110b26c795a0dd071217a800a50175d2_66.png)

![](img/110b26c795a0dd071217a800a50175d2_68.png)

```python
import numpy as np

![](img/110b26c795a0dd071217a800a50175d2_70.png)

# 创建位置标志位
apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)

![](img/110b26c795a0dd071217a800a50175d2_72.png)

# 查看带有标志位的数据
print(apple_data[['AAPL', 'SMA_short', 'SMA_long', 'Position']].head())
```

![](img/110b26c795a0dd071217a800a50175d2_74.png)

我们还可以将这个标志位绘制在图表上，以更清晰地展示策略的持仓变化。

![](img/110b26c795a0dd071217a800a50175d2_76.png)

```python
# 绘制股价、均线及持仓信号
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/110b26c795a0dd071217a800a50175d2_78.png)

# 第一个子图：股价和均线
ax1.plot(apple_data['AAPL'], label='Price', alpha=0.5)
ax1.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA')
ax1.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Apple Stock Price and Moving Averages')

# 第二个子图：持仓信号（0或1）
ax2.plot(apple_data['Position'], label='Trading Signal', color='gray', drawstyle='steps-post')
ax2.set_ylabel('Position (0=Sell, 1=Buy)')
ax2.set_xlabel('Date')
ax2.legend()
ax2.set_ylim([-0.1, 1.1])

![](img/110b26c795a0dd071217a800a50175d2_80.png)

plt.show()
```

![](img/110b26c795a0dd071217a800a50175d2_82.png)

---

![](img/110b26c795a0dd071217a800a50175d2_84.png)

## 策略逻辑与初步思考

通过上述可视化，我们已经能够识别出基于双均线交叉的买卖点。这引出了一个核心的交易策略思想：**在黄金交叉点买入，在死亡交叉点卖出**。

与“买入并持有”的被动策略不同，这种主动策略旨在通过捕捉市场波动来获取超额收益。其核心逻辑是：
*   当短期趋势强于长期趋势（黄金交叉）时入场，期望抓住上涨波段。
*   当短期趋势转弱（死亡交叉）时离场，以规避下跌风险。

理论上，一个完美的趋势跟踪系统可以让我们在股价下跌前卖出，并在上涨开始时买入，从而实现“永远盈利”。当然，实际应用中还需考虑交易成本、信号滞后性等因素。在接下来的课程中，我们将基于这个`Position`标志位，具体计算这种策略的回报率，并与简单持有策略进行对比。

![](img/110b26c795a0dd071217a800a50175d2_86.png)

---

![](img/110b26c795a0dd071217a800a50175d2_88.png)

本节课中我们一起学习了金融数据可视化分析的关键步骤：从数据预处理、计算移动平均线，到可视化展示及标记交易信号。我们掌握了如何利用`pandas`进行滚动计算，使用`matplotlib`绘制专业图表，并初步理解了双均线交叉策略的逻辑基础。这为后续量化策略的回测与评估打下了坚实的基础。