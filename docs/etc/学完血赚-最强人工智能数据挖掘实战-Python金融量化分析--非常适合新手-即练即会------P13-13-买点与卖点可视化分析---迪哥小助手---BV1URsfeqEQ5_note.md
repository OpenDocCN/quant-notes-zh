# 金融量化分析：P13：买点与卖点可视化分析 📈

![](img/9b65478c61f3d4635c43cd2f41f7ef44_0.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_2.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_3.png)

在本节课中，我们将学习如何利用Python进行金融数据可视化分析，核心是计算股票的短期与长期移动平均线，并通过它们的交叉点来识别潜在的买入和卖出信号。我们将以苹果公司的股价数据为例，一步步完成数据预处理、指标计算和策略可视化。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_4.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_6.png)

## 数据准备与预处理

![](img/9b65478c61f3d4635c43cd2f41f7ef44_7.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_9.png)

首先，我们需要导入必要的工具包并加载数据。这一步是后续所有分析的基础。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_11.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/9b65478c61f3d4635c43cd2f41f7ef44_12.png)

# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_14.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_15.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_16.png)

加载数据后，我们选择苹果公司（AAPL）的股价数据作为分析对象，并处理数据中可能存在的缺失值，以确保分析的准确性。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_17.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_19.png)

以下是具体步骤：
1.  从数据集中提取苹果公司的股价列。
2.  使用 `dropna()` 方法移除该列中的所有缺失值。

```python
# 选择苹果公司的股价列，并删除缺失值
apple_data = data[['AAPL']].dropna()
print(apple_data.head())
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_21.png)

## 计算移动平均线 📊

![](img/9b65478c61f3d4635c43cd2f41f7ef44_22.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_23.png)

上一节我们准备好了干净的数据，本节中我们来看看如何计算关键的指标——移动平均线。移动平均线是技术分析中常用的工具，用于平滑股价波动并识别趋势。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_25.png)

我们需要定义短期和长期的窗口大小，并计算相应的简单移动平均（SMA）。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_27.png)

```python
# 定义短期和长期窗口大小
short_window = 40
long_window = 100

# 计算短期移动平均线
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()

![](img/9b65478c61f3d4635c43cd2f41f7ef44_29.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_30.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_31.png)

# 计算长期移动平均线
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_32.png)

计算完成后，数据的前几行会包含很多 `NaN` 值，这是因为移动平均计算需要足够的窗口数据。我们可以通过绘图来直观地观察股价与两条均线的关系。

```python
# 绘制股价与移动平均线
apple_data[['AAPL', 'SMA_short', 'SMA_long']].plot(figsize=(12, 6))
plt.show()
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_34.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_35.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_36.png)

在生成的图表中，短期均线（SMA_short）波动更剧烈，长期均线（SMA_long）更为平滑。两者的交叉点是我们关注的重点。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_37.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_38.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_39.png)

## 识别买卖信号 🚦

![](img/9b65478c61f3d4635c43cd2f41f7ef44_40.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_41.png)

通过图表我们已经能直观看到均线的交叉，本节我们将通过编程方式精确标记这些信号。核心是创建一个标志位，来记录短期均线是高于还是低于长期均线。

首先，我们需要移除计算移动平均线时产生的缺失值，以便进行后续的逻辑判断。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_43.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_44.png)

```python
# 删除因计算移动平均而产生的缺失值
apple_data.dropna(inplace=True)
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_45.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_46.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_47.png)

接着，我们创建一个名为 `position` 的新列。当短期均线上穿长期均线时（金叉），标记为1，代表潜在的买入信号；当短期均线下穿长期均线时（死叉），标记为0，代表潜在的卖出信号。

以下是创建信号标志位的代码：
```python
# 生成交易信号：短期均线大于长期均线时为1，否则为0
apple_data['position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
print(apple_data[['AAPL', 'SMA_short', 'SMA_long', 'position']].head())
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_49.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_50.png)

我们可以将这个信号标志位也绘制在图上，以便更清晰地观察策略的触发点。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_52.png)

```python
# 绘制股价、均线及买卖信号
fig, ax1 = plt.subplots(figsize=(10, 6))
ax1.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
ax1.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA')
ax1.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA')
ax1.set_ylabel('Price')

![](img/9b65478c61f3d4635c43cd2f41f7ef44_54.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_55.png)

# 在次坐标轴上显示买卖信号
ax2 = ax1.twinx()
ax2.plot(apple_data['position'], label='Trading Signal (1=Buy)', color='red', alpha=0.3)
ax2.set_ylabel('Signal')
ax1.legend(loc='upper left')
plt.show()
```

![](img/9b65478c61f3d4635c43cd2f41f7ef44_57.png)

## 策略逻辑与收益对比 💡

我们已经成功识别了买卖信号，本节我们来探讨这个策略背后的逻辑，并对比其与“买入并持有”策略的差异。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_59.png)

双均线交叉策略的核心思想是趋势跟踪：
*   **黄金交叉（金叉）**：当短期均线从下方上穿长期均线，表明上涨趋势可能开始，策略发出**买入**信号。
*   **死亡交叉（死叉）**：当短期均线从上方下穿长期均线，表明下跌趋势可能开始，策略发出**卖出**信号。

![](img/9b65478c61f3d4635c43cd2f41f7ef44_60.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_61.png)

假设我们初始有100元。在“买入并持有”策略下，我们从期初买入股票并一直持有到期末，最终收益完全取决于股价的涨跌。而在双均线策略下，我们根据信号在“持有股票”和“持有现金”两种状态间切换，旨在抓住主要上涨趋势，规避主要下跌趋势。

例如，股价从100元跌至80元：
*   **买入并持有**：亏损20元。
*   **双均线策略**：如果在100元时收到卖出信号，我们卖出股票持有现金；在80元时收到买入信号，我们再买入。这相当于我们锁定了20元的利润。

虽然这个简化模型未考虑交易成本、滑点等因素，但它清晰地展示了趋势跟踪策略如何试图通过择时来改善收益风险比。

---

![](img/9b65478c61f3d4635c43cd2f41f7ef44_63.png)

![](img/9b65478c61f3d4635c43cd2f41f7ef44_65.png)

本节课中我们一起学习了金融数据可视化分析的基本流程。我们从导入和预处理苹果公司股价数据开始，接着计算了短期与长期移动平均线，并通过图表和编程两种方式识别了基于均线交叉的买卖信号。最后，我们探讨了该策略试图通过捕捉趋势来超越简单“买入并持有”策略的逻辑思想。这为后续构建和回测更复杂的量化交易策略奠定了重要基础。