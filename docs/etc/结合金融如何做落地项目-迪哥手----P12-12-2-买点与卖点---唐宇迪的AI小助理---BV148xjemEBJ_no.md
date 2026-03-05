# Python金融分析与量化交易实战：12.2：买点与卖点可视化分析 📈

![](img/2d016940d08a1c968714b90e8d6f3fc0_0.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_2.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_4.png)

在本节课中，我们将学习如何基于双均线策略，对股票数据进行可视化分析，并识别出潜在的买入和卖出信号点。我们将使用苹果公司的股票数据作为示例，通过计算短期和长期移动平均线，并观察它们的交叉情况来制定交易策略。

![](img/2d016940d08a1c968714b90e8d6f3fc0_6.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_8.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_10.png)

## 第一步：导入工具包与读取数据

![](img/2d016940d08a1c968714b90e8d6f3fc0_12.png)

首先，我们需要导入必要的Python库并加载数据。我们将使用`pandas`进行数据处理，`matplotlib`进行可视化。

![](img/2d016940d08a1c968714b90e8d6f3fc0_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/2d016940d08a1c968714b90e8d6f3fc0_16.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_18.png)

接下来，读取包含多只股票历史价格的数据集。我们将指定第一列（日期）作为索引，并解析日期格式。

![](img/2d016940d08a1c968714b90e8d6f3fc0_20.png)

```python
data = pd.read_csv('your_data.csv', index_col=0, parse_dates=True)
```

![](img/2d016940d08a1c968714b90e8d6f3fc0_22.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_24.png)

数据加载完成后，我们选择苹果公司（AAPL）的股票价格列作为本次分析的对象。

![](img/2d016940d08a1c968714b90e8d6f3fc0_26.png)

```python
apple_data = data[['AAPL']]
```

![](img/2d016940d08a1c968714b90e8d6f3fc0_28.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_30.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_32.png)

## 第二步：数据预处理

在开始分析之前，我们需要处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/2d016940d08a1c968714b90e8d6f3fc0_34.png)

```python
apple_data_clean = apple_data.dropna()
print(apple_data_clean.head())
```

通过执行以上代码，我们得到了一个只包含苹果股票价格且无缺失值的数据集。

![](img/2d016940d08a1c968714b90e8d6f3fc0_36.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_38.png)

## 第三步：计算短期与长期移动平均线

![](img/2d016940d08a1c968714b90e8d6f3fc0_40.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_42.png)

上一节我们完成了数据准备，本节中我们来看看如何计算关键的技术指标——移动平均线。移动平均线能帮助我们平滑价格波动，识别趋势。

![](img/2d016940d08a1c968714b90e8d6f3fc0_44.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_46.png)

以下是计算移动平均线的步骤：

![](img/2d016940d08a1c968714b90e8d6f3fc0_48.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_50.png)

1.  **定义窗口期**：首先，我们需要设定短期和长期移动平均线的计算窗口。考虑到我们的数据集时间跨度较长（约7-8年），我们将使用较大的窗口以便在图表中更清晰地观察趋势。
    ```python
    short_window = 40
    long_window = 100
    ```
2.  **计算移动平均线**：使用`rolling`和`mean`方法分别计算短期和长期移动平均线，并将结果作为新列添加到数据框中。
    ```python
    apple_data_clean['SMA_short'] = apple_data_clean['AAPL'].rolling(window=short_window).mean()
    apple_data_clean['SMA_long'] = apple_data_clean['AAPL'].rolling(window=long_window).mean()
    ```
3.  **检查结果**：计算完成后，我们可以查看数据的尾部，确认移动平均线已成功计算。由于前`long_window-1`个数据点无法计算长期均线，这些位置会出现缺失值（NaN）。
    ```python
    print(apple_data_clean.tail())
    ```

![](img/2d016940d08a1c968714b90e8d6f3fc0_52.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_54.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_56.png)

