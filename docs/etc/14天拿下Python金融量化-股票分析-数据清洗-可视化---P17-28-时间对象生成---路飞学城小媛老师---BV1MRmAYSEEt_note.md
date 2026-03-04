# Python金融量化：P17：时间对象生成 📅

![](img/3fcfbe98097e454034f41ad9c292c45e_1.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_3.png)

在本节课中，我们将学习如何使用Pandas库中的`date_range`函数来生成一个时间范围序列。这是处理时间序列数据时，创建规则时间索引的常用方法。

![](img/3fcfbe98097e454034f41ad9c292c45e_5.png)

上一节我们介绍了如何使用`to_datetime`方法将字符串批量转换为时间对象。本节中我们来看看如何直接生成一个时间范围。

![](img/3fcfbe98097e454034f41ad9c292c45e_7.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_9.png)

## 生成时间范围

![](img/3fcfbe98097e454034f41ad9c292c45e_11.png)

`pd.date_range()`函数可以方便地生成一个等间隔的时间序列。它有几个核心参数来控制序列的起始、结束和频率。

![](img/3fcfbe98097e454034f41ad9c292c45e_13.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_15.png)

以下是`date_range`函数的主要参数：

![](img/3fcfbe98097e454034f41ad9c292c45e_17.png)

*   **start**：指定时间序列的开始时间。
*   **end**：指定时间序列的结束时间。
*   **periods**：指定要生成的时间点的数量（长度）。
*   **freq**：指定时间序列的频率，例如“D”代表天，“H”代表小时。

![](img/3fcfbe98097e454034f41ad9c292c45e_19.png)

**注意**：你可以指定`start`和`end`，或者指定`start`和`periods`来生成序列。

## 频率参数详解

![](img/3fcfbe98097e454034f41ad9c292c45e_21.png)

`freq`参数非常强大，它决定了生成时间点的间隔。以下是常用的频率代码：

![](img/3fcfbe98097e454034f41ad9c292c45e_23.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_25.png)

*   `D`：日历日（默认）
*   `H`：小时
*   `W`：周（默认以周日为每周起始，`W-MON`表示以周一为起始）
*   `B`：工作日（排除周末）
*   `M`：月末
*   `MS`：月初
*   `Q`：季末
*   `A` 或 `Y`：年末
*   `T` 或 `min`：分钟
*   `S`：秒

![](img/3fcfbe98097e454034f41ad9c292c45e_27.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_29.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_31.png)

你甚至可以组合使用，例如`2H30min`表示每2小时30分钟。

![](img/3fcfbe98097e454034f41ad9c292c45e_33.png)

![](img/3fcfbe98097e454034f41ad9c292c45e_35.png)

## 时间序列的转换

![](img/3fcfbe98097e454034f41ad9c292c45e_37.png)

由`date_range`生成的对象是一个`DatetimeIndex`。你可以方便地将其转换为Python原生的`datetime`对象列表或字符串列表，以便在其他场景中使用。

![](img/3fcfbe98097e454034f41ad9c292c45e_39.png)

例如，使用`.to_pydatetime()`方法可以将其转换为Python `datetime`对象列表。

![](img/3fcfbe98097e454034f41ad9c292c45e_41.png)

本节课中我们一起学习了如何使用`pd.date_range()`函数生成时间范围序列，了解了其核心参数`start`、`end`、`periods`和`freq`的用法，并认识了常见的频率代码。这个功能极大地简化了规则时间索引的创建过程。