# 基于Python的股票分析与量化交易入门到实践 - P16：4.1：量化交易开发Numpy应用-股价分析实战

## 📊 概述
在本节课中，我们将学习如何使用Python的NumPy库进行股价的统计分析。我们将从理解股价数据的基本结构开始，然后介绍几个关键的股价分析指标及其计算方法，最后通过实际的代码演示，一步步实现这些指标的计算。

---

## 📈 数据样本说明
在开始分析之前，我们首先需要了解股价数据的基本结构。典型的股价数据通常包含以下七列信息：

*   **股票代码**：用于唯一标识一只股票。
*   **交易日期**：记录交易发生的时间，是时间序列分析的基础。
*   **收盘价**：交易日结束时的最终成交价格。
*   **开盘价**：交易日开始时的第一笔成交价格。
*   **最高价**：交易日内达到的最高成交价格。
*   **最低价**：交易日内达到的最低成交价格。
*   **成交量**：交易日内成交的股票总数量。

在NumPy中，我们可以通过下标（从0开始）来访问这些列。例如，收盘价通常是第3列（下标为2），成交量是最后一列（下标为6）。本节课我们将使用平安银行在2019年初约20个交易日的示例数据进行演示。

---

## 🎯 核心股价指标介绍
上一节我们了解了数据的基本结构，本节中我们来看看用于分析股价的几个核心统计指标。这些指标可以帮助我们量化股票的表现和风险。

以下是本节课将要计算的几个关键指标：

1.  **极差**：用于衡量股价在特定周期内的波动幅度。计算公式为：`极差 = 最高价的最大值 - 最低价的最小值`。极差值越大，表明股价在该周期内波动越剧烈。
2.  **成交量加权平均价格**：这是一个重要的金融参考指标，代表了金融资产的平均价格，其计算考虑了成交量因素。公式可以表示为：`VWAP = Σ(收盘价 * 成交量) / Σ(成交量)`。
3.  **对数收益率**：用于计算价格的变化率，是后续计算波动率的基础。对于价格序列P，对数收益率的计算公式为：`log_return = log(P_t / P_{t-1})`，这等价于 `log(P_t) - log(P_{t-1})`。
4.  **波动率**：用于衡量价格变动的风险。我们通常计算**年化波动率**和**月化波动率**。年化波动率的一种常见计算方法是：`年化波动率 = 对数收益率的标准差 / 对数收益率的均值 * sqrt(250)`，其中250代表一年的近似交易日数。

---

## 💻 实战编码：使用NumPy计算股价指标
理论介绍完毕，现在让我们进入实战环节，使用NumPy库来实际计算上述指标。我们将按照数据读取、基础统计、高级指标的顺序进行。

### 步骤一：读取数据
首先，我们需要从CSV文件中加载股价数据。这里我们使用NumPy的 `loadtxt` 函数。

![](img/86f5f73df83f15361e62d19caae5a5b5_1.png)

```python
import numpy as np

# 定义文件路径
fname = ‘stock_data.csv‘

# 使用loadtxt读取数据
# delimiter: 分隔符，CSV文件通常是逗号‘,‘
# usecols: 指定要读取的列索引（收盘价第3列，成交量第7列）
# unpack: 为True时，将每列数据分别赋值给不同变量
close_price, volume = np.loadtxt(fname, delimiter=‘,‘, usecols=(2, 6), unpack=True)

print(“收盘价：“, close_price)
print(“成交量：“, volume)
```

### 步骤二：计算最高价与最低价
接下来，我们计算股价在样本期间内的最高价和最低价。这需要先读取最高价和最低价的数据列。

```python
# 读取最高价（第4列，下标3）和最低价（第5列，下标4）
high_price = np.loadtxt(fname, delimiter=‘,‘, usecols=(3,), unpack=True)
low_price = np.loadtxt(fname, delimiter=‘,‘, usecols=(4,), unpack=True)

# 计算最高价的最大值和最低价的最小值
highest = np.max(high_price)
lowest = np.min(low_price)

print(f“最高价：{highest:.2f}“)
print(f“最低价：{lowest:.2f}“)
```

### 步骤三：计算极差
极差反映了价格的波动范围。我们可以分别计算最高价序列和最低价序列的极差。

```python
# 计算最高价序列的极差（Peak to Peak）
high_range = np.ptp(high_price)
# 计算最低价序列的极差
low_range = np.ptp(low_price)

print(f“最高价极差：{high_range:.2f}“)
print(f“最低价极差：{low_range:.2f}“)
```

### 步骤四：计算平均价格与VWAP
现在我们来计算普通的平均收盘价和更专业的成交量加权平均价格。

```python
# 计算平均收盘价
mean_price = np.average(close_price)
print(f“平均收盘价：{mean_price:.2f}“)

# 计算成交量加权平均价格（VWAP）
# weight参数用于指定加权值，这里使用成交量
vwap = np.average(close_price, weights=volume)
print(f“成交量加权平均价格(VWAP)：{vwap:.2f}“)
```

### 步骤五：计算中位数与方差
中位数和方差是描述数据分布和离散程度的基本统计量。

```python
# 计算收盘价的中位数
median_price = np.median(close_price)
print(f“收盘价中位数：{median_price:.2f}“)

# 计算收盘价的方差
variance = np.var(close_price) # 方法一：使用np.var函数
# variance = close_price.var() # 方法二：使用数组的.var()方法
print(f“收盘价方差：{variance:.6f}“)
```

### 步骤六：计算收益率与波动率
最后，我们计算对数收益率，并基于此推导出年化和月化波动率。

```python
# 计算对数收益率
log_returns = np.diff(np.log(close_price))
print(“对数收益率：“, log_returns)

# 计算年化波动率
# 假设一年有250个交易日
annual_volatility = log_returns.std() / log_returns.mean() * np.sqrt(250)
print(f“年化波动率：{annual_volatility:.2%}“)

# 计算月化波动率（假设一个月有约21个交易日，250/12≈20.83）
monthly_volatility = log_returns.std() / log_returns.mean() * np.sqrt(250/12)
print(f“月化波动率：{monthly_volatility:.2%}“)
```

---

## 📝 本章总结
本节课中我们一起学习了使用NumPy进行股价统计分析的全过程。

![](img/86f5f73df83f15361e62d19caae5a5b5_3.png)

首先，我们介绍了股价数据的标准样本结构，通常包含股票代码、交易日期、四价（开、高、低、收）和成交量等七列核心信息。

接着，我们详细讲解了几个关键的股价分析指标：用于衡量波动幅度的**极差**、考虑成交量的**成交量加权平均价格(VWAP)**、作为风险分析基础的**对数收益率**，以及最终衡量价格变动风险的**年化/月化波动率**。

最后，我们通过完整的代码实战，演示了如何利用NumPy高效地读取数据并计算所有这些指标。从基础的`max`、`min`、`average`，到专门的`ptp`（计算极差）、带权重的`average`（计算VWAP），再到结合`diff`、`log`、`std`计算收益率和波动率，NumPy为金融数据分析提供了强大而简洁的工具集。

![](img/86f5f73df83f15361e62d19caae5a5b5_5.png)

希望你能通过本课的练习，掌握使用NumPy处理金融数据的基本方法，并为进一步的量化分析打下坚实基础。