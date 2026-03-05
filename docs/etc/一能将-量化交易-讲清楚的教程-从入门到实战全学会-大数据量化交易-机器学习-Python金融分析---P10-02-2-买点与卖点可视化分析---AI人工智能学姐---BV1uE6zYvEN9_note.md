# Python金融量化：P10：02-2：买点与卖点可视化分析 📈

![](img/80fd3ca35953974d207d6356728d0bcb_0.png)

![](img/80fd3ca35953974d207d6356728d0bcb_2.png)

在本节课中，我们将学习如何通过计算短期和长期移动平均线，来识别股票交易中的潜在买点和卖点，并进行可视化分析。我们将使用苹果公司的股票数据作为示例，通过代码实现数据处理、指标计算和结果展示。

![](img/80fd3ca35953974d207d6356728d0bcb_4.png)

---

![](img/80fd3ca35953974d207d6356728d0bcb_6.png)

![](img/80fd3ca35953974d207d6356728d0bcb_8.png)

## 第一步：导入工具包与读取数据

![](img/80fd3ca35953974d207d6356728d0bcb_10.png)

首先，我们需要导入必要的Python库并加载数据。我们将使用`pandas`进行数据处理，`matplotlib`进行绘图。

![](img/80fd3ca35953974d207d6356728d0bcb_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/80fd3ca35953974d207d6356728d0bcb_14.png)

接下来，读取包含多只股票历史价格的数据文件。我们将第一列（日期）设置为索引，并解析日期格式。

![](img/80fd3ca35953974d207d6356728d0bcb_16.png)

![](img/80fd3ca35953974d207d6356728d0bcb_18.png)

```python
# 读取数据，指定第一列为索引（日期）
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/80fd3ca35953974d207d6356728d0bcb_20.png)

数据加载完成后，我们选择苹果公司（AAPL）的股票价格列进行后续分析。

![](img/80fd3ca35953974d207d6356728d0bcb_22.png)

![](img/80fd3ca35953974d207d6356728d0bcb_24.png)

```python
# 选择苹果公司的股票价格列
apple_data = data['AAPL']
```

![](img/80fd3ca35953974d207d6356728d0bcb_26.png)

---

![](img/80fd3ca35953974d207d6356728d0bcb_28.png)

## 第二步：数据预处理

![](img/80fd3ca35953974d207d6356728d0bcb_30.png)

在开始计算之前，我们需要对数据进行预处理，主要是处理可能存在的缺失值，以确保后续计算的准确性。

![](img/80fd3ca35953974d207d6356728d0bcb_32.png)

```python
# 删除缺失值
apple_data_clean = apple_data.dropna()
```

![](img/80fd3ca35953974d207d6356728d0bcb_34.png)

处理后的数据将作为我们分析的基础。

---

## 第三步：计算短期与长期移动平均线

![](img/80fd3ca35953974d207d6356728d0bcb_36.png)

移动平均线是技术分析中常用的指标。短期均线对价格变化更敏感，长期均线则反映长期趋势。我们将计算两个均线。

![](img/80fd3ca35953974d207d6356728d0bcb_38.png)

首先，定义短期和长期的窗口大小。由于我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将窗口设置得比通常的5日和20日更大一些。

![](img/80fd3ca35953974d207d6356728d0bcb_40.png)

```python
# 定义短期和长期窗口大小
short_window = 42
long_window = 252
```

![](img/80fd3ca35953974d207d6356728d0bcb_42.png)

![](img/80fd3ca35953974d207d6356728d0bcb_44.png)

接下来，计算短期移动平均线（SMA）和长期移动平均线（LMA）。

![](img/80fd3ca35953974d207d6356728d0bcb_46.png)

```python
# 计算短期移动平均线
apple_data_clean['SMA_Short'] = apple_data_clean['AAPL'].rolling(window=short_window).mean()

![](img/80fd3ca35953974d207d6356728d0bcb_48.png)

![](img/80fd3ca35953974d207d6356728d0bcb_50.png)

