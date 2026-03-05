# Python金融分析与量化交易实战教程：P10：2-买点与卖点可视化分析 📈

![](img/111834fb4d7b9261d33c998039c42052_0.png)

![](img/111834fb4d7b9261d33c998039c42052_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点和卖点。我们将从数据预处理开始，逐步计算技术指标，并最终通过图表直观地展示交易信号。

![](img/111834fb4d7b9261d33c998039c42052_4.png)

![](img/111834fb4d7b9261d33c998039c42052_6.png)

---

![](img/111834fb4d7b9261d33c998039c42052_8.png)

## 第一步：导入工具包与读取数据

![](img/111834fb4d7b9261d33c998039c42052_10.png)

![](img/111834fb4d7b9261d33c998039c42052_12.png)

首先，我们需要导入必要的Python库并加载我们的股票数据。

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/111834fb4d7b9261d33c998039c42052_14.png)

接下来，我们读取包含股票历史价格的数据文件。这里假设数据的第一列是日期，我们将把它设置为索引。

![](img/111834fb4d7b9261d33c998039c42052_16.png)

![](img/111834fb4d7b9261d33c998039c42052_18.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/111834fb4d7b9261d33c998039c42052_20.png)

数据读取完成后，我们选择苹果公司的股票作为分析示例。

![](img/111834fb4d7b9261d33c998039c42052_22.png)

![](img/111834fb4d7b9261d33c998039c42052_24.png)

```python
apple_data = data[['AAPL']]
```

![](img/111834fb4d7b9261d33c998039c42052_26.png)

---

![](img/111834fb4d7b9261d33c998039c42052_28.png)

## 第二步：数据预处理

![](img/111834fb4d7b9261d33c998039c42052_30.png)

![](img/111834fb4d7b9261d33c998039c42052_32.png)

在开始分析之前，我们需要对数据进行预处理，以确保数据的完整性和准确性。一个常见的步骤是处理缺失值。

以下是处理缺失值的步骤：
1.  检查数据中是否存在缺失值。
2.  使用 `dropna()` 方法移除含有缺失值的行。

![](img/111834fb4d7b9261d33c998039c42052_34.png)

```python
apple_data_clean = apple_data.dropna()
print(apple_data_clean.head())
```

经过预处理，我们得到了一个干净、只包含苹果股价的数据集，为后续计算做好了准备。

---

![](img/111834fb4d7b9261d33c998039c42052_36.png)

## 第三步：计算移动平均线

![](img/111834fb4d7b9261d33c998039c42052_38.png)

上一节我们准备好了数据，本节中我们来看看如何计算技术指标。移动平均线是分析股票趋势的基础工具。我们将计算短期和长期简单移动平均线。

![](img/111834fb4d7b9261d33c998039c42052_40.png)

![](img/111834fb4d7b9261d33c998039c42052_42.png)

首先，定义短期和长期的窗口大小。考虑到我们的数据跨度较长（约7-8年），我们将窗口设置得稍大一些，以便在图表中更清晰地观察趋势。

![](img/111834fb4d7b9261d33c998039c42052_44.png)

```python
short_window = 40
long_window = 100
```

![](img/111834fb4d7b9261d33c998039c42052_46.png)

![](img/111834fb4d7b9261d33c998039c42052_48.png)

接下来，我们使用Pandas的 `rolling()` 和 `mean()` 方法分别计算短期和长期移动平均线，并将结果作为新列添加到数据框中。

![](img/111834fb4d7b9261d33c998039c42052_50.png)

```python
apple_data_clean['SMA_short'] = apple_data_clean['AAPL'].rolling(window=short_window).mean()
apple_data_clean['SMA_long'] = apple_data_clean['AAPL'].rolling(window=long_window).mean()
```

![](img/111834fb4d7b9261d33c998039c42052_52.png)

![](img/111834fb4d7b9261d33c998039c42052_54.png)

计算完成后，我们可以查看数据的尾部，确认移动平均线已成功计算。

![](img/111834fb4d7b9261d33c998039c42052_56.png)

