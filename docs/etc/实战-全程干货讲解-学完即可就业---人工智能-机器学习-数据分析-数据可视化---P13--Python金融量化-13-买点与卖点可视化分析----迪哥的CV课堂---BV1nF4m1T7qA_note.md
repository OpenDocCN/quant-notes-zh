# Python金融量化：13：买点与卖点可视化分析 📈

![](img/c497f6c9be1a315aee5fd567896382ac_0.png)

![](img/c497f6c9be1a315aee5fd567896382ac_2.png)

在本节课中，我们将学习如何利用Python进行金融量化分析，具体目标是实现股票买点与卖点的可视化。我们将通过计算短期和长期移动平均线，识别“黄金交叉”和“死亡交叉”，并以此为基础构建一个简单的交易策略，最后对比策略收益与“买入并持有”策略的收益差异。

![](img/c497f6c9be1a315aee5fd567896382ac_4.png)

---

![](img/c497f6c9be1a315aee5fd567896382ac_6.png)

## 第一步：导入工具包与读取数据

![](img/c497f6c9be1a315aee5fd567896382ac_8.png)

![](img/c497f6c9be1a315aee5fd567896382ac_10.png)

首先，我们需要导入必要的Python库并加载股票数据。

![](img/c497f6c9be1a315aee5fd567896382ac_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/c497f6c9be1a315aee5fd567896382ac_14.png)

以下是读取数据的具体步骤：
1.  读取名为 `data.csv` 的数据文件。
2.  将第一列指定为索引，该列代表时间。
3.  解析时间索引，确保其被正确识别为日期时间格式。

![](img/c497f6c9be1a315aee5fd567896382ac_16.png)

```python
# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/c497f6c9be1a315aee5fd567896382ac_18.png)

![](img/c497f6c9be1a315aee5fd567896382ac_20.png)

数据加载完成后，我们选择苹果公司（AAPL）的股价数据作为本次分析的示例。

![](img/c497f6c9be1a315aee5fd567896382ac_22.png)

```python
# 选择苹果公司的股价列
stock_data = data[['AAPL']].copy()
```

![](img/c497f6c9be1a315aee5fd567896382ac_24.png)

---

![](img/c497f6c9be1a315aee5fd567896382ac_26.png)

![](img/c497f6c9be1a315aee5fd567896382ac_28.png)

## 第二步：数据预处理

![](img/c497f6c9be1a315aee5fd567896382ac_30.png)

上一节我们导入了数据，本节中我们来看看如何为分析做准备。原始数据中可能存在缺失值，这会影响后续移动平均线的计算，因此第一步是清理数据。

![](img/c497f6c9be1a315aee5fd567896382ac_32.png)

以下是数据预处理的步骤：
*   **处理缺失值**：使用 `dropna()` 方法删除含有缺失值的行，确保数据完整性。

![](img/c497f6c9be1a315aee5fd567896382ac_34.png)

```python
# 删除缺失值
stock_data = stock_data.dropna()
```

![](img/c497f6c9be1a315aee5fd567896382ac_36.png)

处理完成后，我们便得到了一个干净、只包含苹果公司股价的 `DataFrame`，可以用于后续计算。

---

## 第三步：计算短期与长期移动平均线

![](img/c497f6c9be1a315aee5fd567896382ac_38.png)

![](img/c497f6c9be1a315aee5fd567896382ac_40.png)

数据准备就绪后，接下来我们需要计算技术指标。移动平均线是分析股价趋势的基础工具，本节我们将计算短期和长期简单移动平均线。

我们定义短期窗口为42天，长期窗口为252天。在大量历史数据中，较大的窗口值能使图表趋势更清晰。

![](img/c497f6c9be1a315aee5fd567896382ac_42.png)

![](img/c497f6c9be1a315aee5fd567896382ac_44.png)

计算公式如下：
*   **短期移动平均线 (SMA_short)** = 过去42日收盘价的平均值
*   **长期移动平均线 (SMA_long)** = 过去252日收盘价的平均值

![](img/c497f6c9be1a315aee5fd567896382ac_46.png)

在Pandas中，我们使用 `rolling()` 和 `mean()` 方法进行计算。

![](img/c497f6c9be1a315aee5fd567896382ac_48.png)

![](img/c497f6c9be1a315aee5fd567896382ac_50.png)

```python
# 定义短期和长期窗口
short_window = 42
long_window = 252

![](img/c497f6c9be1a315aee5fd567896382ac_52.png)

# 计算短期移动平均线
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()

![](img/c497f6c9be1a315aee5fd567896382ac_54.png)

![](img/c497f6c9be1a315aee5fd567896382ac_56.png)

# 计算长期移动平均线
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/c497f6c9be1a315aee5fd567896382ac_58.png)

