# Python金融分析与量化交易实战教程：P43：4-获取给定区间全部数据 📊

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_0.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_2.png)

## 概述
在本节课中，我们将学习如何从给定的时间区间内，循环获取每一天的金融数据，并将这些数据整理成一个结构化的总表。这个过程是量化分析中处理时间序列数据的基础。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_4.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_6.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_8.png)

上一节我们介绍了如何获取单日的因子数据。本节中，我们来看看如何扩展这个操作，以获取一个连续时间段内的所有数据。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_10.png)

---

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_12.png)

## 核心思路与步骤
核心思路是：我们有一个日期序列，需要遍历其中的每一天，获取当天的数据，然后将每天的数据汇总到一个总的数据表中。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_14.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_16.png)

以下是实现这一目标的具体步骤：

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_18.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_20.png)

1.  **遍历日期序列**：使用 `for` 循环遍历给定的所有交易日。
2.  **获取每日数据**：在循环中，针对每一天，调用获取因子数据的函数。
3.  **处理数据格式**：从返回的三维数据中，提取出我们需要的指标值。
4.  **构建每日数据表**：将提取出的数据与对应的日期、股票代码等信息，组合成一个小的 `DataFrame`。
5.  **汇总到总表**：在循环外部初始化一个空的 `DataFrame` 作为总表，在每次循环中将构建好的每日数据表拼接到总表中。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_22.png)

---

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_24.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_26.png)

## 代码实现详解

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_28.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_30.png)

### 1. 初始化总表
首先，我们需要创建一个空的 `DataFrame` 来存放最终汇总的所有数据。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_32.png)

```python
import pandas as pd

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_34.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_36.png)

# 初始化一个空的DataFrame作为总表
total_data = pd.DataFrame()
```

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_38.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_40.png)

### 2. 遍历日期并获取数据
假设我们有一个日期列表 `date_list`，它包含了我们要获取数据的所有交易日。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_42.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_44.png)

```python
# 假设 date_list 是包含所有目标日期的列表
for i in range(len(date_list)):
    current_date = date_list[i]
    # 获取第i天（即current_date）的因子数据
    # 假设 get_factor_data 是获取数据的函数，返回一个三维数组
    daily_raw_data = get_factor_data(current_date)
```

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_46.png)

### 3. 提取和处理每日数据
从获取的原始数据中，提取出我们关心的指标值。根据视频中的例子，有用的数据在第三个维度。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_48.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_50.png)

```python
    # 提取需要的指标值，假设我们需要的值在 daily_raw_data[0, 2, :] 这个位置
    # 具体索引需根据实际数据结构调整
    daily_values = daily_raw_data[0, 2, :]
```

### 4. 构建每日数据表
将提取出的数值、对应的日期以及股票代码（或其他标识）构建成一个结构化的 `DataFrame`。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_52.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_54.png)

```python
    # 构建当天的数据DataFrame
    # 假设 stock_codes 是一个包含所有股票代码的列表，与 daily_values 长度一致
    daily_df = pd.DataFrame({
        'date': current_date,  # 日期列
        'stock_code': stock_codes,  # 股票代码列
        'factor_value': daily_values  # 因子值列，列名可自定义
    })
```

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_56.png)

### 5. 拼接至总表
使用 `pandas.concat` 函数将每日的数据表 `daily_df` 拼接到总表 `total_data` 中。**关键点**：必须将拼接结果重新赋值给 `total_data`。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_58.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_60.png)

```python
    # 将当天的数据拼接到总表中，注意要重新赋值
    total_data = pd.concat([total_data, daily_df], ignore_index=True)
```

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_62.png)

**注意**：`ignore_index=True` 参数会忽略原有索引并生成新的连续索引，这通常是我们想要的结果。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_64.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_66.png)

---

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_68.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_70.png)

## 完整代码示例
将以上步骤整合，得到完整的代码逻辑。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_72.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_74.png)

```python
import pandas as pd

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_76.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_78.png)

# 1. 初始化总表
total_data = pd.DataFrame()

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_80.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_82.png)

# 2. 遍历日期序列 (假设 date_list 已定义)
for i in range(len(date_list)):
    current_date = date_list[i]

    # 3. 获取并处理每日数据
    daily_raw_data = get_factor_data(current_date)  # 假设的函数
    daily_values = daily_raw_data[0, 2, :]  # 提取数值

    # 4. 构建每日DataFrame (假设 stock_codes 已定义)
    daily_df = pd.DataFrame({
        'date': current_date,
        'stock_code': stock_codes,
        'factor_value': daily_values
    })

    # 5. 拼接至总表
    total_data = pd.concat([total_data, daily_df], ignore_index=True)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_84.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_86.png)

# 6. 查看结果
print(total_data.head())
```

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_88.png)

---

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_90.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_92.png)

## 注意事项与优化
*   **执行速度**：如果时间区间较长（例如一年），循环获取数据的过程可能会比较慢。这是正常现象。
*   **数据验证**：循环结束后，务必使用 `total_data.head()` 或 `total_data.tail()` 检查数据格式是否正确，是否包含了日期、股票代码和因子值这三列。
*   **错误处理**：在实际应用中，应考虑添加异常处理（如 `try...except`），以应对某一天数据获取失败的情况。
*   **内存考虑**：如果数据量极大，可以考虑分批处理或使用更高效的数据结构。

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_94.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_96.png)

---

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_98.png)

![](img/92ea03ffbafd131c35a0fa2f9780fbb1_100.png)

## 总结
本节课中我们一起学习了如何获取给定时间区间内的全部金融数据。我们通过一个 `for` 循环遍历每一个交易日，获取当日的因子数据，将其整理成包含日期、股票代码和数值的 `DataFrame`，并最终将所有日期的数据拼接成一个完整的总表。这个方法虽然直接，但却是处理时间序列面板数据的基础和关键步骤。掌握它，你就能为后续更复杂的分析和策略回测准备好规整的数据基础。