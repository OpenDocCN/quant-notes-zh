# Python金融分析与量化交易实战：P28：04-4-获取给定区间全部数据 📊

![](img/21d61b12fcb8abb272deb9273fc30568_1.png)

![](img/21d61b12fcb8abb272deb9273fc30568_3.png)

在本节课中，我们将学习如何通过循环遍历，获取指定时间区间内每一天的因子数据，并将这些数据整理成一个结构化的表格。这是构建量化分析数据集的关键一步。

![](img/21d61b12fcb8abb272deb9273fc30568_5.png)

![](img/21d61b12fcb8abb272deb9273fc30568_7.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间区间内的全部数据。

![](img/21d61b12fcb8abb272deb9273fc30568_9.png)

![](img/21d61b12fcb8abb272deb9273fc30568_11.png)

## 核心思路与步骤

核心思路是：我们有一个日期序列，需要遍历其中的每一天，获取当天的因子数据，然后将所有日期的数据汇总到一个总表中。

以下是实现此功能的具体步骤：

![](img/21d61b12fcb8abb272deb9273fc30568_13.png)

1.  **遍历日期序列**：使用 `for` 循环遍历给定的日期范围。
2.  **获取每日数据**：在循环中，针对每一天，调用获取因子数据的函数。
3.  **处理数据格式**：从返回的三维数据中提取出我们需要的实际指标值。
4.  **构建每日数据表**：为每一天的数据创建一个临时的 `DataFrame`，包含日期和指标值两列。
5.  **汇总到总表**：将每个临时 `DataFrame` 拼接（`concat`）到一个总 `DataFrame` 中。

## 代码实现详解

![](img/21d61b12fcb8abb272deb9273fc30568_15.png)

首先，我们需要确定日期序列的长度，这代表了我们要处理的总天数。

![](img/21d61b12fcb8abb272deb9273fc30568_17.png)

```python
# 假设 date_series 是我们的日期序列
total_days = len(date_series)
```

![](img/21d61b12fcb8abb272deb9273fc30568_19.png)

接着，我们初始化一个空的 `DataFrame` 作为总表，用于存放最终汇总的数据。

![](img/21d61b12fcb8abb272deb9273fc30568_21.png)

```python
import pandas as pd
total_data = pd.DataFrame()
```

现在，开始遍历每一天。在循环中，我们需要执行以下操作：

*   **获取单日数据**：调用函数（例如 `get_factor_data`）并传入当前日期索引 `i`，以获取当天的三维数据。
*   **提取指标值**：从返回的三维数组中，提取出我们关心的实际指标值。根据之前的学习，有效数据通常在第三个维度。
    ```python
    # 假设 daily_raw_data 是获取到的三维数据
    # 提取第一个股票（索引0），第一个因子（索引0）的所有日期数据（索引:）
    daily_value = daily_raw_data[0, 0, :]
    ```
*   **构建每日数据表**：创建一个临时的 `DataFrame`，包含两列：`‘date’`（日期）和 `‘factor_value’`（因子值）。
    ```python
    daily_df = pd.DataFrame()
    daily_df[‘factor_value‘] = daily_value
    daily_df[‘date‘] = date_series[i]  # 假设 date_series[i] 能获取到具体日期
    ```
*   **拼接到总表**：使用 `pd.concat` 函数将当天的 `daily_df` 添加到总表 `total_data` 中。
    ```python
    total_data = pd.concat([total_data, daily_df])
    ```

![](img/21d61b12fcb8abb272deb9273fc30568_23.png)

![](img/21d61b12fcb8abb272deb9273fc30568_25.png)

将以上步骤整合到循环中，完整的代码逻辑如下：

```python
import pandas as pd

![](img/21d61b12fcb8abb272deb9273fc30568_27.png)

# 初始化总表
total_data = pd.DataFrame()

![](img/21d61b12fcb8abb272deb9273fc30568_29.png)

# 遍历日期序列中的每一天
for i in range(len(date_series)):
    # 1. 获取第 i 天的原始因子数据
    daily_raw_data = get_factor_data(i)  # 假设的获取数据函数

    # 2. 从三维数据中提取需要的指标值
    daily_value = daily_raw_data[0, 0, :]

    # 3. 构建当天的数据 DataFrame
    daily_df = pd.DataFrame()
    daily_df[‘factor_value‘] = daily_value
    daily_df[‘date‘] = date_series[i]

    # 4. 将当天数据拼接到总表
    total_data = pd.concat([total_data, daily_df])

# 循环结束后，查看总表的前几行
print(total_data.head())
```

**注意**：如果时间区间较长（例如一年），此循环操作可能需要一些时间来完成。执行完毕后，`total_data` 这个 `DataFrame` 将包含指定区间内每一天的日期和对应的因子指标值。

![](img/21d61b12fcb8abb272deb9273fc30568_31.png)

![](img/21d61b12fcb8abb272deb9273fc30568_33.png)

## 检查结果

![](img/21d61b12fcb8abb272deb9273fc30568_35.png)

运行完成后，我们可以使用 `total_data.head()` 来查看数据的前几行，确认其结构是否符合预期：包含“日期”和“因子值”两列，并且数据是按日期排列的。

![](img/21d61b12fcb8abb272deb9273fc30568_37.png)

![](img/21d61b12fcb8abb272deb9273fc30568_39.png)

本节课中我们一起学习了如何通过循环遍历和拼接操作，获取并整合一个给定时间区间内的全部因子数据。这个方法虽然步骤稍多，但它是批量处理时间序列数据、构建分析基础的常用且有效的手段。