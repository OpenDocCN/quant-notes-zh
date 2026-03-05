# 人工智能数据挖掘实战：P43：获取给定区间全部数据 📊

在本节课中，我们将学习如何从给定的时间区间内，循环获取每一天的股票因子数据，并将这些数据整理成一个结构化的总表。这是金融量化分析中处理时间序列数据的基础操作。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_1.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_2.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_3.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间段内的所有数据。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_4.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_5.png)

## 核心思路与流程 🧠

![](img/cd6a9b8084abe0f97bb14664ea4582c5_7.png)

核心思路是：我们有一个日期序列，需要遍历其中的每一天，获取当天的因子数据，然后将每天的数据拼接成一个完整的 `DataFrame`。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_9.png)

以下是实现这一目标的具体步骤：

![](img/cd6a9b8084abe0f97bb14664ea4582c5_10.png)

1.  **遍历日期序列**：使用循环结构遍历给定的每一个交易日。
2.  **获取每日数据**：在循环中，调用数据获取函数，传入当前日期索引，得到当天的因子值。
3.  **处理每日数据**：每日返回的数据是一个三维数组，我们需要通过索引提取出有用的部分（通常是第三维），并将其转换为一维数据。
4.  **构建每日数据表**：将提取出的数据与对应的日期组合，构建一个包含“日期”和“因子值”两列的临时 `DataFrame`。
5.  **拼接总表**：在循环外部初始化一个空的汇总 `DataFrame`。在每次循环中，将构建好的每日数据表拼接到这个总表中。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_12.png)

## 代码实现详解 💻

### 1. 初始化总表

![](img/cd6a9b8084abe0f97bb14664ea4582c5_14.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_15.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_16.png)

首先，我们创建一个空的 `DataFrame` 作为最终汇总数据的总表。

```python
import pandas as pd
total_df = pd.DataFrame()  # 初始化一个空的总表
```

![](img/cd6a9b8084abe0f97bb14664ea4582c5_18.png)

### 2. 遍历日期并获取数据

![](img/cd6a9b8084abe0f97bb14664ea4582c5_20.png)

假设我们有一个日期列表 `date_list`，它包含了我们要获取数据的所有交易日。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_21.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_22.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_23.png)

以下是遍历日期并处理数据的核心代码：

![](img/cd6a9b8084abe0f97bb14664ea4582c5_25.png)

```python
# 假设 date_list 是包含所有目标日期的列表
for i in range(len(date_list)):
    # 获取第 i 天的因子数据，返回一个三维数组
    daily_data_3d = get_factor_data(i)  # 假设的获取数据函数

    # 提取有用的数据（通常位于第三维）
    useful_data = daily_data_3d[0, 0, :]

    # 构建当天的数据表
    daily_df = pd.DataFrame(useful_data, columns=['factor_value'])

    # 添加日期列
    daily_df['date'] = date_list[i]

    # 将当天的数据表拼接到总表中
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

**代码解释**：
*   `get_factor_data(i)`: 这是一个假设的函数，代表获取第 `i` 个索引对应日期的数据。实际应用中，你需要替换为真实的数据接口调用。
*   `daily_data_3d[0, 0, :]`: 根据之前对数据形状的分析，我们通过索引 `[0, 0, :]` 来获取所有股票在该因子上的值。
*   `pd.DataFrame(useful_data, columns=['factor_value'])`: 将一维数组转换为 `DataFrame`，并命名列名为 `‘factor_value’`。
*   `daily_df[‘date’] = date_list[i]`: 为这个每日数据表添加一列，记录对应的日期。
*   `pd.concat([total_df, daily_df], ignore_index=True)`: 使用 `concat` 函数将每日数据表 `daily_df` 拼接到总表 `total_df` 的末尾。`ignore_index=True` 参数确保拼接后的索引是连续的。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_27.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_28.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_29.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_30.png)

### 3. 检查最终结果

![](img/cd6a9b8084abe0f97bb14664ea4582c5_31.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_32.png)

循环结束后，我们可以查看总表的前几行，以确认数据是否按预期整合。

```python
print(total_df.head())
```

![](img/cd6a9b8084abe0f97bb14664ea4582c5_34.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_35.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_36.png)

预期的输出结构应包含 `date` 和 `factor_value` 两列，每一行代表一只股票在特定日期的因子值。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_37.png)

## 注意事项 ⚠️

![](img/cd6a9b8084abe0f97bb14664ea4582c5_39.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_40.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_41.png)

*   **执行速度**：如果获取数据的时间区间较长（例如一年），此循环过程可能会比较耗时。在实际应用中，这是正常现象。
*   **数据完整性**：确保循环中的每一步，特别是数据提取 (`daily_data_3d[0, 0, :]`) 和赋值 (`total_df = pd.concat(…)`) 都正确执行，避免出现总表为空的情况。
*   **列名自定义**：代码中的列名 `‘factor_value’` 和 `‘date’` 可以根据你的分析需求进行修改。

![](img/cd6a9b8084abe0f97bb14664ea4582c5_42.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_43.png)

![](img/cd6a9b8084abe0f97bb14664ea4582c5_44.png)

本节课中我们一起学习了如何通过循环遍历日期来获取连续时间区间内的全部股票因子数据，并利用 `pandas` 的 `DataFrame` 和 `concat` 功能将其整合为一张清晰的总表。这个方法虽然直接，但却是处理时间序列金融数据的基础且必要的技能。