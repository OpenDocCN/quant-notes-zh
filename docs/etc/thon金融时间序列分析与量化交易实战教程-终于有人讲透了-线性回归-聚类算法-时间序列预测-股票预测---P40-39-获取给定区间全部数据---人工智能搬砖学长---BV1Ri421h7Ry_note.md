# Python金融时间序列分析与量化交易实战教程：P40：39.获取给定区间全部数据 📊

![](img/8336452040d4b4c95781bced4037d41f_0.png)

![](img/8336452040d4b4c95781bced4037d41f_2.png)

![](img/8336452040d4b4c95781bced4037d41f_4.png)

![](img/8336452040d4b4c95781bced4037d41f_6.png)

![](img/8336452040d4b4c95781bced4037d41f_8.png)

在本节课中，我们将学习如何从金融数据接口中，获取一个指定时间区间内的全部数据。我们将通过编写一个循环，逐日获取数据，并将这些数据整理成一个结构化的DataFrame，以便进行后续分析。

![](img/8336452040d4b4c95781bced4037d41f_10.png)

![](img/8336452040d4b4c95781bced4037d41f_12.png)

![](img/8336452040d4b4c95781bced4037d41f_14.png)

上一节我们介绍了如何获取单日数据，本节中我们来看看如何获取一个连续时间段内的所有数据。

![](img/8336452040d4b4c95781bced4037d41f_16.png)

![](img/8336452040d4b4c95781bced4037d41f_18.png)

![](img/8336452040d4b4c95781bced4037d41f_20.png)

## 核心思路与步骤

![](img/8336452040d4b4c95781bced4037d41f_22.png)

![](img/8336452040d4b4c95781bced4037d41f_24.png)

![](img/8336452040d4b4c95781bced4037d41f_26.png)

获取区间数据的核心思路是遍历指定日期范围内的每一天，分别获取当天的数据，然后将这些每日数据汇总到一个总表中。

![](img/8336452040d4b4c95781bced4037d41f_28.png)

![](img/8336452040d4b4c95781bced4037d41f_30.png)

![](img/8336452040d4b4c95781bced4037d41f_32.png)

以下是实现这一目标的具体步骤：

![](img/8336452040d4b4c95781bced4037d41f_34.png)

![](img/8336452040d4b4c95781bced4037d41f_36.png)

![](img/8336452040d4b4c95781bced4037d41f_38.png)

1.  **确定日期范围**：首先，需要明确要获取数据的起始日期和结束日期。
2.  **遍历每一天**：使用循环结构（如 `for` 循环）遍历日期范围内的每一天。
3.  **获取单日数据**：在循环内部，调用获取单日数据的函数或方法，传入当前循环到的日期。
4.  **处理与提取数据**：对获取到的单日数据进行处理，提取出我们关心的指标值（例如股票价格、因子值等）。
5.  **构建每日数据表**：将提取出的数据（如日期、股票代码、指标值）组织成一个小的DataFrame。
6.  **汇总到总表**：在每次循环中，将构建好的每日小DataFrame拼接到一个初始为空的“总”DataFrame中。
7.  **检查与验证**：循环结束后，检查总DataFrame的内容，确保数据完整、准确。

![](img/8336452040d4b4c95781bced4037d41f_40.png)

![](img/8336452040d4b4c95781bced4037d41f_42.png)

![](img/8336452040d4b4c95781bced4037d41f_44.png)

## 代码实现详解

![](img/8336452040d4b4c95781bced4037d41f_46.png)

![](img/8336452040d4b4c95781bced4037d41f_48.png)

![](img/8336452040d4b4c95781bced4037d41f_50.png)

下面，我们通过代码来具体实现上述步骤。假设我们已经有一个可以获取单日数据的函数 `get_single_day_data(date)`。

![](img/8336452040d4b4c95781bced4037d41f_52.png)

![](img/8336452040d4b4c95781bced4037d41f_54.png)

![](img/8336452040d4b4c95781bced4037d41f_56.png)

