# Python金融分析与量化交易实战：P26：3-策略效果演示与指标分析 📊

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_1.png)

在本节课中，我们将学习如何对量化策略进行效果演示与指标分析。我们将重点讲解如何调整数据结构、实现交易逻辑，并解读回测结果中的关键指标。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_3.png)

上一节我们介绍了策略的初步构建，本节中我们来看看如何完善交易逻辑并分析策略表现。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_5.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_7.png)

## 数据结构的调整

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_9.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_11.png)

我们最初的想法是将股票名称横向排列，即第一个股票、第二个股票、第三个股票依次横向书写。但通常，我们更喜欢纵向书写数据，即第一个股票及其财务营收数据，第二个股票及其财务营收数据，以此类推。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_13.png)

因此，我们需要对`DataFrame`进行调整。其实只需要对它执行一个转置操作即可。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_15.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_17.png)

以下是转置操作的代码示例：

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_19.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_21.png)

```python
# 假设 df 是原始的横向DataFrame
df_transposed = df.T
print(df_transposed)
```

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_23.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_25.png)

转置完成后，数据结构变为纵向排列。每一行是一个股票名称，后面跟随其对应的实际指标值。第一列是索引，即股票代码，后面是具体的数值。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_27.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_28.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_30.png)

此时，我们只需要获取索引列，即股票名称列表。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_32.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_34.png)

```python
stock_list = df_transposed.index.tolist()
```

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_36.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_38.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_40.png)

我们将这个股票列表存储到`context`对象中，作为后续交易筛选的结果。这样，在`before_trading`函数中，我们就完成了从沪深300中筛选出目标股票的工作。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_42.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_44.png)

## 实现交易逻辑

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_46.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_48.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_50.png)

接下来，我们进入`handle_bar`函数，实现具体的交易逻辑。首先，我们需要判断当前账户是否持有股票。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_52.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_54.png)

我们可以通过API文档查询`context`对象中关于持仓的信息。`context.portfolio.positions`是一个字典，包含了当前所有持仓的信息。

以下是判断和交易逻辑的实现步骤：

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_56.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_58.png)

首先，获取当前持仓信息。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_60.png)

```python
current_positions = context.portfolio.positions
```

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_62.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_64.png)

然后进行判断。如果当前持仓字典为空，说明是首次运行，手里没有任何股票。此时，我们需要买入筛选出的股票。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_66.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_68.png)

如果持仓不为空，则进入日常调仓逻辑。我们需要比较当前持有的股票和最新筛选出的股票池，卖出不再池中的股票，买入新增的股票。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_70.png)

以下是具体的实现代码：

```python
def handle_bar(context, bar_dict):
    # 获取当前持仓和计划持仓（筛选出的股票池）
    current_positions = context.portfolio.positions
    target_stocks = context.target_stocks  # 假设已在before_trading中赋值

    # 如果当前没有持仓，则执行初始建仓
    if not current_positions:
        for stock in target_stocks:
            # 平均分配资金买入
            order_target_percent(stock, 1.0 / len(target_stocks))
    else:
        # 调仓逻辑：卖出不再目标池中的股票
        for stock in current_positions.keys():
            if stock not in target_stocks:
                order_target_percent(stock, 0)  # 卖出全部

        # 买入目标池中的股票（平均分配）
        for stock in target_stocks:
            order_target_percent(stock, 1.0 / len(target_stocks))
```

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_72.png)

在`order_target_percent`函数中，第一个参数是股票代码，第二个参数是目标权重（0到1之间）。设置为0表示清仓，设置为1表示满仓该股票。我们这里采用平均分配资金的方式。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_74.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_76.png)

## 策略回测与指标分析

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_78.png)

完成代码后，我们运行回测。回测结果会包含一系列绩效指标和图表。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_80.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_82.png)

首先看整体指标，主要包括以下几项：
*   **回测年化收益**：我们设计的策略在整个回测期间的年化收益率。
*   **基准年化收益**：同期市场指数（如沪深300）的年化收益率。用于对比。
*   **最大回撤**：策略净值从高点回落的最大幅度，衡量策略的风险。
*   **夏普比率**：衡量承担单位风险所获得的超额回报。收益为负时，该指标通常也为负。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_84.png)

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_86.png)

以下是关键指标的解读：
*   如果策略年化收益高于基准收益，说明策略跑赢了市场。
*   最大回撤越小，说明策略净值曲线越稳健，下跌风险控制得越好。
*   夏普比率越高越好。正收益下，夏普比率越高，说明风险调整后的收益越好。

除了概览指标，回测结果还包含详细的交易记录、每日持仓、净值曲线等，可以点击查看以进行更深入的分析。

![](img/a8e7f51e0fe0259a381f8dc9643f0cb5_88.png)

本节课中我们一起学习了如何调整策略的数据格式、实现完整的买卖交易逻辑，并初步解读了回测报告中的核心绩效指标。理解这些指标是评估和优化量化策略的基础。