```python
print(apple_data_clean.tail())
```

![](img/111834fb4d7b9261d33c998039c42052_58.png)

---

## 第四步：可视化股价与移动平均线

有了移动平均线数据，我们现在可以将其可视化，以直观地观察股价趋势和均线交叉点。

![](img/111834fb4d7b9261d33c998039c42052_60.png)

![](img/111834fb4d7b9261d33c998039c42052_62.png)

我们使用Matplotlib绘制原始股价以及短期和长期移动平均线。

![](img/111834fb4d7b9261d33c998039c42052_64.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data_clean['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data_clean['SMA_short'], label=f'Short SMA ({short_window} days)', color='red')
plt.plot(apple_data_clean['SMA_long'], label=f'Long SMA ({long_window} days)', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/111834fb4d7b9261d33c998039c42052_66.png)

![](img/111834fb4d7b9261d33c998039c42052_68.png)

在生成的图表中，我们可以观察到：
*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**。

![](img/111834fb4d7b9261d33c998039c42052_70.png)

---

![](img/111834fb4d7b9261d33c998039c42052_72.png)

## 第五步：标记交易信号

为了量化这些交叉点，我们需要创建一个标志位来明确指示短期均线是高于还是低于长期均线。

![](img/111834fb4d7b9261d33c998039c42052_74.png)

首先，我们需要移除在计算移动平均线时产生的缺失值。

![](img/111834fb4d7b9261d33c998039c42052_76.png)

```python
apple_data_signals = apple_data_clean.dropna()
```

![](img/111834fb4d7b9261d33c998039c42052_78.png)

然后，我们使用 `np.where()` 函数创建 `Position` 列。当短期均线高于长期均线时，标记为1；否则标记为0。

```python
import numpy as np
apple_data_signals['Position'] = np.where(apple_data_signals['SMA_short'] > apple_data_signals['SMA_long'], 1, 0)
print(apple_data_signals.head())
```

![](img/111834fb4d7b9261d33c998039c42052_80.png)

现在，数据框中多了一个 `Position` 列，值为1代表短期趋势强于长期趋势（潜在持有或买入期），值为0则代表相反。

![](img/111834fb4d7b9261d33c998039c42052_82.png)

我们也可以将这个交易信号可视化到之前的图表上。

![](img/111834fb4d7b9261d33c998039c42052_84.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data_signals['AAPL'], label='Price')
plt.plot(apple_data_signals['SMA_short'], label='Short SMA')
plt.plot(apple_data_signals['SMA_long'], label='Long SMA')
# 在Position值变化的位置标记买卖点（简化示例）
plt.fill_between(apple_data_signals.index, plt.ylim()[0], plt.ylim()[1],
                 where=apple_data_signals['Position'] == 1,
                 color='green', alpha=0.3, label='Buy/Hold Signal')
plt.legend()
plt.show()
```

---

## 第六步：理解策略逻辑

最后，我们来探讨基于此策略的交易逻辑。单纯持有股票会经历所有的涨跌。而双均线策略旨在通过信号进行择时交易：
*   当出现 **“黄金交叉”**（`Position` 从0变为1）时，买入或持有股票。
*   当出现 **“死亡交叉”**（`Position` 从1变为0）时，卖出股票以规避下跌风险。

这样做的目标是**在上涨趋势中持有，在下跌趋势前退出**，从而可能获得比单纯买入并持有策略更高的风险调整后收益。下一节我们可以基于这个 `Position` 列来模拟具体的交易并计算回报率。

![](img/111834fb4d7b9261d33c998039c42052_86.png)

---

![](img/111834fb4d7b9261d33c998039c42052_88.png)

本节课中我们一起学习了金融数据分析的关键步骤：从数据导入预处理，到计算移动平均线指标，再到将买卖信号可视化并标记出来。我们利用苹果公司的股价数据，演示了如何通过短期和长期均线的交叉来识别潜在的交易机会，为构建一个简单的量化交易策略奠定了坚实的基础。