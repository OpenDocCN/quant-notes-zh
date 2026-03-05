# 人生苦短，我学量化！：P38：获取给定区间全部数据

![](img/55c0dc6837a1b5483a559b24b4b919ae_0.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_2.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_4.png)

## 概述
在本节课中，我们将学习如何从时间序列数据中，获取一个指定日期区间内的所有数据。我们将通过编写一个循环，逐日获取数据，并将结果整理成一个结构化的DataFrame，以便后续分析。

![](img/55c0dc6837a1b5483a559b24b4b919ae_6.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_8.png)

---

![](img/55c0dc6837a1b5483a559b24b4b919ae_10.png)

上一节我们介绍了如何获取单日数据。本节中，我们来看看如何获取一个连续时间段内的所有数据。

![](img/55c0dc6837a1b5483a559b24b4b919ae_12.png)

我们的数据是一个日期序列。要获取其中每一天的数据，最直接的方法是使用一个`for`循环来遍历每一天。

![](img/55c0dc6837a1b5483a559b24b4b919ae_14.png)

以下是实现这一过程的核心步骤：

![](img/55c0dc6837a1b5483a559b24b4b919ae_16.png)

1.  **确定日期范围**：首先，我们需要知道数据序列的总长度，即总共有多少天。
2.  **遍历每一天**：使用`for`循环遍历序列中的每一天。
3.  **获取每日数据**：在循环内部，获取当前遍历到的这一天的具体数据值。
4.  **构建每日DataFrame**：将获取到的每日数据（日期和指标值）整理成一个小的DataFrame。
5.  **汇总数据**：在循环外部创建一个总的DataFrame，并将每次循环生成的每日DataFrame拼接进去，最终形成一个包含所有日期数据的完整表格。

![](img/55c0dc6837a1b5483a559b24b4b919ae_18.png)

---

![](img/55c0dc6837a1b5483a559b24b4b919ae_20.png)

### 步骤详解与代码实现

![](img/55c0dc6837a1b5483a559b24b4b919ae_22.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_24.png)

首先，我们导入必要的库并准备数据。

![](img/55c0dc6837a1b5483a559b24b4b919ae_26.png)

```python
import pandas as pd
# 假设我们已经有了一个名为 `date_series` 的日期序列
# 以及一个用于获取某日数据的函数 `get_daily_data(date)`
```

接下来，我们开始实现上述步骤。

![](img/55c0dc6837a1b5483a559b24b4b919ae_28.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_30.png)

**第一步：确定循环次数**
我们需要知道日期序列的长度，以确定`for`循环需要执行多少次。

![](img/55c0dc6837a1b5483a559b24b4b919ae_32.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_34.png)

```python
total_days = len(date_series)
```

![](img/55c0dc6837a1b5483a559b24b4b919ae_36.png)

**第二步：创建总表**
在循环开始前，我们先创建一个空的DataFrame，用于存放最终汇总的所有数据。

![](img/55c0dc6837a1b5483a559b24b4b919ae_38.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_40.png)

```python
total_df = pd.DataFrame()
```

![](img/55c0dc6837a1b5483a559b24b4b919ae_42.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_44.png)

**第三步：遍历与获取数据**
现在，我们开始遍历每一天。对于每一天，我们都需要获取其数据并整理。

![](img/55c0dc6837a1b5483a559b24b4b919ae_46.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_48.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_50.png)

```python
for i in range(total_days):
    # 1. 获取当前日期
    current_date = date_series[i]
    
    # 2. 获取当前日期的数据值
    # 假设 get_daily_data 返回一个三维数据，我们只需要第三个维度的值
    daily_value = get_daily_data(current_date).iloc[0, 0, :]
    
    # 3. 构建每日数据DataFrame
    # 这个DataFrame包含两列：日期和指标值
    daily_df = pd.DataFrame({
        'date': [current_date],
        'factor_value': [daily_value]
    })
    
    # 4. 将每日DataFrame拼接到总表中
    # 注意：需要使用赋值操作来更新总表
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

![](img/55c0dc6837a1b5483a559b24b4b919ae_52.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_54.png)

**代码说明**：
*   `get_daily_data(current_date).iloc[0, 0, :]`：这行代码模拟了从原始数据结构中提取我们关心的具体数值。`iloc`是基于位置的索引，`[0, 0, :]`表示取第一个维度的第0个位置、第二个维度的第0个位置，以及第三个维度的全部数据。
*   `pd.DataFrame({‘date’: …, ‘factor_value’: …})`：这里我们创建了一个只有一行数据的DataFrame，列名分别为`date`（日期）和`factor_value`（指标值）。
*   `pd.concat([total_df, daily_df], ignore_index=True)`：这是拼接DataFrame的关键操作。`ignore_index=True`参数会忽略原有索引并生成新的连续索引，保证总表的整洁。

![](img/55c0dc6837a1b5483a559b24b4b919ae_56.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_58.png)

**第四步：查看结果**
循环执行完毕后，我们可以查看最终生成的总表。

![](img/55c0dc6837a1b5483a559b24b4b919ae_60.png)

```python
print(total_df.head())
```

![](img/55c0dc6837a1b5483a559b24b4b919ae_62.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_64.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_66.png)

执行这段代码可能需要一些时间，特别是当日期区间较长时（例如一年）。最终，`total_df`将是一个包含多行数据的DataFrame，每一行对应一个日期，并包含该日期的股票指标值。

![](img/55c0dc6837a1b5483a559b24b4b919ae_68.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_70.png)

---

![](img/55c0dc6837a1b5483a559b24b4b919ae_72.png)

![](img/55c0dc6837a1b5483a559b24b4b919ae_74.png)

### 总结
本节课中，我们一起学习了如何获取给定时间区间内的全部金融时间序列数据。我们通过编写`for`循环遍历每一天，获取每日数据并构建DataFrame，最后使用`pd.concat`函数将所有日期的数据汇总到一个总表中。这个过程虽然需要手动编写循环，但它是处理和分析跨日期数据的基础且必要的方法。