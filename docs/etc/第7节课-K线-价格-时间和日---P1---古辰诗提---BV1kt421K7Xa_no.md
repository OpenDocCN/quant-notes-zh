# 从零开始量化：7：K线、价格、时间和日期相关关键字 - P1

![](img/a554de882a09d05b098c021524333455_1.png)

![](img/a554de882a09d05b098c021524333455_3.png)

![](img/a554de882a09d05b098c021524333455_5.png)

在本节课中，我们将学习MC语言中与K线、价格、时间和日期相关的核心关键字。我们将了解如何获取K线的绝对与相对编号、周期、状态，以及如何处理日期和时间数据。同时，我们也会探讨如何在信号中处理K线内部的Tick数据。

上一节我们介绍了`current bar`等相对编号的概念，本节中我们来看看绝对编号以及其他与K线相关的关键字。

## 绝对编号与相对编号

![](img/a554de882a09d05b098c021524333455_7.png)

![](img/a554de882a09d05b098c021524333455_9.png)

`current bar`表示当前K线的相对编号，它是相对于`max bars back`（参考K线数量）的编号。而绝对编号则不考虑`max bars back`，是从图表数据开头开始计数的编号。绝对编号的关键字通常在前面加上`symbol`或`simple`。

例如，`symbol current bar`或`simple current bar`返回的就是绝对编号。它们与相对编号的关系可以用以下公式表示：

**`simple current bar` = `current bar` + `max bars back`**

![](img/a554de882a09d05b098c021524333455_11.png)

![](img/a554de882a09d05b098c021524333455_13.png)

![](img/a554de882a09d05b098c021524333455_15.png)

![](img/a554de882a09d05b098c021524333455_17.png)

`max bars back`本身也是一个关键字，可以在代码中获取其值。在指标和信号中，`max bars back`的行为有所不同：
*   在**指标**中，`max bars back`会根据需要实时更新。
*   在**信号**中，`max bars back`必须手动设置（默认为50），且每根K线更新一次。

以下是演示绝对编号与相对编号区别的代码示例：
```easyLanguage
// 输出相对编号和绝对编号
Print(“Current Bar: “, current bar, “ | Symbol Current Bar: “, symbol current bar);
// 验证关系
Print(“Max Bars Back: “, max bars back);
Print(“Current Bar + Max Bars Back: “, current bar + max bars back);
```

## K线编号函数：`BarNumber`

与`current bar`不同，`BarNumber`是一个**序列函数**，这意味着它可以被“回溯”调用，即获取历史K线的编号。`current bar`关键字则不能直接用于回溯。

以下是`BarNumber`的用法示例：
```easyLanguage
// BarNumber 是一个序列函数，可以回溯
value2 = BarNumber[20]; // 获取20根K线之前的BarNumber
Print(“Current BarNumber: “, BarNumber, “ | BarNumber 20 bars ago: “, value2);
```
`BarNumber`通常在图表的绘图函数中非常有用。

## K线周期与类型

`BarInterval`关键字返回图表上K线周期的数值。例如，在5分钟图表上，`BarInterval`返回5。

`BarType`关键字返回一个数值，表示K线的类型（如日内、日线等），但其分类比较粗略。为了获得更详细的周期类型（如分钟、小时），可以使用`BarType_ex`（扩展类型）。

以下是相关示例：
```easyLanguage
Print(“Bar Interval: “, BarInterval); // 例如：5 (5分钟图)
Print(“Bar Type: “, BarType); // 例如：1 (日内)
Print(“Bar Type (Extended): “, BarType_ex); // 例如：2 (分钟类型)
```

![](img/a554de882a09d05b098c021524333455_19.png)

## K线状态：`BarStatus`

![](img/a554de882a09d05b098c021524333455_21.png)

`BarStatus`返回一个数值，表示指定数据序列中，最近一个Tick相对于当前K线的状态。
*   **0**: 表示K线开盘时的Tick。
*   **1**: 表示K线内部的Tick。
*   **2**: 表示K线收盘时的Tick。

