# Python金融量化投资分析：P64：上下文数据存储 📊

![](img/b420f5bb580025920a8fabee528a98e4_1.png)

![](img/b420f5bb580025920a8fabee528a98e4_3.png)

![](img/b420f5bb580025920a8fabee528a98e4_4.png)

在本节课中，我们将学习如何构建一个简易的回测框架，核心是创建一个用于存储回测过程中所有关键信息的上下文（Context）类。我们将定义初始资金、时间范围、持仓信息等属性，并获取指定时间范围内的交易日历。

![](img/b420f5bb580025920a8fabee528a98e4_6.png)

---

## 导入必要库

![](img/b420f5bb580025920a8fabee528a98e4_8.png)

![](img/b420f5bb580025920a8fabee528a98e4_9.png)

首先，我们需要导入进行数据分析和获取金融数据所必需的Python库。

![](img/b420f5bb580025920a8fabee528a98e4_11.png)

```python
import numpy as np
import pandas as pd
import tushare as ts
```

![](img/b420f5bb580025920a8fabee528a98e4_13.png)

---

![](img/b420f5bb580025920a8fabee528a98e4_15.png)

## 定义Context类

上一节我们介绍了构建回测框架的准备工作。本节中，我们来看看如何定义核心的`Context`类，它将存储回测所需的所有上下文信息。

![](img/b420f5bb580025920a8fabee528a98e4_17.png)

![](img/b420f5bb580025920a8fabee528a98e4_19.png)

`Context`类的初始化函数（`__init__`）需要接收并存储几个关键参数：初始资金（`cash`）、回测开始日期（`start_date`）和结束日期（`end_date`）。

![](img/b420f5bb580025920a8fabee528a98e4_21.png)

```python
class Context:
    def __init__(self, cash, start_date, end_date):
        self.cash = cash
        self.start_date = start_date
        self.end_date = end_date
```

![](img/b420f5bb580025920a8fabee528a98e4_23.png)

![](img/b420f5bb580025920a8fabee528a98e4_25.png)

![](img/b420f5bb580025920a8fabee528a98e4_27.png)

---

![](img/b420f5bb580025920a8fabee528a98e4_28.png)

## 初始化类属性

![](img/b420f5bb580025920a8fabee528a98e4_30.png)

除了接收的参数，`Context`类还需要初始化其他几个重要的属性。

![](img/b420f5bb580025920a8fabee528a98e4_32.png)

以下是需要在`__init__`方法中初始化的属性列表：
*   `positions`：一个字典，用于记录持仓信息。初始时为空字典。
*   `benchmark`：用于记录基准参照（例如沪深300指数）。初始时可设为`None`。
*   `date_range`：一个列表，存储`start_date`和`end_date`之间的所有交易日。

![](img/b420f5bb580025920a8fabee528a98e4_34.png)

![](img/b420f5bb580025920a8fabee528a98e4_36.png)

为了获取交易日历，我们使用`tushare`包的`trade_cal`接口。为了提高效率，可以先将完整日历保存到本地CSV文件。

![](img/b420f5bb580025920a8fabee528a98e4_38.png)

![](img/b420f5bb580025920a8fabee528a98e4_40.png)

```python
        # 获取并保存交易日历（首次运行后注释掉）
        # trade_cal_df = ts.trade_cal()
        # trade_cal_df.to_csv(‘trade_cal.csv‘, index=False)

        # 从本地文件读取交易日历
        trade_cal_df = pd.read_csv(‘trade_cal.csv‘)
```

![](img/b420f5bb580025920a8fabee528a98e4_42.png)

接着，我们使用布尔索引从`trade_cal_df`中筛选出在指定时间范围内且是交易日（`is_open == 1`）的日期，并将其转换为数组存入`date_range`。

![](img/b420f5bb580025920a8fabee528a98e4_44.png)

```python
        # 筛选指定时间范围内的交易日
        condition = (trade_cal_df[‘is_open‘] == 1) & \
                    (trade_cal_df[‘cal_date‘] >= start_date) & \
                    (trade_cal_df[‘cal_date‘] <= end_date)
        self.date_range = trade_cal_df.loc[condition, ‘cal_date‘].values
```

![](img/b420f5bb580025920a8fabee528a98e4_46.png)

![](img/b420f5bb580025920a8fabee528a98e4_48.png)

---

![](img/b420f5bb580025920a8fabee528a98e4_50.png)

## 创建全局g对象

在量化平台中，通常有一个全局对象（例如聚宽中的`g`）用于存储跨函数访问的临时变量。我们可以简单地用一个空类来实现这个功能。

![](img/b420f5bb580025920a8fabee528a98e4_52.png)

```python
class Global:
    pass

![](img/b420f5bb580025920a8fabee528a98e4_54.png)

g = Global()
# 之后可以通过 g.变量名 = 值 来动态创建属性
```

---

![](img/b420f5bb580025920a8fabee528a98e4_56.png)

## 测试Context类

![](img/b420f5bb580025920a8fabee528a98e4_58.png)

![](img/b420f5bb580025920a8fabee528a98e4_60.png)

现在，我们可以实例化`Context`类并进行测试，确保`date_range`等属性被正确计算。

![](img/b420f5bb580025920a8fabee528a98e4_62.png)

```python
# 实例化Context对象
context = Context(cash=100000, start_date=‘20160101‘, end_date=‘20170101‘)

![](img/b420f5bb580025920a8fabee528a98e4_64.png)

![](img/b420f5bb580025920a8fabee528a98e4_66.png)

# 打印交易日范围进行验证
print(context.date_range)
```

![](img/b420f5bb580025920a8fabee528a98e4_68.png)

运行上述代码，将输出2016年1月1日至2017年1月1日之间的所有交易日数组，证明我们的上下文数据存储功能工作正常。

---

![](img/b420f5bb580025920a8fabee528a98e4_70.png)

本节课中我们一起学习了如何构建一个简易回测框架的上下文存储部分。我们定义了`Context`类来集中管理资金、时间、持仓和交易日历，并创建了全局`g`对象。这个基础结构将作为我们后续实现数据获取和策略回测的基石。下一节，我们将探讨如何在这个框架中获取历史价格数据。