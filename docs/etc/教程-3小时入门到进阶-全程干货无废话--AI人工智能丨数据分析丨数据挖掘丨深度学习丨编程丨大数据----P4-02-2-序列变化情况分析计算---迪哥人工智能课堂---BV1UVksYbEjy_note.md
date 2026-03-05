# Python金融分析与量化交易实战教程：P4：02-2-序列变化情况分析计算 📈

![](img/909fd182ec9b1dd02ad2e5affb223db7_0.png)

![](img/909fd182ec9b1dd02ad2e5affb223db7_2.png)

在本节课中，我们将学习如何分析时间序列数据的变化情况。具体来说，我们将计算两种关键指标：**每日差异值**和**每日变化率**。这些指标对于理解金融数据的动态变化至关重要。

![](img/909fd182ec9b1dd02ad2e5affb223db7_4.png)

上一节我们介绍了时间序列数据的基本处理，本节中我们来看看如何量化其变化。

## 计算每日差异值

首先，我们需要计算每一天与前一天的指标差异值。例如，在股票数据中，我们不仅关心当天的价格，更关心相比前一天是上涨还是下跌了多少。

![](img/909fd182ec9b1dd02ad2e5affb223db7_6.png)

在Pandas中，计算这种差异非常简单。我们可以使用`.diff()`方法。该方法默认计算当前行与前一行之间的差值。

![](img/909fd182ec9b1dd02ad2e5affb223db7_8.png)

以下是计算差异值的代码示例：

```python
# 假设 `data` 是一个包含时间序列数据的DataFrame
diff_data = data.diff()
print(diff_data.head())
```

![](img/909fd182ec9b1dd02ad2e5affb223db7_10.png)

执行上述代码后，你会看到一个新的DataFrame。对于每一列，1月5日的数据等于1月5日的原始值减去1月4日的原始值。由于1月4日没有前一天的数据，因此其差异值会显示为`NaN`（Not a Number）。

![](img/909fd182ec9b1dd02ad2e5affb223db7_12.png)

`.diff()`方法默认计算相邻两天的差异。我们还可以通过指定`periods`参数来计算间隔多天的差异，例如计算两天或一周的差异。

养成查阅API文档的习惯非常重要。遇到不熟悉的函数或参数时，可以复制函数名进行搜索，这是最高效的学习方法。

## 计算每日变化率（增长率）

![](img/909fd182ec9b1dd02ad2e5affb223db7_14.png)

差异值是一个绝对值，有时我们更关心相对变化，即增长率。例如，股价从1元涨到2元（增长100%）与从10000元涨到10001元（增长0.01%）的意义完全不同。

![](img/909fd182ec9b1dd02ad2e5affb223db7_16.png)

增长率（或变化率）的计算公式为：
**增长率 = (后一天的值 - 前一天的值) / 前一天的值**

![](img/909fd182ec9b1dd02ad2e5affb223db7_18.png)

在Pandas中，我们可以使用`.pct_change()`方法来直接计算这个百分比变化。

以下是计算增长率的代码示例：

![](img/909fd182ec9b1dd02ad2e5affb223db7_20.png)

```python
# 计算每日百分比变化（增长率）
pct_change_data = data.pct_change()
print(pct_change_data.head())
```

计算完成后，数据将显示为小数形式（例如0.05表示增长5%）。我们可以使用`.round()`方法来控制显示的小数位数。

![](img/909fd182ec9b1dd02ad2e5affb223db7_22.png)

```python
# 保留三位小数
pct_change_data_rounded = pct_change_data.round(3)
print(pct_change_data_rounded.head())
```

![](img/909fd182ec9b1dd02ad2e5affb223db7_24.png)

## 分析变化率的统计特征

![](img/909fd182ec9b1dd02ad2e5affb223db7_26.png)

![](img/909fd182ec9b1dd02ad2e5affb223db7_28.png)

得到每日增长率序列后，我们可以进一步分析其整体统计特征，例如计算所有交易日增长率的平均值。

![](img/909fd182ec9b1dd02ad2e5affb223db7_30.png)

以下是计算平均增长率的代码：

![](img/909fd182ec9b1dd02ad2e5affb223db7_32.png)

```python
# 计算各列增长率的平均值
mean_pct_change = pct_change_data.mean()
print(mean_pct_change)
```

![](img/909fd182ec9b1dd02ad2e5affb223db7_34.png)

为了更直观地展示各个指标的平均增长率，我们可以将其可视化。由于平均值是单个数值，适合用条形图来展示。

以下是绘制平均增长率条形图的代码：

```python
import matplotlib.pyplot as plt

![](img/909fd182ec9b1dd02ad2e5affb223db7_36.png)

# 绘制平均增长率的条形图
mean_pct_change.plot(kind='bar', figsize=(16, 8))
plt.title('各指标平均增长率')
plt.xlabel('指标')
plt.ylabel('平均增长率')
plt.show()
```

![](img/909fd182ec9b1dd02ad2e5affb223db7_38.png)

通过这张图，我们可以一目了然地看出哪个指标在观察期内的平均增长表现最好。

![](img/909fd182ec9b1dd02ad2e5affb223db7_40.png)

![](img/909fd182ec9b1dd02ad2e5affb223db7_42.png)

本节课中我们一起学习了时间序列变化分析的两个核心操作：计算**每日差异值**（`.diff()`）和计算**每日变化率**（`.pct_change()`）。我们还学会了如何计算这些变化序列的统计特征（如平均值）并进行可视化。这些是金融数据分析和量化交易中描述数据动态的基础技能。