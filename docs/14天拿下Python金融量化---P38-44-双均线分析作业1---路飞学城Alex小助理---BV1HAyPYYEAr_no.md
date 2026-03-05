# Python金融量化：P38：双均线分析作业1 📈

在本节课中，我们将学习如何实现双均线分析。具体内容包括：获取股票数据、计算移动平均线（MA）、绘制价格与均线图，以及识别技术分析中的“金叉”和“死叉”信号。我们将使用两种方法来实现核心逻辑，一种是直观的循环方法，另一种是高效的向量化方法。

## 数据准备与导入 📊

首先，我们需要导入必要的库并加载股票数据。我们将使用`pandas`来处理数据，`numpy`进行数值计算。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 加载数据，假设数据文件已提前下载
df = pd.read_csv('601318.csv', index_col='date', parse_dates=True)
# 选择需要的列：开盘价、收盘价、最高价、最低价
df = df[['open', 'close', 'high', 'low']]
```

## 计算移动平均线 📉

移动平均线是分析股票趋势的重要指标。接下来，我们将计算5日移动平均线（MA5）和30日移动平均线（MA30）。

### 方法一：使用循环计算

对于初学者，使用循环来理解均线的计算过程是非常直观的。其核心思想是：对于每一天（从第N天开始），计算其前N个交易日收盘价的平均值。

**公式**：`MA(N)_t = (Close_{t-N+1} + ... + Close_t) / N`

以下是实现代码：

```python
# 创建两列，初始值为NaN（非数字，表示缺失值）
df['ma5'] = np.nan
df['ma30'] = np.nan

# 计算5日均线
for i in range(4, len(df)):
    df.loc[df.index[i], 'ma5'] = df['close'][i-4:i+1].mean()

# 计算30日均线
for i in range(29, len(df)):
    df.loc[df.index[i], 'ma30'] = df['close'][i-29:i+1].mean()
```

![](img/17b6753c4240bb37bc31a0f344fbb194_1.png)

### 方法二：使用Pandas的rolling函数

![](img/17b6753c4240bb37bc31a0f344fbb194_3.png)

Pandas库提供了更高效的方法来计算移动平均线，即`rolling()`函数配合`mean()`方法。`rolling(window=N)`会创建一个在数据上“滚动”的窗口，每次窗口包含N个数据点。

以下是实现代码：

```python
# 使用rolling方法计算移动平均线
df['ma5'] = df['close'].rolling(5).mean()
df['ma30'] = df['close'].rolling(30).mean()
```

![](img/17b6753c4240bb37bc31a0f344fbb194_5.png)

这种方法代码简洁，运行效率高，是实际数据分析中的首选。

![](img/17b6753c4240bb37bc31a0f344fbb194_7.png)

## 数据可视化 📊

![](img/17b6753c4240bb37bc31a0f344fbb194_9.png)

计算出均线后，我们可以将收盘价、MA5和MA30绘制在同一张图上，直观地观察它们的走势关系。

![](img/17b6753c4240bb37bc31a0f344fbb194_11.png)

```python
# 绘制收盘价与两条均线
df[['close', 'ma5', 'ma30']].plot()
plt.show()
```

如果数据量过大导致图形密集，可以截取部分数据进行绘制，例如前100个交易日的数据：

![](img/17b6753c4240bb37bc31a0f344fbb194_13.png)

```python
df_sample = df.head(100)
df_sample[['close', 'ma5', 'ma30']].plot()
plt.show()
```

## 识别金叉与死叉信号 🔍

![](img/17b6753c4240bb37bc31a0f344fbb194_15.png)

![](img/17b6753c4240bb37bc31a0f344fbb194_17.png)

“金叉”和“死叉”是技术分析中的重要信号。简单来说：
*   **金叉**：短期均线（如MA5）自下而上穿越长期均线（如MA30），通常被视为买入信号。
*   **死叉**：短期均线自上而下穿越长期均线，通常被视为卖出信号。

![](img/17b6753c4240bb37bc31a0f344fbb194_19.png)

在识别信号前，需要先剔除包含缺失值（NaN）的行，因为均线前期的值不存在。

```python
# 删除包含NaN值的行
df_clean = df.dropna()
```

### 方法一：使用循环判断

通过遍历每一天，比较当天和前一天短期均线与长期均线的大小关系来判断交叉点。

![](img/17b6753c4240bb37bc31a0f344fbb194_21.png)

以下是判断逻辑的代码实现：

![](img/17b6753c4240bb37bc31a0f344fbb194_23.png)

![](img/17b6753c4240bb37bc31a0f344fbb194_25.png)

```python
# 初始化两个列表，用于存储金叉和死叉的日期
golden_cross_dates = []
death_cross_dates = []

