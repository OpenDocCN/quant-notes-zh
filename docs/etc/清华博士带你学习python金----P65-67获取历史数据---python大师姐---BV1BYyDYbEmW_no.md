# Python金融量化投资：P65：获取历史数据 📈

![](img/15746812af95aa6901ea32fa6e536643_1.png)

在本节课中，我们将学习如何获取股票的历史行情数据。这是构建量化交易策略的基础，因为我们需要基于历史数据进行分析和回测。我们将实现一个名为 `attribute_history` 的函数，它可以获取指定股票在指定时间段内的历史数据。

![](img/15746812af95aa6901ea32fa6e536643_3.png)

![](img/15746812af95aa6901ea32fa6e536643_5.png)

![](img/15746812af95aa6901ea32fa6e536643_7.png)

---

![](img/15746812af95aa6901ea32fa6e536643_9.png)

## 核心概念与准备工作

在开始编写代码之前，我们需要明确几个核心概念和准备工作。

![](img/15746812af95aa6901ea32fa6e536643_11.png)

![](img/15746812af95aa6901ea32fa6e536643_13.png)

![](img/15746812af95aa6901ea32fa6e536643_15.png)

**1. 数据来源与格式**
我们将从本地CSV文件或在线API获取数据。数据通常包含开盘价(`open`)、收盘价(`close`)、最高价(`high`)、最低价(`low`)和成交量(`volume`)等列。

![](img/15746812af95aa6901ea32fa6e536643_17.png)

![](img/15746812af95aa6901ea32fa6e536643_19.png)

**2. 回测上下文**
在回测系统中，我们需要一个“当前日期”的概念。这个日期会随着回测的进行而逐日推进。我们将使用一个全局的 `context` 对象来存储这个信息，包括：
*   `cash`: 初始资金。
*   `start_date`: 回测开始日期。
*   `end_date`: 回测结束日期。
*   `dt`: 模拟执行到的当前日期。

![](img/15746812af95aa6901ea32fa6e536643_21.png)

![](img/15746812af95aa6901ea32fa6e536643_23.png)

**3. 交易日历**
股票市场并非每天都是交易日（如周末、节假日）。因此，在计算历史数据的起止日期时，我们必须基于交易日历进行，以确保获取的是有效的交易日数据。

---

![](img/15746812af95aa6901ea32fa6e536643_25.png)

![](img/15746812af95aa6901ea32fa6e536643_27.png)

## 实现 `attribute_history` 函数

![](img/15746812af95aa6901ea32fa6e536643_29.png)

![](img/15746812af95aa6901ea32fa6e536643_31.png)

![](img/15746812af95aa6901ea32fa6e536643_33.png)

上一节我们介绍了获取历史数据所需的核心概念。本节中，我们来看看如何具体实现 `attribute_history` 函数。这个函数的目标是：给定一个股票代码和要获取的天数，返回从“当前日期”往前推指定天数的历史数据。

![](img/15746812af95aa6901ea32fa6e536643_35.png)

![](img/15746812af95aa6901ea32fa6e536643_37.png)

函数的参数设计如下：
*   `security`: 股票代码。
*   `count`: 要获取的历史天数。
*   `field`: 需要获取的数据列（如 `[‘open‘, ‘close‘]`），默认为全部列。

![](img/15746812af95aa6901ea32fa6e536643_39.png)

![](img/15746812af95aa6901ea32fa6e536643_41.png)

以下是实现该函数的关键步骤：

**步骤一：确定历史数据的结束日期**
在回测中，“今天”的数据是尚未发生的，因此我们获取的历史数据应截止到“昨天”。我们可以通过 `context.dt` 减去一天来计算。
```python
import datetime
end_date = context.dt - datetime.timedelta(days=1)
```

**步骤二：确定历史数据的开始日期**
我们需要根据 `count` 参数，从交易日历中找出 `end_date` 之前的第 `count` 个交易日。这需要用到我们之前准备好的交易日历数据 `trade_cal`。

![](img/15746812af95aa6901ea32fa6e536643_43.png)

![](img/15746812af95aa6901ea32fa6e536643_45.png)

以下是计算逻辑：
1.  从 `trade_cal` 中筛选出所有早于或等于 `end_date` 的交易日。
2.  从这些交易日中，取出倒数第 `count` 个交易日的日期，即为 `start_date`。

![](img/15746812af95aa6901ea32fa6e536643_47.png)

![](img/15746812af95aa6901ea32fa6e536643_49.png)

![](img/15746812af95aa6901ea32fa6e536643_51.png)

```python
# 假设 trade_cal 是一个DataFrame，包含 ‘calendar_date‘ 和 ‘is_open‘ 列
# ‘is_open‘ 为 1 表示是交易日
valid_dates = trade_cal[(trade_cal[‘is_open‘] == 1) & (trade_cal[‘calendar_date‘] <= end_date)]
start_date = valid_dates.iloc[-count][‘calendar_date‘]
```

![](img/15746812af95aa6901ea32fa6e536643_53.png)

![](img/15746812af95aa6901ea32fa6e536643_55.png)

