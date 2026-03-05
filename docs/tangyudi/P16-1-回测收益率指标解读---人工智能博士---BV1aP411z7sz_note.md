# 量化投资基础：P16：1-回测收益率指标解读 📈

![](img/69109b1c435829d537b3198a5ddb0cba_1.png)

在本节课中，我们将要学习如何解读策略回测的核心评估指标。当我们完成一个投资策略后，需要在历史数据中进行测试，以评估其表现。评估不仅仅是判断“赚”或“赔”，而是需要一系列量化指标来衡量收益、风险以及与市场基准的对比优势。本节课将重点介绍最基础的指标——回测收益率，并解释其含义与计算方法。

上一节我们介绍了策略评估的重要性，本节中我们来看看最核心的收益指标。

## 回测收益率

![](img/69109b1c435829d537b3198a5ddb0cba_3.png)

回测收益率是最基本、最常用的策略评估指标。它衡量了在回测周期内，策略最终获得的收益相对于初始本金的增长比例。例如，一个4%的回测收益率意味着，相对于初始投入的资金，策略最终带来了4%的增值。

![](img/69109b1c435829d537b3198a5ddb0cba_5.png)

![](img/69109b1c435829d537b3198a5ddb0cba_7.png)

其计算公式如下：

![](img/69109b1c435829d537b3198a5ddb0cba_9.png)

**公式：**
`回测收益率 = (期末资产总值 / 期初资产总值) - 1`

![](img/69109b1c435829d537b3198a5ddb0cba_11.png)

其中：
*   `期末资产总值` 代表回测结束时卖出所有资产后的总金额。
*   `期初资产总值` 代表回测开始时投入的本金。

![](img/69109b1c435829d537b3198a5ddb0cba_13.png)

![](img/69109b1c435829d537b3198a5ddb0cba_15.png)

这个指标直观地反映了策略在测试期间的整体盈利能力。

![](img/69109b1c435829d537b3198a5ddb0cba_17.png)

![](img/69109b1c435829d537b3198a5ddb0cba_19.png)

接下来，我们通过代码来演示如何计算并可视化回测收益率的变化过程。

## 代码演示

![](img/69109b1c435829d537b3198a5ddb0cba_21.png)

首先，我们导入必要的工具包并加载示例数据。

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/69109b1c435829d537b3198a5ddb0cba_23.png)

![](img/69109b1c435829d537b3198a5ddb0cba_25.png)

# 设置中文字体（示例，根据实际环境调整）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设数据包含策略每日的资产净值
data = pd.read_csv('your_strategy_data.csv', index_col=0, parse_dates=True)
# 假设我们关注‘net_value’这一列，它代表每日的资产净值
strategy_net_value = data['net_value']
print(strategy_net_value.head())
```

假设我们的数据 `strategy_net_value` 是一个时间序列，记录了策略从开始到结束每一天的资产净值。

计算回测收益率的第一步，是计算每日净值相对于期初净值的比值。

以下是计算步骤：

![](img/69109b1c435829d537b3198a5ddb0cba_27.png)

![](img/69109b1c435829d537b3198a5ddb0cba_29.png)

1.  计算每日净值与期初净值的比值。
2.  将此比值序列可视化，观察资产相对增长过程。
3.  根据公式计算最终的回测收益率。

![](img/69109b1c435829d537b3198a5ddb0cba_31.png)

![](img/69109b1c435829d537b3198a5ddb0cba_33.png)

```python
# 1. 计算每日净值相对于期初净值的比值
relative_value = strategy_net_value / strategy_net_value.iloc[0]

![](img/69109b1c435829d537b3198a5ddb0cba_35.png)

![](img/69109b1c435829d537b3198a5ddb0cba_37.png)

# 2. 绘制资产相对增长曲线
plt.figure(figsize=(10, 6))
relative_value.plot()
plt.title('策略资产相对增长曲线')
plt.ylabel('相对净值 (期初=1)')
plt.xlabel('日期')
plt.grid(True)
plt.show()

# 3. 计算最终的回测收益率
final_return = relative_value.iloc[-1] - 1

![](img/69109b1c435829d537b3198a5ddb0cba_39.png)

![](img/69109b1c435829d537b3198a5ddb0cba_41.png)

# 将结果整理成DataFrame展示
result_df = pd.DataFrame({
    '回测收益率': [final_return]
}, index=['策略表现'])
print(result_df)
```

![](img/69109b1c435829d537b3198a5ddb0cba_43.png)

![](img/69109b1c435829d537b3198a5ddb0cba_45.png)

执行以上代码后，我们将得到一条曲线，展示了资产净值随时间相对于初始值（设为1）的变化情况。同时，会输出一个表格，清晰地显示该策略在整个回测周期内的最终回测收益率。

![](img/69109b1c435829d537b3198a5ddb0cba_47.png)

本节课中我们一起学习了策略评估的基础——回测收益率。我们理解了它的定义是衡量策略相对于初始本金的最终盈利比例，并掌握了其计算公式 `(期末资产/期初资产) - 1`。通过Python代码，我们实践了如何从净值数据中计算并可视化这一指标。回测收益率是评估策略表现的起点，在后续课程中，我们将以此为基础，学习更多衡量风险和比较优势的复杂指标。