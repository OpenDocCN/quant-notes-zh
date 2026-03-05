# 金融量化分析实战：P43：获取给定区间全部数据 📊

![](img/8fdd35b06b757986f2f1c622c782ee1a_0.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_2.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_4.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_6.png)

在本节课中，我们将学习如何通过循环遍历指定日期区间，获取每一天的股票因子数据，并将这些数据整理成一个结构化的DataFrame。这是构建量化分析数据集的关键一步。

![](img/8fdd35b06b757986f2f1c622c782ee1a_8.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_10.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间段内的全部数据。

![](img/8fdd35b06b757986f2f1c622c782ee1a_12.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_14.png)

## 核心思路与流程

![](img/8fdd35b06b757986f2f1c622c782ee1a_16.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_18.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_20.png)

获取多日数据的核心思路是使用循环。我们有一个日期序列，需要遍历其中的每一天，分别获取当天的因子值，最后将所有日期的数据汇总到一个总表中。

![](img/8fdd35b06b757986f2f1c622c782ee1a_22.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_24.png)

以下是实现此功能的具体步骤：

![](img/8fdd35b06b757986f2f1c622c782ee1a_26.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_28.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_30.png)

1.  **确定日期范围**：首先需要明确要获取数据的起止日期。
2.  **遍历每一天**：使用 `for` 循环遍历日期序列中的每一个日期。
3.  **获取单日数据**：在循环内部，调用获取单日数据的函数，传入当前日期索引，以得到当天的因子值。
4.  **处理与提取数据**：从返回的三维数据结构中，提取出我们需要的实际数值部分。
5.  **构建单日数据表**：将提取出的数值与对应的日期、股票代码等信息组合，构建成一个临时的DataFrame。
6.  **汇总到总表**：在每次循环中，将临时DataFrame拼接到一个初始为空的“总”DataFrame中。

![](img/8fdd35b06b757986f2f1c622c782ee1a_32.png)

## 代码实现详解

![](img/8fdd35b06b757986f2f1c622c782ee1a_34.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_36.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_38.png)

接下来，我们通过代码来具体实现上述流程。

![](img/8fdd35b06b757986f2f1c622c782ee1a_40.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_42.png)

首先，我们需要知道日期序列的长度，这决定了循环的次数。

![](img/8fdd35b06b757986f2f1c622c782ee1a_44.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_46.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_48.png)

```python
# 假设 dates 是日期序列
total_days = len(dates)
```

![](img/8fdd35b06b757986f2f1c622c782ee1a_50.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_52.png)

然后，我们初始化一个空的DataFrame作为总表，用于累积所有日期的数据。

![](img/8fdd35b06b757986f2f1c622c782ee1a_54.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_56.png)

```python
import pandas as pd
total_data = pd.DataFrame()
```

![](img/8fdd35b06b757986f2f1c622c782ee1a_58.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_60.png)

现在，开始遍历每一天。在循环中，我们需要完成获取数据、构建单日DataFrame和拼接三个主要操作。

![](img/8fdd35b06b757986f2f1c622c782ee1a_62.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_64.png)

以下是循环体内的关键代码：

![](img/8fdd35b06b757986f2f1c622c782ee1a_66.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_68.png)

```python
for i in range(total_days):
    # 1. 获取第i天的因子数据，返回一个三维数组
    daily_raw_data = get_factor_data(i)  # 假设的获取数据函数

    # 2. 从三维数组中提取有用的数值部分（假设在第三个维度）
    # 这里索引 [0, 2, :] 是一个示例，具体取决于数据结构
    daily_values = daily_raw_data[0, 2, :]

    # 3. 构建单日DataFrame
    # 首先，用因子值创建一列，并指定列名
    daily_df = pd.DataFrame(daily_values, columns=['factor_value'])

    # 然后，添加日期列。假设 dates[i] 是第i天的日期
    daily_df['date'] = dates[i]

    # 还可以添加股票代码列（假设 stock_codes 是股票代码列表）
    # daily_df['stock_code'] = stock_codes

    # 4. 将单日DataFrame拼接到总表中
    # 注意：需要将拼接结果重新赋值给 total_data
    total_data = pd.concat([total_data, daily_df], ignore_index=True)
```

![](img/8fdd35b06b757986f2f1c622c782ee1a_70.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_72.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_74.png)

## 注意事项与优化

![](img/8fdd35b06b757986f2f1c622c782ee1a_76.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_78.png)

在实现过程中，有几点需要特别注意：

![](img/8fdd35b06b757986f2f1c622c782ee1a_80.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_82.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_84.png)

*   **重新赋值**：使用 `pd.concat` 进行拼接后，必须将结果赋值回原变量（如 `total_data = pd.concat(...)`），否则总表不会更新。
*   **执行效率**：如果获取的日期区间较长（例如一年），循环遍历可能会比较耗时。这是正常现象，因为需要逐日请求和计算数据。
*   **数据结构**：代码中 `daily_raw_data[0, 2, :]` 的索引方式是基于对返回数据结构（可能是numpy数组）的理解。在实际应用中，你需要根据 `get_factor_data` 函数返回的具体数据结构来调整索引，以正确提取数值。

![](img/8fdd35b06b757986f2f1c622c782ee1a_86.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_88.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_90.png)

循环执行完毕后，我们可以查看总表的前几行，以确认数据是否按预期整理完成。

![](img/8fdd35b06b757986f2f1c622c782ee1a_92.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_94.png)

```python
print(total_data.head())
```

![](img/8fdd35b06b757986f2f1c622c782ee1a_96.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_98.png)

输出应包含 `date`（日期）、`factor_value`（因子值）等列，每一行代表一只股票在某个特定日期的因子数据。

![](img/8fdd35b06b757986f2f1c622c782ee1a_100.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_102.png)

![](img/8fdd35b06b757986f2f1c622c782ee1a_104.png)

本节课中我们一起学习了如何通过循环遍历获取一个连续时间区间内的全部股票因子数据，并将其整理为结构化的DataFrame。我们掌握了从确定日期范围、遍历每一天、提取和处理数据，到最终拼接成总表的完整流程。这个过程是构建量化分析模型数据基础的核心步骤，虽然代码逻辑简单，但却是处理时间序列金融数据不可或缺的一环。