**步骤三：调用底层数据获取函数**
有了 `start_date` 和 `end_date`，我们就可以调用另一个函数 `attribute_daterange_history` 来获取这两个具体日期之间的数据。`attribute_history` 函数的核心工作就是完成日期计算，然后调用这个底层函数。

![](img/15746812af95aa6901ea32fa6e536643_57.png)

![](img/15746812af95aa6901ea32fa6e536643_59.png)

```python
def attribute_history(security, count, field=None):
    # ... 日期计算逻辑 ...
    return attribute_daterange_history(security, start_date, end_date, field)
```

![](img/15746812af95aa6901ea32fa6e536643_61.png)

![](img/15746812af95aa6901ea32fa6e536643_63.png)

---

![](img/15746812af95aa6901ea32fa6e536643_65.png)

![](img/15746812af95aa6901ea32fa6e536643_67.png)

![](img/15746812af95aa6901ea32fa6e536643_69.png)

## 实现 `attribute_daterange_history` 函数

![](img/15746812af95aa6901ea32fa6e536643_71.png)

![](img/15746812af95aa6901ea32fa6e536643_73.png)

上一节我们实现了基于天数的历史数据查询。本节中，我们来看看更底层的 `attribute_daterange_history` 函数，它负责根据具体的起止日期获取数据。

![](img/15746812af95aa6901ea32fa6e536643_75.png)

这个函数接受明确的 `start_date` 和 `end_date` 作为参数。其核心任务是尝试从本地读取数据文件，如果本地没有，则从网络API下载。

![](img/15746812af95aa6901ea32fa6e536643_77.png)

以下是该函数的实现要点：

![](img/15746812af95aa6901ea32fa6e536643_79.png)

![](img/15746812af95aa6901ea32fa6e536643_81.png)

**步骤一：尝试读取本地数据**
我们首先尝试从本地CSV文件（文件名通常为 `{股票代码}.csv`）中读取数据。使用 `pandas` 库可以方便地做到这一点，并将日期列设置为索引。

![](img/15746812af95aa6901ea32fa6e536643_83.png)

![](img/15746812af95aa6901ea32fa6e536643_85.png)

```python
import pandas as pd
try:
    df = pd.read_csv(f‘{security}.csv‘, index_col=‘date‘, parse_dates=[‘date‘])
except FileNotFoundError:
    # 如果本地文件不存在，则进入下一步
    pass
```

![](img/15746812af95aa6901ea32fa6e536643_87.png)

![](img/15746812af95aa6901ea32fa6e536643_89.png)

**步骤二：本地无数据时从网络获取**
如果本地文件不存在（触发 `FileNotFoundError` 异常），我们就使用 `tushare` 等金融数据包的API在线获取数据。

```python
    import tushare as ts
    df = ts.get_k_data(security, start=start_date.strftime(‘%Y-%m-%d‘), end=end_date.strftime(‘%Y-%m-%d‘))
    df.set_index(‘date‘, inplace=True) # 同样将日期设为索引
```

![](img/15746812af95aa6901ea32fa6e536643_91.png)

![](img/15746812af95aa6901ea32fa6e536643_93.png)

![](img/15746812af95aa6901ea32fa6e536643_95.png)

**步骤三：筛选指定日期范围和字段**
无论数据来自本地还是网络，我们最终都得到一个包含所有历史数据的DataFrame `df`。我们需要从中截取出 `start_date` 到 `end_date` 之间的数据行，并根据 `field` 参数筛选出需要的列。

![](img/15746812af95aa6901ea32fa6e536643_97.png)

```python
    # 按日期范围切片
    result_df = df.loc[start_date:end_date]
    # 按字段筛选列，如果field为None则返回所有列
    if field is not None:
        result_df = result_df[field]
    return result_df
```

![](img/15746812af95aa6901ea32fa6e536643_99.png)

通过 `try-except` 结构，我们实现了“本地缓存优先，网络获取兜底”的数据获取策略，既提高了效率，也保证了数据的可用性。

![](img/15746812af95aa6901ea32fa6e536643_101.png)

---

## 课程总结与回顾

![](img/15746812af95aa6901ea32fa6e536643_103.png)

本节课中，我们一起学习了如何为量化回测系统构建历史数据获取模块。

![](img/15746812af95aa6901ea32fa6e536643_105.png)

![](img/15746812af95aa6901ea32fa6e536643_107.png)

![](img/15746812af95aa6901ea32fa6e536643_109.png)

我们首先明确了获取历史数据需要**回测上下文**（`context`）来跟踪当前模拟日期，并需要依赖**交易日历**来准确计算交易日。接着，我们实现了两个核心函数：
1.  **`attribute_history(security, count, field)`**: 这是面向策略作者的主要接口，通过指定天数来获取历史数据。
2.  **`attribute_daterange_history(security, start_date, end_date, field)`**: 这是底层数据获取函数，负责根据具体日期范围，智能地从本地文件或网络API加载数据，并返回处理好的DataFrame。

![](img/15746812af95aa6901ea32fa6e536643_111.png)

这个模块是量化策略进行历史分析和技术指标计算的基础。有了可靠的历史数据，我们就可以在接下来的课程中，进一步实现交易信号生成、订单执行和投资组合管理等更复杂的功能。