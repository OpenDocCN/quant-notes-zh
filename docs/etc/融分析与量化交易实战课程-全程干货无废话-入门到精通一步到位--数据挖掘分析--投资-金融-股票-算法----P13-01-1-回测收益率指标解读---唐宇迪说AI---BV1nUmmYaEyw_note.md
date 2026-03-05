# Python金融量化交易实战：P13：01-1：回测收益率指标解读 📈

在本节课中，我们将学习如何评估一个量化交易策略。当我们完成一个策略设计后，需要在历史数据上进行测试，以判断其表现。评估不仅仅是看“赚”或“赔”，而是需要一系列具体的指标来衡量收益、风险以及与市场基准的对比优势。本节课将重点解读最基础的评估指标——回测收益率。

![](img/8467974aa2b6bedc5dd04be2d802536a_1.png)

上一节我们介绍了策略评估的必要性，本节中我们来看看如何具体计算和理解回测收益率。

## 回测收益率概念

回测收益率是评估策略表现最核心、最基础的指标。它衡量的是，在回测周期内，策略最终获得的收益相对于初始本金的增长比例。

![](img/8467974aa2b6bedc5dd04be2d802536a_3.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_5.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_7.png)

其计算公式如下：

![](img/8467974aa2b6bedc5dd04be2d802536a_9.png)

**回测收益率 = (期末资产总值 - 期初资产总值) / 期初资产总值**

或者等价于：

![](img/8467974aa2b6bedc5dd04be2d802536a_11.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_13.png)

**回测收益率 = (期末价格 Pt / 期初价格 P0) - 1**

![](img/8467974aa2b6bedc5dd04be2d802536a_15.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_17.png)

这个指标直观地告诉我们，经过一段时间的策略运行，我们的本金总共增值了多少。

![](img/8467974aa2b6bedc5dd04be2d802536a_19.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_21.png)

## 使用Python计算回测收益率

我们将通过一个简单的Python示例，演示如何计算并可视化策略在整个回测周期内的累计收益变化。

![](img/8467974aa2b6bedc5dd04be2d802536a_23.png)

以下是计算回测收益率的步骤：

![](img/8467974aa2b6bedc5dd04be2d802536a_25.png)

1.  导入必要的工具包。
2.  读取历史股价数据。
3.  计算每日资产相对于期初的比值。
4.  根据比值计算最终的回测收益率。
5.  将结果进行可视化展示。

![](img/8467974aa2b6bedc5dd04be2d802536a_27.png)

首先，我们导入工具包并读取数据。

```python
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体，方便绘图显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0, parse_dates=True)
print(data.head())
```

假设我们的数据包含了策略模拟交易后的每日资产净值。接下来，我们计算每日资产相对于期初的比值。

![](img/8467974aa2b6bedc5dd04be2d802536a_29.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_31.png)

```python
# 计算每日资产净值相对于期初的比值
# 期初净值为第一天的数据
initial_value = data.iloc[0]
cumulative_return_ratio = data / initial_value

![](img/8467974aa2b6bedc5dd04be2d802536a_33.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_35.png)

# 绘制累计收益曲线
plt.figure(figsize=(10, 6))
cumulative_return_ratio.plot()
plt.title('策略累计收益走势')
plt.xlabel('日期')
plt.ylabel('累计收益倍数')
plt.grid(True)
plt.show()
```

![](img/8467974aa2b6bedc5dd04be2d802536a_37.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_39.png)

通过上面的图表，我们可以看到资产净值随时间变化的轨迹。最后，我们计算整个回测周期的最终收益率。

```python
# 计算最终的回测收益率
# 取最后一天的净值比值，然后减去1
final_return_ratio = cumulative_return_ratio.iloc[-1]
backtest_return = final_return_ratio - 1

![](img/8467974aa2b6bedc5dd04be2d802536a_41.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_43.png)

# 将结果整理成DataFrame，便于查看
result_df = pd.DataFrame(backtest_return.values, columns=['回测收益率'], index=backtest_return.index)
print(result_df)
```

![](img/8467974aa2b6bedc5dd04be2d802536a_45.png)

![](img/8467974aa2b6bedc5dd04be2d802536a_47.png)

执行以上代码后，我们将得到一个清晰的表格，显示策略最终的回测收益率。正值代表盈利，负值代表亏损。

![](img/8467974aa2b6bedc5dd04be2d802536a_49.png)

本节课中我们一起学习了量化策略评估的基础——回测收益率。我们理解了它的定义和计算公式，并通过Python代码实践了从数据读取到结果计算与可视化的完整流程。回测收益率告诉我们策略的最终盈利情况，是衡量策略有效性的第一个关键指标。在后续课程中，我们将继续学习其他重要的风险评估和绩效分析指标。