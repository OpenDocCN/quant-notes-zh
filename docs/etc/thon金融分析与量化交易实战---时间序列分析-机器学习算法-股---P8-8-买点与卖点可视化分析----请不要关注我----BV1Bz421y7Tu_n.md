# 人生苦短，我学量化！：P8：买点与卖点可视化分析

![](img/b112c289ce44c8da042296a29f714475_0.png)

![](img/b112c289ce44c8da042296a29f714475_2.png)

![](img/b112c289ce44c8da042296a29f714475_4.png)

## 概述
在本节课中，我们将学习如何利用Python进行股票数据的可视化分析，特别是通过计算短期和长期移动平均线来识别潜在的买入和卖出信号点。我们将从数据预处理开始，逐步计算均线，并最终通过图表直观地展示交易信号。

![](img/b112c289ce44c8da042296a29f714475_6.png)

![](img/b112c289ce44c8da042296a29f714475_8.png)

## 数据准备与预处理
首先，我们需要导入必要的工具包并加载数据。

![](img/b112c289ce44c8da042296a29f714475_10.png)

![](img/b112c289ce44c8da042296a29f714475_12.png)

![](img/b112c289ce44c8da042296a29f714475_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/b112c289ce44c8da042296a29f714475_16.png)

读取数据文件，并将第一列（时间）设置为索引，同时解析时间格式。

![](img/b112c289ce44c8da042296a29f714475_18.png)

![](img/b112c289ce44c8da042296a29f714475_20.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/b112c289ce44c8da042296a29f714475_22.png)

![](img/b112c289ce44c8da042296a29f714475_24.png)

我们选择苹果公司的股票数据作为本次分析的例子。

![](img/b112c289ce44c8da042296a29f714475_26.png)

![](img/b112c289ce44c8da042296a29f714475_28.png)

```python
apple_data = data[['AAPL']].copy()
```

![](img/b112c289ce44c8da042296a29f714475_30.png)

数据中可能存在缺失值，这会影响后续的窗口计算。因此，第一步是去除缺失值。

![](img/b112c289ce44c8da042296a29f714475_32.png)

![](img/b112c289ce44c8da042296a29f714475_34.png)

```python
apple_data = apple_data.dropna()
print(apple_data.head())
```

![](img/b112c289ce44c8da042296a29f714475_36.png)

## 计算移动平均线
上一节我们完成了数据清洗，本节中我们来看看如何计算关键的移动平均线指标。

我们需要定义短期和长期的窗口大小。通常，短期窗口为5天，长期窗口为20天。但由于我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们将适当增大窗口值。

![](img/b112c289ce44c8da042296a29f714475_38.png)

![](img/b112c289ce44c8da042296a29f714475_40.png)

以下是计算短期和长期移动平均线的步骤：
1.  定义短期和长期的窗口天数。
2.  使用 `rolling` 方法计算指定窗口内的均值。
3.  将计算结果作为新列添加到数据框中。

![](img/b112c289ce44c8da042296a29f714475_42.png)

```python
# 定义窗口大小
short_window = 40
long_window = 100

![](img/b112c289ce44c8da042296a29f714475_44.png)

![](img/b112c289ce44c8da042296a29f714475_46.png)

# 计算短期移动平均线
apple_data['short_mavg'] = apple_data['AAPL'].rolling(window=short_window, min_periods=1).mean()

![](img/b112c289ce44c8da042296a29f714475_48.png)

![](img/b112c289ce44c8da042296a29f714475_50.png)

# 计算长期移动平均线
apple_data['long_mavg'] = apple_data['AAPL'].rolling(window=long_window, min_periods=1).mean()
```

![](img/b112c289ce44c8da042296a29f714475_52.png)

![](img/b112c289ce44c8da042296a29f714475_54.png)

计算完成后，我们可以查看数据的尾部以确认结果。

![](img/b112c289ce44c8da042296a29f714475_56.png)

![](img/b112c289ce44c8da042296a29f714475_58.png)

```python
print(apple_data.tail())
```

![](img/b112c289ce44c8da042296a29f714475_60.png)

![](img/b112c289ce44c8da042296a29f714475_62.png)

## 可视化均线与交叉点
计算完均线后，我们可以通过绘图来直观地观察股价与两条均线的走势关系。

![](img/b112c289ce44c8da042296a29f714475_64.png)

