# Python金融量化投资分析：P43：双均线分析作业1 📈

在本节课中，我们将学习如何通过Python实现双均线分析。具体内容包括：获取股票数据、计算移动平均线、绘制价格与均线图，以及识别技术分析中的“金叉”和“死叉”信号。

---

## 数据获取与准备 📊

上一节我们介绍了作业要求，本节中我们来看看如何实现。首先，我们需要导入必要的库并获取股票数据。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 读取本地已保存的股票数据文件
df = pd.read_csv('601318.csv', index_col='date', parse_dates=True)
# 选取需要的列：开盘价、收盘价、最高价、最低价
df = df[['open', 'close', 'high', 'low']]
```

---

## 计算移动平均线 📉

获取数据后，下一步是计算5日移动平均线和30日移动平均线。移动平均线是分析股票趋势的重要工具。

以下是计算移动平均线的两种方法：

### 方法一：使用循环计算

此方法通过遍历数据，手动计算每个窗口的平均值。

```python
# 创建两个新列，初始值为NaN（缺失值）
df['ma5'] = np.nan
df['ma30'] = np.nan

# 计算5日均线
for i in range(4, len(df)):
    df.loc[df.index[i], 'ma5'] = df['close'][i-4:i+1].mean()

# 计算30日均线
for i in range(29, len(df)):
    df.loc[df.index[i], 'ma30'] = df['close'][i-29:i+1].mean()
```

### 方法二：使用Pandas的`rolling`方法

![](img/c96a2bc0e618e736dfeacc77d58c04dc_1.png)

Pandas提供了更简洁高效的`rolling`方法来计算移动平均。

![](img/c96a2bc0e618e736dfeacc77d58c04dc_3.png)

```python
# 使用rolling方法计算5日和30日移动平均
df['ma5'] = df['close'].rolling(5).mean()
df['ma30'] = df['close'].rolling(30).mean()
```

`rolling(5)`创建一个长度为5的滚动窗口，`.mean()`计算每个窗口内数据的平均值。这种方法代码更简洁，运行效率也更高。

---

![](img/c96a2bc0e618e736dfeacc77d58c04dc_5.png)

![](img/c96a2bc0e618e736dfeacc77d58c04dc_7.png)

## 绘制价格与均线图 🎨

计算完均线后，我们可以将收盘价与两条移动平均线绘制在同一张图上进行直观对比。

![](img/c96a2bc0e618e736dfeacc77d58c04dc_9.png)

```python
# 选取收盘价和两条均线这三列数据
df[['close', 'ma5', 'ma30']].plot()
plt.show()
```

![](img/c96a2bc0e618e736dfeacc77d58c04dc_11.png)

如果数据点过多导致图表不清晰，可以截取部分数据（如前100行）进行绘制。

```python
# 截取前100行数据绘图
df_sample = df.head(100)
df_sample[['close', 'ma5', 'ma30']].plot()
plt.show()
```

![](img/c96a2bc0e618e736dfeacc77d58c04dc_13.png)

在图中，收盘价线波动较大，5日均线较为平滑，30日均线则更加平缓。均线的交叉点（金叉和死叉）是技术分析关注的重点。

---

![](img/c96a2bc0e618e736dfeacc77d58c04dc_15.png)

## 识别金叉与死叉日期 🔍

![](img/c96a2bc0e618e736dfeacc77d58c04dc_17.png)

图表绘制完成后，我们需要精确找出所有“金叉”和“死叉”发生的日期。金叉指短期均线（如5日线）自下而上穿越长期均线（如30日线），通常被视为买入信号。死叉则相反，被视为卖出信号。

![](img/c96a2bc0e618e736dfeacc77d58c04dc_19.png)

在开始识别前，需要先剔除包含缺失值（NaN）的数据行。

```python
# 删除包含缺失值的行
df = df.dropna()
```

以下是识别交叉点的两种方法：

### 方法一：使用循环判断

![](img/c96a2bc0e618e736dfeacc77d58c04dc_21.png)

通过遍历数据，比较相邻两天的均线关系。

![](img/c96a2bc0e618e736dfeacc77d58c04dc_23.png)

![](img/c96a2bc0e618e736dfeacc77d58c04dc_25.png)

```python
# 初始化两个列表，用于存储金叉和死叉的日期
golden_cross_dates = []
death_cross_dates = []

for i in range(1, len(df)):
    # 获取当前日和前一日的均线值
    ma5_today = df['ma5'].iloc[i]
    ma30_today = df['ma30'].iloc[i]
    ma5_yesterday = df['ma5'].iloc[i-1]
    ma30_yesterday = df['ma30'].iloc[i-1]
    
    # 判断金叉：今日5日线 > 30日线，且昨日5日线 <= 30日线
    if (ma5_today > ma30_today) and (ma5_yesterday <= ma30_yesterday):
        golden_cross_dates.append(df.index[i])
    
    # 判断死叉：今日5日线 < 30日线，且昨日5日线 >= 30日线
    if (ma5_today < ma30_today) and (ma5_yesterday >= ma30_yesterday):
        death_cross_dates.append(df.index[i])
```

![](img/c96a2bc0e618e736dfeacc77d58c04dc_27.png)

### 方法二：使用向量化操作判断

![](img/c96a2bc0e618e736dfeacc77d58c04dc_29.png)

利用Pandas的布尔索引和`shift`方法，可以无需循环即可完成判断，代码更简洁高效。

```python
# 创建两个布尔序列
s1 = df['ma5'] < df['ma30']      # 今日5日线低于30日线
s2 = df['ma5'] >= df['ma30']     # 今日5日线高于或等于30日线

![](img/c96a2bc0e618e736dfeacc77d58c04dc_31.png)

# 识别死叉：昨日s2为True，今日s1为True (即由高于转为低于)
death_cross = df[(s1) & (s2.shift(1))].index.tolist()

![](img/c96a2bc0e618e736dfeacc77d58c04dc_33.png)

![](img/c96a2bc0e618e736dfeacc77d58c04dc_35.png)

# 识别金叉：昨日s1为True，今日s2为True (即由低于转为高于)
golden_cross = df[(s2) & (s1.shift(1))].index.tolist()
```

![](img/c96a2bc0e618e736dfeacc77d58c04dc_37.png)

这种方法的核心思想是：**金叉发生在短期均线从低于长期均线变为高于长期均线的时刻；死叉则相反**。通过`shift(1)`将昨日的布尔值序列与今日对比，即可定位交叉点。

![](img/c96a2bc0e618e736dfeacc77d58c04dc_39.png)

![](img/c96a2bc0e618e736dfeacc77d58c04dc_40.png)

---

## 总结 📝

本节课中我们一起学习了双均线分析的完整实现流程：

1.  **数据准备**：使用Pandas获取并整理股票数据。
2.  **计算均线**：掌握了通过循环和`rolling`函数两种方法计算移动平均线。
3.  **数据可视化**：使用Matplotlib绘制收盘价与移动平均线图。
4.  **信号识别**：深入理解了“金叉”和“死叉”的定义，并运用循环和向量化两种方法精准识别这些技术信号发生的日期。

![](img/c96a2bc0e618e736dfeacc77d58c04dc_42.png)

理解并实现双均线策略是量化交易的基础。循环方法易于理解，适合初学者；而向量化方法效率更高，是进行大数据分析时的首选。