![](img/a554de882a09d05b098c021524333455_23.png)

![](img/a554de882a09d05b098c021524333455_25.png)

![](img/a554de882a09d05b098c021524333455_27.png)

其语法为`BarStatus(DataN)`，其中`DataN`指定数据编号（如`Data2`, `Data3`），不填则默认为当前图表数据。

在信号中，默认每根K线只在结束时执行一次代码，无法感知K线内部的Tick状态。为了在信号中也能响应Tick，需要使用`intrabar persist`变量声明。

## `Intrabar Persist` 与 Tick级处理

`intrabar persist`用于声明变量，使该变量能在K线内部随着每个Tick更新，而不是只在K线结束时更新一次。要启用此功能，还需在信号属性中开启**“使用精细资料（逐笔Tick）”**进行回测，并在代码开头使用`[IntrabarOrderGeneration = true]`指令。

![](img/a554de882a09d05b098c021524333455_29.png)

![](img/a554de882a09d05b098c021524333455_31.png)

以下是一个对比示例：
```easyLanguage
[IntrabarOrderGeneration = true]

Vars:
    intrabar persist Add_1(0), // 使用 intrabar persist
    Add_2(0); // 普通变量

![](img/a554de882a09d05b098c021524333455_33.png)

![](img/a554de882a09d05b098c021524333455_35.png)

Add_1 = Add_1 + 1; // 每个Tick自增1
Add_2 = Add_2 + 1; // 每根K线自增1

Print(“Add_1 (Tick级): “, Add_1, “ | Add_2 (K线级): “, Add_2);
```
运行后，`Add_1`的数值会远大于`Add_2`，因为它在每笔Tick都进行了累加。这使得在信号中基于`BarStatus`和精确Tick价格（如`Close`，在Tick未完成时代表最新价）进行复杂逻辑判断成为可能，例如在K线开始时，基于前几笔Tick的中间价挂单，以获得进场优势。

**注意**：使用Tick级精细回测会极大增加计算负荷，通常仅在需要极高精度控制委托时使用。

## 时间与日期关键字

在MC中，K线所附带的时间戳通常以该K线的**结束时间**为准。这与一些其他平台（如文华）以开始时间为准不同。

日期和时间关键字返回的是数值型数据，便于直接进行大小比较和计算。

*   `Date`: 返回从1900年1月1日至今的天数。**注意**：年份部分在2000年之前是两位数（如99），在2000年及之后是三位数（如122代表2022年），这是为了便于数值运算和避免前导零带来的混淆。
*   `Time`: 返回从午夜起计算的分钟数（0-1439）。
*   `Time_s`: 返回从午夜起计算的秒数（0-86399）。
*   对应的`Symbol`版本（如`Symbol Date`, `Symbol Time`）返回的是不考虑`max bars back`限制的绝对时间。

![](img/a554de882a09d05b098c021524333455_37.png)

以下是示例：
```easyLanguage
Print(“Date: “, Date); // 例如：1221109 (2022年11月9日)
Print(“Time: “, Time); // 例如：1500 (下午3点)
Print(“Time_s: “, Time_s); // 例如：150000 (下午3点0分0秒)
```
虽然这些值是数值，可以直接进行加减比较（例如判断是否在某个交易时段内），但进行跨日期的复杂运算（如“30天前”）并不直观，通常需要借助其他日期函数来完成。

![](img/a554de882a09d05b098c021524333455_39.png)

![](img/a554de882a09d05b098c021524333455_41.png)

---

![](img/a554de882a09d05b098c021524333455_43.png)

本节课中我们一起学习了MC语言中关于K线、价格、时间和日期的核心关键字。我们区分了K线的绝对编号与相对编号，认识了`BarNumber`、`BarInterval`、`BarStatus`等函数的用途，并深入了解了如何通过`intrabar persist`在信号中实现Tick级别的精细控制。最后，我们掌握了日期和时间关键字的特性和基本用法。理解这些基础概念是构建复杂量化策略的重要基石。