```python
apple_data.plot(figsize=(12, 8))
plt.show()
```

在生成的图表中，我们可以观察到：
*   红色线代表苹果公司的原始股价。
*   其他颜色的线分别代表短期和长期移动平均线。
*   当短期均线从下方上穿长期均线时，形成“黄金交叉”，这通常被视为一个潜在的买入信号。
*   当短期均线从上方下穿长期均线时，形成“死亡交叉”，这通常被视为一个潜在的卖出信号。

![](img/b112c289ce44c8da042296a29f714475_66.png)

![](img/b112c289ce44c8da042296a29f714475_68.png)

## 标记买卖信号点
为了更系统地进行策略分析，我们需要在数据中明确标记出这些信号点。

![](img/b112c289ce44c8da042296a29f714475_70.png)

![](img/b112c289ce44c8da042296a29f714475_72.png)

![](img/b112c289ce44c8da042296a29f714475_74.png)

首先，需要再次去除在计算移动平均线过程中产生的新缺失值。

![](img/b112c289ce44c8da042296a29f714475_76.png)

```python
apple_data = apple_data.dropna()
```

![](img/b112c289ce44c8da042296a29f714475_78.png)

接着，我们创建一个新的列 `position` 作为标志位。当短期均线大于长期均线时，标记为1（代表持有或买入信号）；否则标记为0（代表卖出或观望信号）。

![](img/b112c289ce44c8da042296a29f714475_80.png)

```python
apple_data['position'] = np.where(apple_data['short_mavg'] > apple_data['long_mavg'], 1, 0)
print(apple_data.head())
```

![](img/b112c289ce44c8da042296a29f714475_82.png)

![](img/b112c289ce44c8da042296a29f714475_84.png)

我们也可以将这个标志位绘制在图表上，但由于其值是0和1，与股价尺度不同，需要用到双Y轴。

```python
fig, ax1 = plt.subplots(figsize=(10, 6))

![](img/b112c289ce44c8da042296a29f714475_86.png)

ax1.plot(apple_data['AAPL'], label='Price', color='red', alpha=0.5)
ax1.plot(apple_data['short_mavg'], label=f'Short {short_window} MA', color='blue')
ax1.plot(apple_data['long_mavg'], label=f'Long {long_window} MA', color='green')
ax1.set_ylabel('Price')

![](img/b112c289ce44c8da042296a29f714475_88.png)

![](img/b112c289ce44c8da042296a29f714475_90.png)

ax2 = ax1.twinx()
ax2.plot(apple_data['position'], label='Position (Buy=1)', color='orange', linestyle='--')
ax2.set_ylabel('Position Signal')
ax2.set_ylim(-0.1, 1.1)

![](img/b112c289ce44c8da042296a29f714475_92.png)

fig.legend(loc='upper left')
plt.show()
```

## 理解策略逻辑
通过上述分析，我们得到了明确的买卖信号。现在来探讨基于此信号的交易策略逻辑。

假设我们最初有100元本金：
*   **无策略情况**：如果在最初买入并一直持有到最后，资产的最终价值将完全跟随股价波动。如果股价下跌，资产就会缩水。
*   **双均线策略**：我们可以根据 `position` 标志位进行操作。当信号为1时买入或持有，当信号变为0时卖出。这样，我们可以在价格下跌趋势开始前卖出以锁定利润或减少亏损，并在上涨趋势开始时再次买入。

![](img/b112c289ce44c8da042296a29f714475_94.png)

![](img/b112c289ce44c8da042296a29f714475_96.png)

这种策略的核心优势在于，它试图通过跟随趋势来规避大的下跌风险，并捕捉主要的上涨波段。从理论上讲，相比简单的买入持有策略，它能带来更好的风险调整后收益。

![](img/b112c289ce44c8da042296a29f714475_98.png)

## 总结
本节课我们一起学习了股票量化分析中的一个基础但重要的环节——利用双移动平均线进行买卖点可视化分析。我们完成了从数据加载、清洗、计算移动平均线，到识别交叉信号并标记的全过程。通过图表，我们直观地看到了“黄金交叉”和“死亡交叉”如何指示潜在的买卖时机。最后，我们探讨了基于此信号构建趋势跟踪策略的基本逻辑，为后续的回测和策略评估打下了基础。