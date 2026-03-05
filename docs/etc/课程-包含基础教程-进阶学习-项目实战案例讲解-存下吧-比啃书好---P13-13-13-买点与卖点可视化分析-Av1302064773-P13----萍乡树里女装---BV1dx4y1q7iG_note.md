# 量化交易教程：13：买点与卖点可视化分析 📈

![](img/a5921577f4fae9f499ec1b718fd61f6e_0.png)

在本节课中，我们将学习如何利用移动平均线（均线）策略，对股票数据进行可视化分析，从而识别潜在的买入和卖出信号。我们将使用苹果公司的股价数据作为示例，通过计算短期和长期均线，并标记它们的交叉点，来直观地展示交易策略的核心思想。

![](img/a5921577f4fae9f499ec1b718fd61f6e_2.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_3.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_4.png)

---

![](img/a5921577f4fae9f499ec1b718fd61f6e_6.png)

## 数据准备与预处理

![](img/a5921577f4fae9f499ec1b718fd61f6e_7.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_9.png)

首先，我们需要导入必要的工具包并加载数据。以下是所需的步骤：

![](img/a5921577f4fae9f499ec1b718fd61f6e_11.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

![](img/a5921577f4fae9f499ec1b718fd61f6e_12.png)

# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_14.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_15.png)

接下来，我们选择苹果公司的股价数据作为分析对象，并处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/a5921577f4fae9f499ec1b718fd61f6e_16.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_17.png)

```python
# 选择苹果公司的股价列
data = data[['AAPL']].copy()
# 去除缺失值
data.dropna(inplace=True)
# 查看前几行数据
print(data.head())
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_19.png)

---

## 计算短期与长期移动平均线

![](img/a5921577f4fae9f499ec1b718fd61f6e_21.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_22.png)

上一节我们准备好了数据，本节中我们来看看如何计算关键的指标——移动平均线。移动平均线是分析股价趋势的常用工具。

![](img/a5921577f4fae9f499ec1b718fd61f6e_23.png)

我们需要定义短期和长期的时间窗口。考虑到我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们将短期窗口设置为42天，长期窗口设置为252天。

![](img/a5921577f4fae9f499ec1b718fd61f6e_25.png)

```python
# 定义短期和长期窗口
short_window = 42
long_window = 252
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_27.png)

定义好窗口后，我们开始计算移动平均线。以下是计算步骤：

*   **计算短期均线 (SMA1)**：对股价数据应用一个滚动窗口，计算窗口内的平均值。
*   **计算长期均线 (SMA2)**：使用更大的窗口进行同样的计算。

![](img/a5921577f4fae9f499ec1b718fd61f6e_29.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_30.png)

```python
# 计算短期移动平均线
data['SMA1'] = data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
data['SMA2'] = data['AAPL'].rolling(window=long_window).mean()
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_31.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_32.png)

计算完成后，我们可以查看数据并绘制图表，直观地观察两条均线的走势。

```python
# 绘制股价与移动平均线
data.plot(figsize=(10, 6))
plt.show()
```

在生成的图表中，您会看到原始股价线以及短期（SMA1）和长期（SMA2）均线。两条均线的交叉点是我们关注的重点。

![](img/a5921577f4fae9f499ec1b718fd61f6e_34.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_35.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_36.png)

---

![](img/a5921577f4fae9f499ec1b718fd61f6e_37.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_38.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_39.png)

## 标记均线交叉点

![](img/a5921577f4fae9f499ec1b718fd61f6e_40.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_41.png)

从上一节的图表中，我们已经能够观察到短期和长期均线的交叉。本节我们将通过代码精确地标记这些交叉点所代表的市场信号。

首先，我们需要去除计算均线时产生的前期缺失值，以便进行准确的比较。

```python
# 去除因计算移动平均产生的缺失值
data.dropna(inplace=True)
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_43.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_44.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_45.png)

接着，我们创建一个标志位 `position`。这个标志位用于记录短期均线相对于长期均线的位置关系，它是生成交易信号的基础。

![](img/a5921577f4fae9f499ec1b718fd61f6e_46.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_47.png)

*   当 `position = 1` 时，表示短期均线 **高于** 长期均线，通常视为看涨信号。
*   当 `position = 0` 时，表示短期均线 **低于** 长期均线，通常视为看跌信号。

![](img/a5921577f4fae9f499ec1b718fd61f6e_49.png)

```python
# 创建标志位，标记短期均线是否高于长期均线
data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, 0)
print(data[['AAPL', 'SMA1', 'SMA2', 'position']].head())
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_50.png)

我们也可以将这个标志位绘制在图表上，以便更直观地理解。

![](img/a5921577f4fae9f499ec1b718fd61f6e_52.png)

```python
# 绘制股价、均线及买卖标志位
ax = data[['AAPL', 'SMA1', 'SMA2']].plot(figsize=(10,6))
data['position'].plot(ax=ax, secondary_y=True, style='g')
plt.show()
```

![](img/a5921577f4fae9f499ec1b718fd61f6e_54.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_55.png)

---

## 理解交易策略逻辑

在图表中，短期均线向上穿过长期均线的点被称为“黄金交叉”，这通常被视为**买入信号**。相反，短期均线向下穿过长期均线的点被称为“死亡交叉”，这通常被视为**卖出信号**。

这个策略的核心逻辑在于利用趋势的惯性。一个简单的交易思路是：
*   在“黄金交叉”（`position` 从0变为1）时买入股票。
*   在“死亡交叉”（`position` 从1变为0）时卖出股票。

![](img/a5921577f4fae9f499ec1b718fd61f6e_57.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_58.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_59.png)

通过这种方式，投资者试图在上涨趋势开始时持有股票，在下跌趋势开始时持有现金，从而规避部分下跌风险，并捕捉主要的上涨波段。这比单纯地买入并持有（Buy and Hold）策略更具主动性。

---

## 总结

本节课中我们一起学习了量化交易中一个基础且重要的概念——双移动平均线交叉策略。我们完成了以下工作：

1.  **数据准备**：导入并清洗了苹果公司的股价数据。
2.  **指标计算**：计算了短期（42日）和长期（252日）移动平均线。
3.  **信号标记**：通过比较两条均线，创建了代表买卖信号的标志位 (`position`)。
4.  **可视化分析**：将股价、均线及买卖信号绘制在同一图表中，直观展示了“黄金交叉”和“死亡交叉”。
5.  **策略理解**：探讨了基于均线交叉进行买卖操作的策略逻辑，即试图在趋势开始时入场，在趋势反转时离场。

![](img/a5921577f4fae9f499ec1b718fd61f6e_61.png)

![](img/a5921577f4fae9f499ec1b718fd61f6e_63.png)

通过本课的学习，您已经掌握了使用移动平均线进行初步市场分析和生成交易信号的基本方法。这是构建更复杂量化交易模型的重要一步。