## 第四步：可视化股价与移动平均线

![](img/2d016940d08a1c968714b90e8d6f3fc0_58.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_60.png)

计算完指标后，我们可以通过绘图来直观地观察股价与两条移动平均线的关系。

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data_clean['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(apple_data_clean['SMA_short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(apple_data_clean['SMA_long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

在生成的图表中，我们可以寻找短期均线（红线）与长期均线（绿线）的交叉点：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

![](img/2d016940d08a1c968714b90e8d6f3fc0_62.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_64.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_66.png)

## 第五步：标记均线位置关系并再次清理数据

![](img/2d016940d08a1c968714b90e8d6f3fc0_68.png)

![](img/2d016940d08a1c968714b90e8d6f3fc0_70.png)

为了量化交易信号，我们需要创建一个标志位来明确记录短期均线是高于还是低于长期均线。

![](img/2d016940d08a1c968714b90e8d6f3fc0_72.png)

首先，由于移动平均线的计算产生了前期的缺失值，我们需要再次清理数据。

![](img/2d016940d08a1c968714b90e8d6f3fc0_74.png)

```python
apple_data_final = apple_data_clean.dropna()
```

![](img/2d016940d08a1c968714b90e8d6f3fc0_76.png)

然后，使用`np.where`函数创建标志列`Position`。当短期均线大于长期均线时，标记为1；否则标记为0。

![](img/2d016940d08a1c968714b90e8d6f3fc0_78.png)

```python
import numpy as np
apple_data_final['Position'] = np.where(apple_data_final['SMA_short'] > apple_data_final['SMA_long'], 1, 0)
print(apple_data_final.head())
```

![](img/2d016940d08a1c968714b90e8d6f3fc0_80.png)

我们也可以将这个标志位绘制在图表上，以便更直观地观察策略信号。

```python
plt.figure(figsize=(14, 6))
plt.plot(apple_data_final['AAPL'], label='Price')
plt.plot(apple_data_final['SMA_short'], label='Short MA')
plt.plot(apple_data_final['SMA_long'], label='Long MA')

![](img/2d016940d08a1c968714b90e8d6f3fc0_82.png)

# 创建第二个y轴来显示买卖点位
plt.twinx()
plt.plot(apple_data_final['Position'], label='Position (Buy/Sell Signal)', color='gray', alpha=0.3)
plt.legend(loc='upper left')
plt.show()
```

![](img/2d016940d08a1c968714b90e8d6f3fc0_84.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_86.png)

## 第六步：理解策略逻辑

在进入具体的回测计算之前，让我们先理解双均线交叉策略的核心逻辑。

假设我们初始有100元本金：
*   **无策略情况（买入并持有）**：如果在股价高点（如100元）买入，在随后的低点（如80元）仍然持有，那么账户价值就是80元，**亏损20元**。
*   **有策略情况**：如果我们在死亡交叉（股价从100元开始下跌的信号）出现时以约100元卖出，然后在黄金交叉（股价在80元附近开始上涨的信号）出现时再以约80元买回。这样操作后，我们仍然持有1股股票，但账户里还多出了20元现金。**相当于盈利20元**。

因此，一个有效的交易策略目标是在市场下跌时通过卖出减少损失，并在市场上涨时通过买入参与增长，从而在长期获得比简单持有更好的收益。

![](img/2d016940d08a1c968714b90e8d6f3fc0_88.png)

---

![](img/2d016940d08a1c968714b90e8d6f3fc0_90.png)

本节课中我们一起学习了如何利用Python实现双均线策略的可视化分析。我们完成了从数据加载、预处理、计算移动平均线到识别买卖信号点的全过程。通过图表，我们能够清晰地看到黄金交叉和死亡交叉的出现位置，这为制定“在交叉点进行交易”的量化策略奠定了直观基础。在接下来的课程中，我们将基于这些信号进行策略回测，计算该策略的历史收益表现。