```python
import pandas as pd

![](img/8336452040d4b4c95781bced4037d41f_58.png)

![](img/8336452040d4b4c95781bced4037d41f_60.png)

![](img/8336452040d4b4c95781bced4037d41f_62.png)

![](img/8336452040d4b4c95781bced4037d41f_64.png)

# 假设的起始和结束日期
start_date = '2019-01-01'
end_date = '2020-01-01'

![](img/8336452040d4b4c95781bced4037d41f_66.png)

![](img/8336452040d4b4c95781bced4037d41f_68.png)

![](img/8336452040d4b4c95781bced4037d41f_70.png)

# 生成日期序列
date_range = pd.date_range(start=start_date, end=end_date)

![](img/8336452040d4b4c95781bced4037d41f_72.png)

![](img/8336452040d4b4c95781bced4037d41f_74.png)

![](img/8336452040d4b4c95781bced4037d41f_76.png)

![](img/8336452040d4b4c95781bced4037d41f_78.png)

# 初始化一个空的DataFrame作为总表
total_data = pd.DataFrame()

![](img/8336452040d4b4c95781bced4037d41f_80.png)

![](img/8336452040d4b4c95781bced4037d41f_82.png)

![](img/8336452040d4b4c95781bced4037d41f_84.png)

# 遍历日期序列中的每一天
for single_date in date_range:
    # 步骤3：获取单日数据（这里用假设函数代替）
    # daily_raw_data = get_single_day_data(single_date)
    # 假设 daily_raw_data 是一个包含所需信息的对象或字典

    # 步骤4：处理与提取数据（示例：提取名为‘factor_value’的指标）
    # 这里需要根据实际数据结构进行调整
    # value = daily_raw_data.loc[0, 0, :]  # 示例索引，实际可能不同
    # 假设我们提取到了一个数值列表或Series，取第一个值作为示例
    # factor_value = value.iloc[0] if hasattr(value, 'iloc') else value[0]

    # 为了演示，我们这里用一个模拟值
    factor_value = 100.5  # 模拟的因子值

    # 步骤5：构建每日数据表
    daily_df = pd.DataFrame({
        'date': [single_date],           # 日期列
        'stock_code': ['000001.SZ'],     # 股票代码列（示例）
        'factor_value': [factor_value]   # 指标值列
    })

    # 步骤6：汇总到总表
    # 使用 pd.concat 进行纵向拼接
    total_data = pd.concat([total_data, daily_df], ignore_index=True)

![](img/8336452040d4b4c95781bced4037d41f_86.png)

![](img/8336452040d4b4c95781bced4037d41f_88.png)

![](img/8336452040d4b4c95781bced4037d41f_90.png)

![](img/8336452040d4b4c95781bced4037d41f_92.png)

# 步骤7：检查结果
print("数据获取完成，总表前5行如下：")
print(total_data.head())
```

![](img/8336452040d4b4c95781bced4037d41f_94.png)

![](img/8336452040d4b4c95781bced4037d41f_96.png)

![](img/8336452040d4b4c95781bced4037d41f_98.png)

**代码关键点说明**：
*   `pd.date_range`: 用于生成一个连续的日期序列。
*   `pd.DataFrame()`: 初始化一个空的DataFrame。
*   `pd.concat()`: 用于将多个DataFrame沿指定轴（默认是行方向，即axis=0）进行拼接。`ignore_index=True` 参数会忽略原有索引并生成新的连续索引。
*   在实际应用中，你需要将 `get_single_day_data` 函数和数据处理部分替换为真实的API调用和数据处理逻辑。

![](img/8336452040d4b4c95781bced4037d41f_100.png)

![](img/8336452040d4b4c95781bced4037d41f_102.png)

![](img/8336452040d4b4c95781bced4037d41f_104.png)

![](img/8336452040d4b4c95781bced4037d41f_106.png)

## 注意事项

![](img/8336452040d4b4c95781bced4037d41f_108.png)

![](img/8336452040d4b4c95781bced4037d41f_110.png)

![](img/8336452040d4b4c95781bced4037d41f_112.png)

1.  **执行效率**：如果获取的日期范围很长（例如数年），此方法可能需要较长时间运行，因为涉及大量的网络请求或数据查询。在实际项目中，可能需要考虑优化，如使用异步请求或检查API是否支持批量获取。
2.  **数据完整性**：循环中需要妥善处理可能出现的异常（如某天数据缺失、网络错误等），通常需要添加 `try...except` 语句来保证程序的健壮性。
3.  **数据结构**：示例中的数据处理部分 (`daily_raw_data.loc[0, 0, :]`) 是高度简化的。你必须根据实际接口返回的数据结构来编写正确的数据提取代码。

![](img/8336452040d4b4c95781bced4037d41f_114.png)

![](img/8336452040d4b4c95781bced4037d41f_116.png)

![](img/8336452040d4b4c95781bced4037d41f_118.png)

![](img/8336452040d4b4c95781bced4037d41f_120.png)

本节课中我们一起学习了如何通过循环遍历来获取一个时间区间内的全部金融数据，并将其整理成规整的DataFrame格式。这是进行时间序列分析和构建量化策略非常基础且重要的一步，为后续的数据清洗、特征工程和模型训练做好了准备。