# 量化投资与Python：P40：4-获取给定区间全部数据 📊

![](img/38e2bfc26474eae3cbf2f707c998fdd9_1.png)

在本节课中，我们将学习如何通过编写循环，从给定的时间序列中获取每一天的数据，并将这些数据整理成一个结构化的总表。这个过程是量化分析中处理时间序列数据的基础。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_3.png)

![](img/38e2bfc26474eae3cbf2f707c998fdd9_5.png)

上一节我们介绍了如何获取单日数据，本节中我们来看看如何获取一个完整时间区间内的所有数据。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_7.png)

## 核心思路与步骤

获取一个区间内所有数据的核心思路是遍历日期序列中的每一天，分别获取当天的数据，然后将这些每日数据汇总到一个总的数据表中。

以下是实现这一目标的具体步骤：

![](img/38e2bfc26474eae3cbf2f707c998fdd9_9.png)

1.  **遍历日期序列**：使用 `for` 循环遍历给定的日期范围。
2.  **获取每日数据**：在循环中，针对每一天，调用数据获取函数。
3.  **处理数据维度**：获取的数据通常是多维的，需要提取出我们关心的指标值。
4.  **构建每日数据表**：将日期和对应的指标值组合成一个小的 `DataFrame`。
5.  **汇总到总表**：在循环外部初始化一个总 `DataFrame`，在每次循环中将每日数据表拼接到总表中。

## 代码实现详解

![](img/38e2bfc26474eae3cbf2f707c998fdd9_11.png)

首先，我们需要确定日期序列的长度，这决定了我们要循环多少次。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_13.png)

![](img/38e2bfc26474eae3cbf2f707c998fdd9_15.png)

```python
# 假设 dates 是我们的日期序列
total_days = len(dates)
```

接下来，我们初始化一个空的 `DataFrame` 作为总表，用于存放最终汇总的数据。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_17.png)

```python
import pandas as pd
total_data = pd.DataFrame()
```

现在，我们开始编写循环，遍历每一天。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_19.png)

```python
for i in range(total_days):
    # 获取第 i 天的数据
    daily_raw_data = get_data(i)  # 假设 get_data 是获取数据的函数
```

![](img/38e2bfc26474eae3cbf2f707c998fdd9_21.png)

获取到的 `daily_raw_data` 可能是一个三维数组。根据之前的学习，我们关心的指标值在第三个维度中。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_23.png)

```python
    # 提取我们需要的指标值，例如索引为 [0, 2, :]
    daily_value = daily_raw_data[0, 2, :]
```

有了每日的指标值后，我们需要将其与对应的日期一起，构建成一个结构清晰的 `DataFrame`。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_25.png)

![](img/38e2bfc26474eae3cbf2f707c998fdd9_27.png)

```python
    # 构建每日数据的小表
    daily_df = pd.DataFrame(daily_value, columns=[‘factor_value‘])
    # 添加日期列
    daily_df[‘date‘] = dates[i]
```

![](img/38e2bfc26474eae3cbf2f707c998fdd9_29.png)

最后，也是关键的一步，将这个每日的小表拼接到我们之前创建的总表中。

```python
    # 将每日数据拼接到总表
    total_data = pd.concat([total_data, daily_df], ignore_index=True)
```

循环结束后，`total_data` 这个 `DataFrame` 就包含了指定日期区间内每一天的指标数据。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_31.png)

![](img/38e2bfc26474eae3cbf2f707c998fdd9_33.png)

## 注意事项与优化

![](img/38e2bfc26474eae3cbf2f707c998fdd9_35.png)

*   **执行速度**：如果日期区间较长（例如一年），循环获取数据的过程可能会比较慢。这是正常现象，因为需要向服务器发起多次请求。
*   **数据赋值**：拼接操作 `pd.concat` 会返回一个新的 `DataFrame`，必须将其赋值回给 `total_data` 变量，否则总表不会更新。
*   **数据完整性**：最终的总表应包含至少两列：`date`（日期）和 `factor_value`（指标值）。根据需求，你还可以添加股票名称等列。

![](img/38e2bfc26474eae3cbf2f707c998fdd9_37.png)

![](img/38e2bfc26474eae3cbf2f707c998fdd9_39.png)

本节课中我们一起学习了如何通过循环遍历来获取一个时间区间内的全部数据，并将其整理成便于分析的 `DataFrame` 格式。虽然过程略显繁琐，但这是处理时间序列数据、进行历史回测或批量分析的必备技能。掌握这个方法后，你就可以对任意时间段的数据进行统一的获取和处理了。