# Python金融分析+量化交易：P13：买点与卖点可视化分析 📈

![](img/0b117222e931c2ccaf152f87e6a8ae4f_0.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_2.png)

在本节课中，我们将学习如何利用双移动平均线策略，对股票数据进行可视化分析，并识别出潜在的买入和卖出信号。我们将从数据预处理开始，计算短期和长期均线，最终通过图表清晰地展示交易信号。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_4.png)

---

![](img/0b117222e931c2ccaf152f87e6a8ae4f_6.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_8.png)

## 数据准备与预处理

![](img/0b117222e931c2ccaf152f87e6a8ae4f_10.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_14.png)

我们将使用之前课程中处理过的数据文件 `data.csv`，并指定第一列（时间）为索引。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_16.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_18.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_20.png)

为了进行清晰的演示，我们选择苹果公司（AAPL）的股价数据作为分析对象。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_22.png)

```python
stock_data = data[['AAPL']].copy()
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_24.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_26.png)

数据中可能存在缺失值，这会影响后续移动平均线的计算。因此，第一步是去除这些缺失值。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_28.png)

```python
stock_data.dropna(inplace=True)
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_30.png)

完成以上步骤后，我们就得到了一个干净、只包含苹果股价的 `DataFrame`，可以用于后续分析。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_32.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_34.png)

---

## 计算短期与长期移动平均线

![](img/0b117222e931c2ccaf152f87e6a8ae4f_36.png)

上一节我们准备好了数据，本节中我们来看看如何计算关键的指标——移动平均线。

移动平均线是分析股票趋势的常用工具。短期均线对价格变化更敏感，长期均线则反映更稳定的趋势。两者的交叉点常被视为交易信号。

我们需要先定义短期和长期的窗口大小。由于我们的数据集跨度约为7-8年，为了在图表中更清晰地观察，我们将窗口设置得比通常的5日和20日更大一些。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_38.png)

```python
short_window = 40
long_window = 100
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_40.png)

接下来，我们使用 `rolling` 和 `mean` 方法分别计算短期和长期简单移动平均线（SMA），并将结果作为新列添加到数据中。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_42.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_44.png)

```python
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_46.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。我们可以查看数据的尾部来确认计算成功。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_48.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_50.png)

```python
print(stock_data.tail())
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_52.png)

---

![](img/0b117222e931c2ccaf152f87e6a8ae4f_54.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_56.png)

## 可视化均线与交叉点

![](img/0b117222e931c2ccaf152f87e6a8ae4f_58.png)

计算完均线后，我们可以通过绘图来直观地观察股价走势以及均线的交叉情况。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_60.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_62.png)

以下是绘制股价与双均线的代码：

```python
stock_data.plot(figsize=(10, 6))
plt.show()
```

在生成的图表中，您将看到：
*   蓝色线代表苹果公司的原始股价。
*   橙色线代表 **`短期均线 (SMA_short)`**。
*   绿色线代表 **`长期均线 (SMA_long)`**。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_64.png)

观察图表，我们可以发现短期均线与长期均线存在多个交叉点。这些交叉点正是我们需要关注的交易信号：
*   **死亡交叉**：当短期均线从上向下穿过长期均线时（如图中第一个交叉点），通常被视为**卖出信号**。
*   **黄金交叉**：当短期均线从下向上穿过长期均线时（如图中后一个明显交叉点），通常被视为**买入信号**。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_66.png)

通过这张图，我们已经能够初步识别出市场中的潜在买点和卖点。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_68.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_70.png)

---

![](img/0b117222e931c2ccaf152f87e6a8ae4f_72.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_74.png)

## 标记交易信号

虽然从图上可以肉眼识别交叉点，但为了进行量化分析，我们需要在数据中明确标记出这些信号。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_76.png)

首先，我们需要剔除在计算移动平均线时产生的缺失值，以确保后续计算的一致性。

```python
stock_data.dropna(inplace=True)
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_78.png)

然后，我们创建一个新的标志列 `Position`。该列的值表示短期均线是否高于长期均线：
*   当 `SMA_short > SMA_long` 时，`Position = 1`，表示短期趋势强于长期趋势。
*   否则，`Position = 0`。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_80.png)

![](img/0b117222e931c2ccaf152f87e6a8ae4f_82.png)

我们可以使用 `numpy` 的 `where` 函数来实现这一逻辑：

```python
import numpy as np
stock_data['Position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
```

现在，查看数据头部，您可以看到新增的 `Position` 列。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_84.png)

```python
print(stock_data.head())
```

![](img/0b117222e931c2ccaf152f87e6a8ae4f_86.png)

我们还可以将买卖信号标记在之前的股价图上，使其更加一目了然（此处代码涉及双Y轴，可作为进阶练习）。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_88.png)

---

## 策略逻辑与收益思考

在标记了交易信号之后，我们来探讨一下基于此策略的交易逻辑。

如果不采用任何策略，只是简单地“买入并持有”，那么投资者的收益将完全取决于股价的最终涨跌。例如，初始100元投资可能随着股价波动变成80元或120元。

然而，双均线策略提供了动态操作的依据：
1.  当出现 **“黄金交叉”** (`Position` 从0变为1) 时，视为买入信号。
2.  当出现 **“死亡交叉”** (`Position` 从1变为0) 时，视为卖出信号。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_90.png)

理想情况下，通过这种“高卖低买”的波段操作，投资者可以在股价长期上涨趋势中获取比简单持有更高的收益，甚至在股价宽幅震荡时也能通过捕捉波动来盈利。例如，在价格高点（死亡交叉）卖出，在随后的低点（黄金交叉）再买回，从而实现利润积累。

![](img/0b117222e931c2ccaf152f87e6a8ae4f_92.png)

本节课中我们一起学习了如何利用双移动平均线策略对股票数据进行可视化分析。我们从数据预处理开始，计算了短期和长期移动平均线，并通过图表直观展示了“黄金交叉”和“死亡交叉”这两个关键的交易信号。最后，我们讨论了如何利用这些信号构建一个基础的量化交易策略逻辑。在接下来的课程中，我们将基于这些信号进行回测，计算策略的实际收益。