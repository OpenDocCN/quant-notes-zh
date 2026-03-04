# Python金融分析与量化交易实战：P39：获取给定区间全部数据 📊

![](img/37ae32ea310b95b7196b6e1e6956cf8c_0.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_2.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_4.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_6.png)

在本节课中，我们将学习如何从给定的时间区间内，循环获取每一天的股票因子数据，并将这些数据整理成一个结构化的总表。这是处理时间序列金融数据的关键步骤。

![](img/37ae32ea310b95b7196b6e1e6956cf8c_8.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_10.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何扩展这一过程，以获取一个完整时间区间内的所有数据。

![](img/37ae32ea310b95b7196b6e1e6956cf8c_12.png)

## 核心思路与步骤

核心思路是遍历指定日期区间内的每一天，对每一天都执行一次数据获取操作，然后将每天获取的数据拼接成一个完整的 `DataFrame`。

![](img/37ae32ea310b95b7196b6e1e6956cf8c_14.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_16.png)

以下是实现这一过程的具体步骤：

![](img/37ae32ea310b95b7196b6e1e6956cf8c_18.png)

1.  **确定日期序列长度**：首先，需要知道我们要处理的总天数，这决定了循环的次数。
2.  **循环遍历每一天**：使用 `for` 循环，遍历日期序列中的每一个日期。
3.  **获取每日数据**：在循环体内，针对当前日期 `i`，调用数据获取函数，获取该日所有股票的因子值。
4.  **提取有效数据**：从返回的三维数据结构中，提取我们需要的实际数值部分。
5.  **构建每日数据表**：将提取出的数据（因子值）与对应的日期、股票代码等信息，组合成一个小的 `DataFrame`。
6.  **拼接成总表**：在每次循环中，将新生成的每日数据 `DataFrame` 拼接到一个初始为空的“总表” `DataFrame` 中。

![](img/37ae32ea310b95b7196b6e1e6956cf8c_20.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_22.png)

## 代码实现详解

![](img/37ae32ea310b95b7196b6e1e6956cf8c_24.png)

以下是实现上述逻辑的核心代码片段：

![](img/37ae32ea310b95b7196b6e1e6956cf8c_26.png)

```python
import pandas as pd

![](img/37ae32ea310b95b7196b6e1e6956cf8c_28.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_30.png)

# 假设 dates 是包含所有目标日期的序列，例如 ['2019-01-01', '2019-01-02', ...]
# 假设 get_factor_data(day_index) 是获取第 day_index 天数据的函数

![](img/37ae32ea310b95b7196b6e1e6956cf8c_32.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_34.png)

# 步骤1 & 2: 根据日期序列长度进行循环
total_days = len(dates)
total_dataframe = pd.DataFrame()  # 初始化一个空的总表

for i in range(total_days):
    # 步骤3 & 4: 获取并提取第 i 天的有效数据
    # 注意：get_factor_data(i) 返回一个三维数据，我们通常需要第三个维度的值
    daily_raw_data = get_factor_data(i)
    daily_values = daily_raw_data.iloc[0, 2, :]  # 提取实际因子值，索引方式根据实际数据结构调整

    # 步骤5: 构建每日数据的小 DataFrame
    # 假设 daily_values 的索引是股票代码
    daily_df = pd.DataFrame({
        'date': dates[i],  # 日期列
        'stock_code': daily_values.index,  # 股票代码列
        'factor_value': daily_values.values  # 因子值列
    })

    # 步骤6: 将每日数据拼接到总表
    total_dataframe = pd.concat([total_dataframe, daily_df], ignore_index=True)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_36.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_38.png)

# 最后，可以查看总表的前几行以确认结果
print(total_dataframe.head())
```

![](img/37ae32ea310b95b7196b6e1e6956cf8c_40.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_42.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_44.png)

## 关键注意事项

![](img/37ae32ea310b95b7196b6e1e6956cf8c_46.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_48.png)

在实现过程中，有几个关键点需要注意：

![](img/37ae32ea310b95b7196b6e1e6956cf8c_50.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_52.png)

*   **数据提取的索引**：函数 `get_factor_data(i)` 返回的数据结构是三维的。代码中 `daily_raw_data.iloc[0, 2, :]` 是一个示例，表示提取第一个维度的第0个元素、第二个维度的第2个元素下的所有第三个维度的数据（即所有股票的因子值）。**你需要根据实际API返回的数据结构来调整这里的索引**。
*   **拼接操作与赋值**：使用 `pd.concat` 进行拼接后，**必须将结果重新赋值给总表变量**（如 `total_dataframe = pd.concat(...)`），否则拼接操作不会生效。
*   **性能考虑**：如果时间区间很长（例如一年或更久），循环获取每一天的数据可能会比较耗时。在实际应用中，这是正常现象。对于练习，可以适当缩短时间区间以加快速度。
*   **列名定义**：在创建每日 `DataFrame` 时，定义的列名（如 `‘date’`, `‘stock_code’`, `‘factor_value’`）将作为总表的列名，请根据你的分析需求进行命名。

![](img/37ae32ea310b95b7196b6e1e6956cf8c_54.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_56.png)

## 结果验证

![](img/37ae32ea310b95b7196b6e1e6956cf8c_58.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_60.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_62.png)

执行完代码后，通过 `print(total_dataframe.head())` 查看总表的前几行。一个理想的结果应该包含三列：日期、股票代码和对应的因子数值。这证明我们成功地将区间内分散的每日数据整合到了一个结构化的表格中。

![](img/37ae32ea310b95b7196b6e1e6956cf8c_64.png)

![](img/37ae32ea310b95b7196b6e1e6956cf8c_66.png)

本节课中我们一起学习了如何通过循环遍历来获取一个时间区间内的全部金融数据，并利用 `pandas` 的 `DataFrame` 和 `concat` 函数将其整理为一张总表。这个过程是量化分析中进行数据准备的基础，虽然代码稍显繁琐，但逻辑清晰，是处理时间序列数据的标准方法之一。