# 量化编程：18：趋势线绘制进阶

在本节课中，我们将深入学习如何在MC中动态绘制趋势线。我们将探讨如何自动识别图表中的高低点，并基于这些点绘制趋势线，同时学习如何管理这些线条以避免图表混乱。

![](img/0aa1e5e76250853427f4b88cd643e56a_1.png)

![](img/0aa1e5e76250853427f4b88cd643e56a_3.png)

![](img/0aa1e5e76250853427f4b88cd643e56a_5.png)

上一节我们介绍了使用固定坐标绘制趋势线的基本方法。本节中，我们将看看如何让程序自动识别高低点并动态绘制趋势线。

![](img/0aa1e5e76250853427f4b88cd643e56a_7.png)

![](img/0aa1e5e76250853427f4b88cd643e56a_9.png)

## 回顾与代码优化

首先，我们回顾一下上节课用`tl.new`函数绘制固定趋势线的方法。其核心是提供起始和结束点的日期、时间及价格。

![](img/0aa1e5e76250853427f4b88cd643e56a_11.png)

```pinescript
value1 = tl.new(bar_index[10], close[10], bar_index, close)
```

![](img/0aa1e5e76250853427f4b88cd643e56a_13.png)

然而，直接将绘图代码放在主逻辑中，即使当前K线不符合绘图条件，程序也会反复尝试执行，这会浪费计算资源。

以下是优化代码逻辑的方法：将绘图逻辑包裹在条件判断语句中，确保只在满足特定条件（例如，是图表上最后一根K线）时才执行。

```pinescript
if barstate.islast
    value1 = tl.new(bar_index[10], close[10], bar_index, close)
    tl.set_extend(value1, extend.right)
```

## `tl.new` 与 `tl.new_self` 的区别

`tl.new`函数绘制的线条默认显示在主图（数据1）上。如果你想将趋势线绘制在策略中添加的其他数据序列（如数据2、数据3）对应的子图上，就需要使用`tl.new_self`。

其区别在于：
*   `tl.new`：线条绘制在策略的主数据序列上。
*   `tl.new_self`：线条绘制在调用该函数的指标或信号所在的数据序列上。你可以在指标设置中指定它显示在哪个子图。

简单理解，`self`代表“自身”，即趋势线会跟随当前指标所在的图表。

## 动态识别高低点绘制趋势线

在实际策略中，我们很少手动输入坐标，更多的是让程序自动识别关键点（如波段高点、低点）并连线。

### 核心思路

我们需要定义一个“高点”或“低点”。例如，一个“高点”可以定义为：当前K线的收盘价，高于其左边N根和右边M根K线的收盘价。

以下是实现这一逻辑的步骤：

1.  **定义参数**：设定判断高低点时，需要观察的左侧和右侧K线数量。
2.  **循环比较**：遍历左侧和右侧的K线，找出它们的最高价（对于高点判断）或最低价（对于低点判断）。
3.  **条件判断**：如果当前K线的价格同时大于左侧和右侧的最大值，则将其标记为一个高点。
4.  **记录坐标**：记录这个高点的日期、时间和价格。
5.  **连接高点**：当识别到两个有效的高点时，用一条趋势线将它们连接起来。

### 代码实现与解析

以下是自动识别波段高点并绘制趋势线的示例代码框架：

```pinescript
//@version=5
indicator("自动趋势线", overlay=true)

// 1. 定义参数：左侧和右侧观察的K线数量
leftBars = input(5, “左侧K线数”)
rightBars = input(3, “右侧K线数”)

// 2. 定义变量存储当前和上一个高点的信息
var float prevHighPrice = na
var int prevHighBarIndex = na
var float currentHighPrice = na
var int currentHighBarIndex = na

// 3. 确保有足够的K线进行判断
if bar_index > leftBars + rightBars
    // 初始化左侧和右侧的最大值
    leftMax = 0.0
    rightMax = 0.0
    
    // 找出右侧K线的最高价
    for i = 1 to rightBars
        rightMax := math.max(rightMax, close[i])
    
    // 找出左侧K线的最高价 (从 rightBars+1 到 rightBars+leftBars)
    for j = rightBars + 1 to rightBars + leftBars
        leftMax := math.max(leftMax, close[j])
    
    // 4. 判断当前点是否为高点
    if close[rightBars] > leftMax and close[rightBars] > rightMax
        // 发现新高点，更新记录
        prevHighPrice := currentHighPrice
        prevHighBarIndex := currentHighBarIndex
        currentHighPrice := close[rightBars]
        currentHighBarIndex := bar_index[rightBars]
        
        // 5. 如果已有前一个高点，则绘制趋势线
        if not na(prevHighPrice)
            // 在绘制新线前，删除旧的趋势线（如果需要）
            // tl.delete(lineId) 
            lineId = tl.new(prevHighBarIndex, prevHighPrice, currentHighBarIndex, currentHighPrice)
            tl.set_extend(lineId, extend.right)
```

**关键点说明**：
*   代码使用循环遍历来比较价格。
*   `prevHighPrice`和`currentHighPrice`分别存储上一个高点和当前高点的信息。
*   只有当识别到两个有效高点时，才会执行绘图操作。
*   注释中提到了`tl.delete()`，用于删除旧的趋势线，这在动态更新线条时非常有用。

## 使用 `tl.delete` 管理趋势线

当策略不断运行并绘制新的趋势线时，图表上可能会留下大量过时的线条，导致图表混乱。`tl.delete()`函数用于删除指定ID的趋势线。

每一条通过`tl.new`或`tl.new_self`绘制的趋势线都有一个唯一的ID（返回值）。保存这个ID，就可以在需要时删除它。

```pinescript
// 绘制一条线并保存其ID
lineId = tl.new(x1, y1, x2, y2)

// 在特定条件下（例如，价格突破该线后）删除这条线
if close > y2
    tl.delete(lineId)
```

在上面的动态绘图示例中，一种良好的实践是：在绘制连接最新两个高点的趋势线之前，先删除之前绘制的同类趋势线，以保持图表的清晰。

## 总结

![](img/0aa1e5e76250853427f4b88cd643e56a_15.png)

本节课中我们一起学习了趋势线绘制的进阶技巧。我们从优化固定绘图代码的效率开始，理解了`tl.new`与`tl.new_self`的应用场景差异。核心内容是学习了如何通过编程逻辑自动识别图表中的波段高点（或低点），并动态绘制连接这些点的趋势线。最后，我们介绍了使用`tl.delete`函数来管理趋势线，避免图表过于杂乱。掌握这些动态绘制和管理趋势线的方法，是构建可视化交易策略或指标的重要基础。