# 量化编程：第17课：转型图绘制与趋势线基础

![](img/242e590020b79e9abef2d9e5440aaec5_1.png)

![](img/242e590020b79e9abef2d9e5440aaec5_3.png)

![](img/242e590020b79e9abef2d9e5440aaec5_5.png)

在本节课中，我们将学习如何绘制转型图（砖形图），并初步了解趋势线的概念及其在MC中的实现方式。课程内容从转型图的绘制逻辑开始，逐步深入到趋势线的自动绘制与数值获取。

## 转型图（砖形图）绘制原理

![](img/242e590020b79e9abef2d9e5440aaec5_7.png)

上一节我们介绍了K线图的基本概念。本节中，我们来看看一种特殊的图表——转型图，也称为砖形图。它与传统K线图的主要区别在于，它完全摒弃了时间轴，仅根据价格变动来绘制图形。

转型图的核心逻辑是：设定一个固定的价格变动单位（砖块高度）。当价格向一个方向变动达到或超过这个单位时，就绘制一个新的砖块。如果价格反向变动超过一个单位，则在新方向绘制砖块。

以下是转型图的核心计算公式：
*   **上涨砖块判定**：`当前价格 >= 基准价 + 砖块高度`
*   **下跌砖块判定**：`当前价格 <= 基准价 - 砖块高度`

其中，基准价通常是上一块砖的收盘价（对于上涨趋势）或开盘价（对于下跌趋势）。

## 绘制转型图的代码实现

理解了原理后，我们通过代码来实现转型图的绘制。首先需要定义计算砖块四个价格（高、低、开、收）的逻辑。

以下是绘制转型图的关键代码步骤：

```pinescript
// 1. 定义砖块高度参数
input brickLength = 10;

// 2. 声明存储砖块价格的变量
var float brickHigh = 0.0;
var float brickLow = 0.0;
var float brickOpen = 0.0;
var float brickClose = 0.0;

// 3. 初始化第一块砖
if barnumber == 1 then begin
    brickOpen := close;
    brickHigh := close;
    brickLow := close;
    brickClose := close;
end;

// 4. 判断逻辑与价格计算
if currentbar > 1 then begin
    // 上涨砖块条件：当前价格 >= 前砖最高价 + 砖块高度
    if close >= brickHigh + brickLength then begin
        brickHigh := floor(close / brickLength) * brickLength;
        brickLow := brickHigh - brickLength;
        brickOpen := brickLow;
        brickClose := brickHigh;
    end
    // 下跌砖块条件：当前价格 <= 前砖最低价 - 砖块高度
    else if close <= brickLow - brickLength then begin
        brickLow := ceil(close / brickLength) * brickLength;
        brickHigh := brickLow + brickLength;
        brickOpen := brickHigh;
        brickClose := brickLow;
    end;
end;

// 5. 绘制砖块
plotPaintBar(brickHigh, brickLow, brickOpen, brickClose, “UpBar”, color.red, “DownBar”, color.green);
```

![](img/242e590020b79e9abef2d9e5440aaec5_9.png)

![](img/242e590020b79e9abef2d9e5440aaec5_11.png)

代码说明：
*   使用 `floor()` 函数确保上涨砖块的最高价是砖块高度的整数倍。
*   使用 `ceil()` 函数确保下跌砖块的最低价是砖块高度的整数倍。
*   `plotPaintBar` 函数用于绘制具有颜色区分的砖块。

![](img/242e590020b79e9abef2d9e5440aaec5_13.png)

![](img/242e590020b79e9abef2d9e5440aaec5_15.png)

## 趋势线基础与应用

![](img/242e590020b79e9abef2d9e5440aaec5_17.png)

绘制完转型图，我们进入另一个实用工具——趋势线的学习。趋势线是判断市场方向的重要辅助工具，在MC中既可以手动绘制，也可以通过脚本自动生成。

![](img/242e590020b79e9abef2d9e5440aaec5_19.png)

![](img/242e590020b79e9abef2d9e5440aaec5_21.png)

![](img/242e590020b79e9abef2d9e5440aaec5_23.png)