for i in range(1, len(df_clean)):
    # 获取当前和前一天的均线值
    ma5_today = df_clean['ma5'].iloc[i]
    ma30_today = df_clean['ma30'].iloc[i]
    ma5_yesterday = df_clean['ma5'].iloc[i-1]
    ma30_yesterday = df_clean['ma30'].iloc[i-1]

    # 判断金叉：昨天 MA5 <= MA30，今天 MA5 > MA30
    if (ma5_yesterday <= ma30_yesterday) and (ma5_today > ma30_today):
        golden_cross_dates.append(df_clean.index[i])
    # 判断死叉：昨天 MA5 >= MA30，今天 MA5 < MA30
    elif (ma5_yesterday >= ma30_yesterday) and (ma5_today < ma30_today):
        death_cross_dates.append(df_clean.index[i])
```

![](img/17b6753c4240bb37bc31a0f344fbb194_27.png)

### 方法二：使用向量化操作判断

![](img/17b6753c4240bb37bc31a0f344fbb194_29.png)

这是一种更高级、更高效的方法。其核心思想是利用布尔序列的移位（`shift`）操作来定位交叉点。

1.  创建两个布尔序列，分别表示“MA5是否小于等于MA30”和“MA5是否大于MA30”。
2.  通过逻辑运算和移位，找出从`False`变为`True`或从`True`变为`False`的转折点。

![](img/17b6753c4240bb37bc31a0f344fbb194_31.png)

![](img/17b6753c4240bb37bc31a0f344fbb194_33.png)

以下是实现代码：

![](img/17b6753c4240bb37bc31a0f344fbb194_35.png)

![](img/17b6753c4240bb37bc31a0f344fbb194_37.png)

```python
# 创建布尔序列
s1 = df_clean['ma5'] <= df_clean['ma30']  # MA5小于等于MA30
s2 = df_clean['ma5'] > df_clean['ma30']   # MA5大于MA30

![](img/17b6753c4240bb37bc31a0f344fbb194_39.png)

![](img/17b6753c4240bb37bc31a0f344fbb194_40.png)

# 识别死叉：昨天s2为True（MA5>MA30），今天s1为True（MA5<=MA30）
# 即：s1为True，且s2.shift(1)也为True
death_cross_mask = s1 & s2.shift(1)
death_cross_dates_vec = df_clean.index[death_cross_mask].tolist()

# 识别金叉：昨天s1为True（MA5<=MA30），今天s2为True（MA5>MA30）
# 即：s2为True，且s1.shift(1)也为True
golden_cross_mask = s2 & s1.shift(1)
golden_cross_dates_vec = df_clean.index[golden_cross_mask].tolist()
```

## 总结 📝

本节课中我们一起学习了双均线分析的完整流程：
1.  **数据准备**：导入并清洗股票数据。
2.  **计算均线**：掌握了使用`for`循环和`pandas.rolling`两种方法计算移动平均线。
3.  **数据可视化**：绘制了收盘价与均线的走势图。
4.  **信号识别**：深入理解了“金叉”和“死叉”的概念，并学会了用循环和向量化两种方法在数据中自动识别这些关键信号。

![](img/17b6753c4240bb37bc31a0f344fbb194_42.png)

循环方法有助于理解底层逻辑，而向量化方法（如`rolling`和布尔索引）则更加简洁高效，是进行量化分析推荐使用的技巧。