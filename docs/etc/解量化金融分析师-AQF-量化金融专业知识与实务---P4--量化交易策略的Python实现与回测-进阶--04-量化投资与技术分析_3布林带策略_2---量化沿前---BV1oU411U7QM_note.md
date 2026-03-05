# 量化金融分析师.AQF：P4：量化交易策略的Python实现与回测（进阶）04.量化投资与技术分析_3布林带策略_2

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_1.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_3.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_5.png)

在本节课中，我们将深入学习如何通过Python循环逐行处理数据，来实现布林带策略的持仓信号计算。我们将重点解析`iterrows()`方法的使用，并理解如何通过循环逻辑将交易信号转换为连续的持仓信号。

## 理解 `iterrows()` 方法

上一节我们介绍了布林带策略交易信号的计算。本节中我们来看看如何将这些离散的交易信号，转化为连续的持仓信号。这需要我们逐行处理`DataFrame`数据。

`DataFrame`的`iterrows()`方法用于逐行迭代数据。它每次迭代会返回两个值：第一个是当前行的索引标签（`index`），第二个是将该行数据转换成的`Series`对象。

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_7.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_9.png)

以下是`iterrows()`方法的基本使用格式：
```python
for index, series in df.iterrows():
    # 在此处处理每一行数据
    # index: 当前行的标签（如日期）
    # series: 当前行所有列的值构成的Series
```

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_11.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_13.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_15.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_17.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_19.png)

## 两种变量接收方式

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_21.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_23.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_25.png)

`iterrows()`返回两个值，在`for`循环中接收它们的方式会影响后续的数据处理逻辑。

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_27.png)

### 方式一：使用单个变量接收
如果只用一个变量接收`iterrows()`的返回值，Python会将两个返回值打包成一个元组（`tuple`）赋给该变量。
```python
for item in df.iterrows():
    # 此时item是一个元组 (index, series)
    row_label = item[0]  # 获取行标签
    row_data = item[1]   # 获取该行的Series数据
```

### 方式二：使用两个变量接收（推荐）
更清晰的方式是直接用两个变量分别接收行标签和行数据，这利用了Python的“解包”（unpack）特性。
```python
for row_label, row_data in df.iterrows():
    # row_label: 直接就是行标签
    # row_data: 直接就是该行的Series数据
    # 可以直接通过 row_data[‘column_name‘] 访问特定列的值
```
这种方式代码更易读，也更容易操作行数据。

## 构建持仓信号逻辑

有了逐行处理数据的能力，我们就可以构建策略的核心：将交易信号`signal`列，转换为代表持续持仓状态的`position`列。

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_29.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_31.png)

以下是实现该逻辑的核心代码步骤：
1.  在数据中新建一个`position`列，并初始化为0（代表空仓）。
2.  使用`iterrows()`逐行遍历数据。
3.  在每一行中，判断`signal`列的值：
    *   如果`signal == 1`（做多信号），则将`position`变量（注意，这是循环内的临时变量）设置为`1`。
    *   如果`signal == -1`（做空信号），则将`position`变量设置为`-1`。
    *   如果`signal == 0`（无信号），则`position`变量保持上一次循环设置的值不变。
4.  将循环内`position`变量的值，赋值给当前行数据`df.at[row_label, ‘position‘]`列。

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_33.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_35.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_37.png)

以下是代码示例：
```python
# 初始化持仓列
stock_data[‘position‘] = 0

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_39.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_41.png)

# 初始化一个变量，用于在循环中记录持仓状态
current_position = 0

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_43.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_45.png)

# 逐行遍历，计算持仓信号
for date, row in stock_data.iterrows():
    # 判断交易信号
    if row[‘signal‘] == 1:
        current_position = 1
    elif row[‘signal‘] == -1:
        current_position = -1
    # 如果signal为0，则current_position保持不变

    # 将当前持仓状态记录到DataFrame的对应行
    stock_data.at[date, ‘position‘] = current_position
```

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_47.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_49.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_51.png)

**关键点理解**：  
循环内的`current_position`变量像一个“记忆单元”。当遇到新的交易信号（1或-1）时，它被更新。当没有新信号时（`signal=0`），它就保持之前的值。这样，`position`列就形成了一段段连续的持仓状态，直到下一个反向或平仓信号出现才会改变。

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_53.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_55.png)

## 策略执行与结果分析

完成持仓信号的计算后，后续步骤与之前学习的策略回测流程一致：

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_57.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_59.png)

1.  **计算策略每日收益**：根据`position`列（持仓方向）和股票每日涨跌幅，计算策略每日的盈亏。
    `策略日收益 = 前一日position * 当日股票收益率`
2.  **计算累计收益**：将策略日收益累加，得到策略和基准（如买入持有）的累计收益曲线。
3.  **可视化与评估**：绘制累计收益对比图，并计算夏普比率、最大回撤等指标，评估策略表现。

通过本节实现的布林带策略，我们可以看到，在某些市场阶段，该策略通过做多和做空信号，其累计收益能够跑赢简单的买入持有策略。这体现了趋势跟踪类指标在波动市场中的潜在价值。

## 总结

本节课中我们一起学习了量化策略实现中的一个关键技术点：**使用循环逐行处理数据以生成持仓信号**。

*   我们深入理解了`DataFrame.iterrows()`方法的两种用法，推荐使用双变量解包的方式，使代码更清晰。
*   我们掌握了如何通过一个循环内的“状态记忆”变量，将离散的交易信号`signal`，转换为连续的持仓状态`position`。这是实现许多非向量化复杂逻辑的基础。
*   我们完成了布林带策略从信号生成、持仓计算到收益回测的完整闭环。

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_61.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_63.png)

![](img/e375c4b234b8d0ff44bd33ad9f7130c8_64.png)

这种方法虽然计算效率可能低于完全的向量化运算，但提供了极高的灵活性，能够实现更复杂的交易逻辑，是量化策略开发中不可或缺的工具。在后续更复杂的策略中，我们会频繁用到这种逐行处理的思维模式。