# 计算长期移动平均线
apple_data_clean['SMA_Long'] = apple_data_clean['AAPL'].rolling(window=long_window).mean()
```

![](img/80fd3ca35953974d207d6356728d0bcb_52.png)

![](img/80fd3ca35953974d207d6356728d0bcb_54.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。

---

![](img/80fd3ca35953974d207d6356728d0bcb_56.png)

![](img/80fd3ca35953974d207d6356728d0bcb_58.png)

## 第四步：可视化移动平均线

现在，我们可以将原始股价、短期均线和长期均线绘制在同一张图上，直观地观察它们的走势和交叉点。

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data_clean['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(apple_data_clean['SMA_Short'], label=f'Short SMA ({short_window} days)', color='red')
plt.plot(apple_data_clean['SMA_Long'], label=f'Long SMA ({long_window} days)', color='green')
plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/80fd3ca35953974d207d6356728d0bcb_60.png)

在生成的图表中，关注短期均线（红色）与长期均线（绿色）的交叉点：
*   **黄金交叉**：短期均线从下向上穿过长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿过长期均线，通常被视为**卖出信号**。

![](img/80fd3ca35953974d207d6356728d0bcb_62.png)

![](img/80fd3ca35953974d207d6356728d0bcb_64.png)

---

![](img/80fd3ca35953974d207d6356728d0bcb_66.png)

## 第五步：标记均线位置关系

![](img/80fd3ca35953974d207d6356728d0bcb_68.png)

![](img/80fd3ca35953974d207d6356728d0bcb_70.png)

为了量化交叉信号，我们创建一个新的列来标记短期均线是否高于长期均线。这有助于我们系统化地识别买卖点。

首先，需要删除在计算移动平均线时产生的缺失值。

```python
# 删除因计算移动平均线而产生的缺失值
apple_data_clean = apple_data_clean.dropna()
```

![](img/80fd3ca35953974d207d6356728d0bcb_72.png)

然后，使用`numpy`的`where`函数创建标志位。

![](img/80fd3ca35953974d207d6356728d0bcb_74.png)

```python
import numpy as np

![](img/80fd3ca35953974d207d6356728d0bcb_76.png)

# 创建标志位：1表示短期均线高于长期均线，0表示低于
apple_data_clean['Position'] = np.where(apple_data_clean['SMA_Short'] > apple_data_clean['SMA_Long'], 1, 0)
```

现在，数据框中多了一列`Position`，其值为1或0，清晰地表明了任一时刻两条均线的相对位置。

![](img/80fd3ca35953974d207d6356728d0bcb_78.png)

---

![](img/80fd3ca35953974d207d6356728d0bcb_80.png)

## 第六步：理解策略逻辑

![](img/80fd3ca35953974d207d6356728d0bcb_82.png)

在进入策略回测之前，理解我们策略的逻辑至关重要。简单来说：
*   当`Position`从0变为1时（黄金交叉），我们**买入**。
*   当`Position`从1变为0时（死亡交叉），我们**卖出**。

这个策略的核心思想是“跟随趋势”。通过机械地执行这些信号，我们试图在市场上涨时持有股票，在市场下跌时持有现金，从而规避部分下跌风险，并捕捉上涨收益。这与“买入并持有”的被动策略形成对比。

---

## 总结

本节课中我们一起学习了量化交易中的一个基础策略——双移动平均线交叉策略的分析流程。

![](img/80fd3ca35953974d207d6356728d0bcb_84.png)

1.  **数据准备**：我们导入了库，读取了股票数据，并选择了苹果公司的股价进行分析。
2.  **数据预处理**：我们清除了数据中的缺失值，为计算做好准备。
3.  **指标计算**：我们定义了短期（42日）和长期（252日）窗口，并计算了相应的移动平均线。
4.  **可视化分析**：我们将股价和两条均线绘制在图上，直观地识别出潜在的“黄金交叉”（买点）和“死亡交叉”（卖点）。
5.  **信号标记**：我们通过创建`Position`标志位，系统化地标记了短期均线相对于长期均线的位置关系，为后续的策略回测打下了基础。

![](img/80fd3ca35953974d207d6356728d0bcb_86.png)

通过本节内容，你已经掌握了如何利用移动平均线进行初步的买卖点分析。在接下来的课程中，我们将基于这些信号，构建一个完整的交易策略，并计算其历史表现，与简单的“买入并持有”策略进行对比。