计算初期会产生大量空值（NaN），因为需要累积足够的天数才能计算第一个平均值，这属于正常现象。我们可以查看数据尾部以确认计算成功。

![](img/c497f6c9be1a315aee5fd567896382ac_60.png)

![](img/c497f6c9be1a315aee5fd567896382ac_62.png)

---

![](img/c497f6c9be1a315aee5fd567896382ac_64.png)

## 第四步：可视化移动平均线与交叉点

计算出移动平均线后，我们可以通过图表直观地观察股价趋势以及两条均线的交叉情况。

```python
# 绘制股价与移动平均线
stock_data[['AAPL', 'SMA_short', 'SMA_long']].plot(figsize=(15, 8))
plt.show()
```

![](img/c497f6c9be1a315aee5fd567896382ac_66.png)

在生成的图表中：
*   **黄金交叉**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**。

![](img/c497f6c9be1a315aee5fd567896382ac_68.png)

![](img/c497f6c9be1a315aee5fd567896382ac_70.png)

通过观察图表，我们可以初步识别出这些潜在的交易点位。

![](img/c497f6c9be1a315aee5fd567896382ac_72.png)

---

![](img/c497f6c9be1a315aee5fd567896382ac_74.png)

![](img/c497f6c9be1a315aee5fd567896382ac_76.png)

## 第五步：标记交易信号

仅仅观察图表不够精确，我们需要在数据中明确标记出这些交叉点所代表的交易信号。为此，我们首先需要清理掉计算移动平均线时产生的缺失值。

![](img/c497f6c9be1a315aee5fd567896382ac_78.png)

以下是标记交易信号的步骤：
1.  **再次清理数据**：删除因计算移动平均线而产生的缺失值行。
2.  **创建信号列**：比较短期均线和长期均线。当短期均线大于长期均线时，标记为 `1`（代表持有或买入信号）；否则标记为 `0`（代表卖出或空仓信号）。

![](img/c497f6c9be1a315aee5fd567896382ac_80.png)

```python
# 删除计算移动平均线后产生的缺失值
stock_data = stock_data.dropna()

![](img/c497f6c9be1a315aee5fd567896382ac_82.png)

# 创建交易信号列
stock_data['Position'] = 0
stock_data['Position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
```

![](img/c497f6c9be1a315aee5fd567896382ac_84.png)

现在，数据中的 `Position` 列清晰地标明了每个时间点根据双均线策略应该采取的操作。

![](img/c497f6c9be1a315aee5fd567896382ac_86.png)

---

## 第六步：策略回测与收益对比

![](img/c497f6c9be1a315aee5fd567896382ac_88.png)

最后，我们将基于标记的交易信号进行简单的策略回测，并与其最简单的策略——“买入并持有”进行收益对比。

![](img/c497f6c9be1a315aee5fd567896382ac_90.png)

![](img/c497f6c9be1a315aee5fd567896382ac_92.png)

**核心思想**：
*   **双均线策略**：在 `Position` 从0变为1时（黄金交叉）全仓买入，从1变为0时（死亡交叉）全仓卖出。通过“高卖低买”的轮动，旨在获取超越单纯持有股票的收益。
*   **买入并持有策略**：在期初买入股票并一直持有到期末，其收益完全取决于股价的最终涨跌。

```python
# 计算双均线策略的每日收益率
stock_data['Strategy_Returns'] = stock_data['AAPL'].pct_change() * stock_data['Position'].shift(1)

# 计算买入并持有策略的每日收益率
stock_data['Buy_Hold_Returns'] = stock_data['AAPL'].pct_change()

# 计算累计收益
stock_data['Cumulative_Strategy'] = (1 + stock_data['Strategy_Returns']).cumprod()
stock_data['Cumulative_Buy_Hold'] = (1 + stock_data['Buy_Hold_Returns']).cumprod()

# 绘制累计收益对比图
stock_data[['Cumulative_Strategy', 'Cumulative_Buy_Hold']].plot(figsize=(15, 8))
plt.title('双均线策略 vs 买入并持有策略 累计收益对比')
plt.show()
```

通过对比两条累计收益曲线，我们可以直观地评估双均线交易策略在历史数据上的表现是否优于被动持有策略。

![](img/c497f6c9be1a315aee5fd567896382ac_94.png)

---

![](img/c497f6c9be1a315aee5fd567896382ac_96.png)

本节课中我们一起学习了金融量化分析的一个完整小案例：从数据加载、预处理开始，到计算移动平均线、识别并可视化交易信号，最后进行简单的策略回测与收益对比。这个过程展示了如何将基本的编程技能与金融概念结合，构建一个可量化、可验证的交易策略原型。