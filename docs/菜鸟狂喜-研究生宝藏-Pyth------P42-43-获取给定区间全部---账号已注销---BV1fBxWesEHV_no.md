# Python金融量化分析：P42：获取给定区间全部数据 📊

![](img/c2933d967670cc6a50f79d67fc0ffd24_0.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_2.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_4.png)

在本节课中，我们将学习如何通过循环遍历一个给定的日期区间，来获取每一天的金融因子数据，并将这些数据整理成一个结构化的DataFrame。这个过程是批量处理时间序列数据的基础。

![](img/c2933d967670cc6a50f79d67fc0ffd24_6.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_8.png)

## 概述

![](img/c2933d967670cc6a50f79d67fc0ffd24_10.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_12.png)

上一节我们介绍了如何获取单日的因子数据。本节中，我们来看看如何扩展这个方法，以获取一个连续时间段内的所有数据。核心思路是使用循环遍历日期序列，逐日获取数据，并将结果汇总。

![](img/c2933d967670cc6a50f79d67fc0ffd24_14.png)

## 数据获取与处理流程

![](img/c2933d967670cc6a50f79d67fc0ffd24_16.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_18.png)

以下是实现批量获取数据的主要步骤。

![](img/c2933d967670cc6a50f79d67fc0ffd24_20.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_22.png)

### 1. 确定日期区间长度

![](img/c2933d967670cc6a50f79d67fc0ffd24_24.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_26.png)

首先，我们需要确定要遍历的日期序列有多少天。这决定了循环的次数。

![](img/c2933d967670cc6a50f79d67fc0ffd24_28.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_30.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_32.png)

```python
# 假设 dates 是一个日期序列
total_days = len(dates)
```

![](img/c2933d967670cc6a50f79d67fc0ffd24_34.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_36.png)

### 2. 遍历每一天并获取数据

![](img/c2933d967670cc6a50f79d67fc0ffd24_38.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_40.png)

接下来，我们使用 `for` 循环遍历日期序列中的每一天。对于每一天，我们都调用获取单日数据的方法。

![](img/c2933d967670cc6a50f79d67fc0ffd24_42.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_44.png)

```python
for i in range(total_days):
    # 获取第 i 天的数据
    daily_data = get_data_for_single_day(i)
```

![](img/c2933d967670cc6a50f79d67fc0ffd24_46.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_48.png)

在获取单日数据时，返回的结果通常是一个多维数组。我们只关心其中存储实际数值的部分。

![](img/c2933d967670cc6a50f79d67fc0ffd24_50.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_52.png)

```python
# 假设返回的数据中，有用的部分在第三个维度（索引为2）
useful_values = daily_data[:, :, 2]
```

![](img/c2933d967670cc6a50f79d67fc0ffd24_54.png)

### 3. 构建每日数据记录

![](img/c2933d967670cc6a50f79d67fc0ffd24_56.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_58.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_60.png)

对于每一天获取到的数据，我们需要将其整理成一个包含日期、股票名称和指标值的记录。我们创建一个临时的DataFrame来存储这些信息。

![](img/c2933d967670cc6a50f79d67fc0ffd24_62.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_64.png)

```python
import pandas as pd

![](img/c2933d967670cc6a50f79d67fc0ffd24_66.png)

# 为当前第 i 天的数据创建DataFrame
# 首先创建指标值列
daily_df = pd.DataFrame(useful_values, columns=[‘factor_value‘])

![](img/c2933d967670cc6a50f79d67fc0ffd24_68.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_70.png)

# 然后添加日期列
daily_df[‘date‘] = dates[i]
```

![](img/c2933d967670cc6a50f79d67fc0ffd24_72.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_74.png)

### 4. 汇总所有日期的数据

![](img/c2933d967670cc6a50f79d67fc0ffd24_76.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_78.png)

在循环内部，我们每处理完一天的数据，就需要将这个临时的 `daily_df` 拼接到一个总的DataFrame中。在循环开始前，我们需要初始化这个总表。

![](img/c2933d967670cc6a50f79d67fc0ffd24_80.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_82.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_84.png)

```python
# 初始化一个空的DataFrame作为总表
total_dataframe = pd.DataFrame()

![](img/c2933d967670cc6a50f79d67fc0ffd24_86.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_88.png)

for i in range(total_days):
    # ... (获取数据并构建daily_df的代码)

    # 将当天的数据拼接到总表中
    total_dataframe = pd.concat([total_dataframe, daily_df], ignore_index=True)
```

![](img/c2933d967670cc6a50f79d67fc0ffd24_90.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_92.png)

**注意**：`pd.concat` 操作会返回一个新的DataFrame，必须将其赋值回 `total_dataframe` 变量，否则拼接无效。

![](img/c2933d967670cc6a50f79d67fc0ffd24_94.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_96.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_98.png)

### 5. 检查最终结果

![](img/c2933d967670cc6a50f79d67fc0ffd24_100.png)

循环执行完毕后，我们可以查看 `total_dataframe` 的前几行，以确认数据是否按预期包含了日期、股票和因子值这三类信息。

![](img/c2933d967670cc6a50f79d67fc0ffd24_102.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_104.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_106.png)

```python
print(total_dataframe.head())
```

![](img/c2933d967670cc6a50f79d67fc0ffd24_108.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_110.png)

## 总结

![](img/c2933d967670cc6a50f79d67fc0ffd24_112.png)

![](img/c2933d967670cc6a50f79d67fc0ffd24_114.png)

本节课中我们一起学习了如何批量获取一个给定时间区间内的全部金融因子数据。我们通过循环遍历日期，逐日获取并处理数据，最后使用 `pd.concat` 方法将所有日期的数据汇总到一个完整的DataFrame中。这个方法虽然步骤稍多，但它是处理时间序列批量数据的基础。请注意，如果时间区间较长，此过程可能会运行较长时间。