![](img/242e590020b79e9abef2d9e5440aaec5_25.png)

![](img/242e590020b79e9abef2d9e5440aaec5_27.png)

一条趋势线由两个点唯一确定。在图表上，每个点由X轴（日期时间）和Y轴（价格）坐标定义。

![](img/242e590020b79e9abef2d9e5440aaec5_29.png)

![](img/242e590020b79e9abef2d9e5440aaec5_31.png)

![](img/242e590020b79e9abef2d9e5440aaec5_33.png)

以下是自动绘制一条趋势线并向右延伸的代码：

![](img/242e590020b79e9abef2d9e5440aaec5_35.png)

![](img/242e590020b79e9abef2d9e5440aaec5_37.png)

```pinescript
// 定义趋势线的起点和终点
date1 = 1120231; // 起始日期
time1 = 1015;    // 起始时间
price1 = 3683;   // 起始价格

date2 = 1120231; // 结束日期
time2 = 1440;    // 结束时间
price2 = 3703;   // 结束价格

// 绘制趋势线，并获取其ID
value1 = TL_New(date1, time1, price1, date2, time2, price2);

![](img/242e590020b79e9abef2d9e5440aaec5_39.png)

![](img/242e590020b79e9abef2d9e5440aaec5_41.png)

![](img/242e590020b79e9abef2d9e5440aaec5_43.png)

// 设置趋势线向右延伸
TL_SetExtRight(value1, true);

// 在最后一根K线位置获取趋势线的当前值
if lastbaronchart then begin
    value2 = TL_GetValue(value1, date, time);
    print(value2); // 输出该时间点趋势线对应的价格
end;
```

![](img/242e590020b79e9abef2d9e5440aaec5_45.png)

![](img/242e590020b79e9abef2d9e5440aaec5_47.png)

代码说明：
*   `TL_New` 函数用于创建一条新的趋势线，返回该趋势线的唯一ID。
*   `TL_SetExtRight` 函数可以将趋势线向右无限延伸。
*   `TL_GetValue` 函数至关重要，它允许我们在任意日期时间获取趋势线上对应的价格值，从而可以将趋势线价格用于条件判断和交易信号生成。

![](img/242e590020b79e9abef2d9e5440aaec5_49.png)

## 其他绘图相关函数简介

![](img/242e590020b79e9abef2d9e5440aaec5_51.png)

![](img/242e590020b79e9abef2d9e5440aaec5_53.png)

除了上述核心内容，MC还提供了一些其他用于控制图表显示的实用函数：

![](img/242e590020b79e9abef2d9e5440aaec5_55.png)

![](img/242e590020b79e9abef2d9e5440aaec5_57.png)

![](img/242e590020b79e9abef2d9e5440aaec5_59.png)

以下是几个常用函数及其作用：
*   `NoPlot`：用于隐藏图表上已绘制的特定指标线。
*   `SetPlotWidth`：动态设置指定编号的指标线的粗细。
*   `SetPlotColor`：动态设置指定编号的指标线的颜色。
*   `GetBackgroundColor`：获取当前图表的背景色。
*   `GetPlotWidth`：获取指定编号的指标线的当前宽度。

![](img/242e590020b79e9abef2d9e5440aaec5_61.png)

![](img/242e590020b79e9abef2d9e5440aaec5_63.png)

![](img/242e590020b79e9abef2d9e5440aaec5_65.png)

这些函数增强了我们对图表视觉效果和指标交互的控制能力。

## 课程总结

![](img/242e590020b79e9abef2d9e5440aaec5_67.png)

本节课中我们一起学习了两个重要主题。首先，我们深入探讨了转型图（砖形图）的绘制原理，并亲手编写代码实现了它，理解了其抛开时间、仅关注价格变动的核心逻辑。其次，我们初步掌握了趋势线的使用方法，学习了如何通过 `TL_New`、`TL_SetExtRight` 和 `TL_GetValue` 等函数自动绘制、延伸趋势线并获取其数值，为后续将趋势线应用于交易策略打下了基础。