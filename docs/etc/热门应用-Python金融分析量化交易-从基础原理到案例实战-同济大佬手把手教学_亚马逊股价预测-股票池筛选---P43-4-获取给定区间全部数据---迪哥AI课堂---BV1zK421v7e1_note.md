# Python金融分析量化交易：P43：4-获取给定区间全部数据 📊

![](img/7b4eaf66f0f71d6bb930a300253b21b0_0.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_2.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_4.png)

在本节课中，我们将学习如何从数据集中获取一个给定时间区间内的所有数据。我们将通过编写一个循环，遍历指定日期范围内的每一天，提取对应的数据，并将这些数据整理成一个结构化的DataFrame，以便后续分析。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_6.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_8.png)

---

![](img/7b4eaf66f0f71d6bb930a300253b21b0_10.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_12.png)

上一节我们介绍了如何获取单日数据，本节中我们来看看如何获取一个连续时间区间内的所有数据。核心思路是遍历日期序列中的每一天，重复执行单日数据获取的操作。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_14.png)

## 遍历日期序列

![](img/7b4eaf66f0f71d6bb930a300253b21b0_16.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_18.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_20.png)

首先，我们需要遍历给定的日期序列。假设我们有一个日期序列，代表需要查询的每一天。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_22.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_24.png)

以下是遍历日期序列的代码实现：

![](img/7b4eaf66f0f71d6bb930a300253b21b0_26.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_28.png)

```python
for i in range(len(date_sequence)):
    current_date = date_sequence[i]
    # 在此处获取当前日期的数据
```

![](img/7b4eaf66f0f71d6bb930a300253b21b0_30.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_32.png)

## 获取每日数据

![](img/7b4eaf66f0f71d6bb930a300253b21b0_34.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_36.png)

在循环内部，对于每一天，我们需要调用数据获取函数。这个函数通常会返回一个多维数组（例如三维数组），其中包含了我们需要的指标数据。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_38.png)

以下是获取每日数据并提取所需指标的代码：

![](img/7b4eaf66f0f71d6bb930a300253b21b0_40.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_42.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_44.png)

```python
# 假设 get_data 函数返回一个三维数组
daily_data = get_data(i)  # 传入索引 i 来获取第 i 天的数据
# 提取有用的部分，通常位于第三个维度
useful_data = daily_data.loc[0, 0, :]
```

![](img/7b4eaf66f0f71d6bb930a300253b21b0_46.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_48.png)

## 构建汇总DataFrame

![](img/7b4eaf66f0f71d6bb930a300253b21b0_50.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_52.png)

获取到每日数据后，我们不能仅仅打印它们，而是需要将它们汇总到一个结构化的表格中。我们希望最终的DataFrame包含三列：日期、股票名称和指标值。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_54.png)

以下是构建汇总DataFrame的步骤：

![](img/7b4eaf66f0f71d6bb930a300253b21b0_56.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_58.png)

1.  首先，创建一个空的DataFrame作为总表。
2.  在循环中，为每一天的数据创建一个临时的DataFrame。
3.  将这个临时DataFrame拼接到总表中。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_60.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_62.png)

以下是实现代码：

![](img/7b4eaf66f0f71d6bb930a300253b21b0_64.png)

```python
import pandas as pd

![](img/7b4eaf66f0f71d6bb930a300253b21b0_66.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_68.png)

# 1. 创建一个空的DataFrame作为总表
total_df = pd.DataFrame()

![](img/7b4eaf66f0f71d6bb930a300253b21b0_70.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_72.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_74.png)

for i in range(len(date_sequence)):
    current_date = date_sequence[i]
    
    # 2. 获取当前日期的数据
    daily_data = get_data(i)
    value_data = daily_data.loc[0, 0, :]
    
    # 3. 为当前日期构建一个临时DataFrame
    # 这个DataFrame有两列：日期和指标值
    temp_df = pd.DataFrame({
        'date': [current_date],
        'factor_value': [value_data]  # 假设我们只关心一个名为‘factor_value’的指标
    })
    
    # 4. 将临时DataFrame拼接到总表中
    # 注意：需要使用 ignore_index=True 来重置索引
    total_df = pd.concat([total_df, temp_df], ignore_index=True)
```

![](img/7b4eaf66f0f71d6bb930a300253b21b0_76.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_78.png)

## 检查与优化

![](img/7b4eaf66f0f71d6bb930a300253b21b0_80.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_82.png)

代码执行后，需要检查生成的`total_df`是否符合预期。此外，如果时间区间较长，循环获取数据可能会比较慢，这是正常现象。在实际应用中，可以根据需要调整时间区间的长度以平衡速度与数据量。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_84.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_86.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_88.png)

执行以下代码查看结果的前几行：

![](img/7b4eaf66f0f71d6bb930a300253b21b0_90.png)

```python
print(total_df.head())
```

![](img/7b4eaf66f0f71d6bb930a300253b21b0_92.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_94.png)

如果输出结果中包含了日期和对应的指标值，说明数据获取与汇总成功。

![](img/7b4eaf66f0f71d6bb930a300253b21b0_96.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_98.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_100.png)

---

![](img/7b4eaf66f0f71d6bb930a300253b21b0_102.png)

![](img/7b4eaf66f0f71d6bb930a300253b21b0_104.png)

本节课中我们一起学习了如何获取给定时间区间内的全部金融数据。我们通过遍历日期序列，循环获取每一天的数据，并将这些数据整理到一个包含日期和指标值的Pandas DataFrame中。这个过程是构建量化分析数据集的基础步骤，虽然代码逻辑简单，但对于处理时间序列数据至关重要。在接下来的课程中，我们将利用这些整理好的数据进行更深入的分析。