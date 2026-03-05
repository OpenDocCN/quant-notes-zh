# Python金融分析与量化交易实战教程：P41：04-4-获取给定区间全部数据 📊

![](img/ffb9063fa0f8263cf116ec725aca7398_1.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_3.png)

在本节课程中，我们将学习如何从给定的时间区间内，逐日获取并整合所有股票因子数据。我们将通过编写一个循环，遍历指定日期范围内的每一天，提取当天的因子值，并将所有日期的数据汇总到一个统一的`DataFrame`中。

![](img/ffb9063fa0f8263cf116ec725aca7398_5.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_7.png)

---

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间区间内的全部数据。

![](img/ffb9063fa0f8263cf116ec725aca7398_9.png)

核心思路是遍历日期序列中的每一天。既然我们有一个日期序列，要获取其中每一天的数据，最直接的方法就是使用一个`for`循环。我们可以通过`range`函数来遍历这个序列的长度，从而访问每一天。

![](img/ffb9063fa0f8263cf116ec725aca7398_11.png)

以下是实现这一过程的关键步骤：

![](img/ffb9063fa0f8263cf116ec725aca7398_13.png)

1.  **确定日期范围**：首先，我们需要知道日期序列的总长度，这决定了我们需要循环多少次。
2.  **遍历每一天**：使用`for`循环，遍历日期序列中的每一个索引位置。
3.  **获取每日数据**：在循环内部，对于每一天（索引`i`），调用获取因子值的函数，并传入索引`i`。该函数返回一个三维数组，我们只需提取其中包含实际数值的部分（通常是第三个维度的全部数据）。
4.  **构建每日数据表**：将提取出的每日数据（一个数值数组）转换成一个`DataFrame`。同时，我们需要为这个`DataFrame`添加两列信息：一列是当前日期，另一列是因子值。我们可以为因子值列指定一个名称，例如“factor_value”。
5.  **汇总到总表**：初始化一个空的`DataFrame`作为总表。在每次循环中，将新构建的每日数据`DataFrame`拼接到这个总表中。

![](img/ffb9063fa0f8263cf116ec725aca7398_15.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_17.png)

以下是实现上述逻辑的代码框架：

```python
import pandas as pd

![](img/ffb9063fa0f8263cf116ec725aca7398_19.png)

# 假设 dates 是日期序列，get_factor_data(i) 是获取第i天因子数据的函数
# 初始化一个空的DataFrame作为总表
total_df = pd.DataFrame()

![](img/ffb9063fa0f8263cf116ec725aca7398_21.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_23.png)

# 遍历日期序列的每一天
for i in range(len(dates)):
    # 1. 获取第i天的因子数据（三维数组）
    daily_data_3d = get_factor_data(i)
    
    # 2. 提取有用的数值部分（假设在第三个维度的全部数据）
    values = daily_data_3d[0, 0, :]  # 根据实际数据结构调整索引
    
    # 3. 构建每日的DataFrame
    daily_df = pd.DataFrame(values, columns=['factor_value'])
    
    # 4. 添加日期列
    daily_df['date'] = dates[i]
    
    # 5. 将每日DataFrame拼接到总表
    total_df = pd.concat([total_df, daily_df], ignore_index=True)

# 打印总表的前几行进行检查
print(total_df.head())
```

![](img/ffb9063fa0f8263cf116ec725aca7398_25.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_27.png)

**代码说明**：
*   `for i in range(len(dates)):` 循环遍历日期序列。
*   `daily_data_3d[0, 0, :]`：这是一个示例，表示从三维数组中提取所有股票在特定因子上的数值。你需要根据`get_factor_data`函数返回的实际数据结构来调整索引。
*   `pd.DataFrame(values, columns=['factor_value'])`：用提取出的数值数组创建`DataFrame`，并命名列。
*   `daily_df['date'] = dates[i]`：为每日`DataFrame`添加日期列。
*   `pd.concat([total_df, daily_df], ignore_index=True)`：将每日数据`DataFrame`拼接到总`DataFrame`中。`ignore_index=True`参数确保拼接后生成新的连续索引。

需要注意的是，如果指定的时间区间较长（例如一年），这个循环过程可能会运行得比较慢，因为需要逐日请求和整合数据。这是获取批量历史数据时常见的情况。

![](img/ffb9063fa0f8263cf116ec725aca7398_29.png)

执行完上述代码后，`total_df`中将包含三列关键信息：`date`（日期）、`factor_value`（因子值），以及从原始数据中带来的股票代码信息。这样，我们就完成了将给定时间区间内所有因子数据提取并整合到一张总表中的目标。

![](img/ffb9063fa0f8263cf116ec725aca7398_31.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_33.png)

---

![](img/ffb9063fa0f8263cf116ec725aca7398_35.png)

![](img/ffb9063fa0f8263cf116ec725aca7398_37.png)

本节课中我们一起学习了如何通过循环遍历来获取一个连续时间区间内的全部股票因子数据。我们掌握了将每日数据提取、封装并最终汇总到一个统一`DataFrame`中的完整流程。这个过程虽然需要手动编写循环，但它是处理时间序列批量数据的基础且必要的方法。