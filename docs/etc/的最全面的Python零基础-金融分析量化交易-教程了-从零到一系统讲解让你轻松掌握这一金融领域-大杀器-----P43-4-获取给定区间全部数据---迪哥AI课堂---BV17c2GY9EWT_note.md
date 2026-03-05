# Python金融分析量化交易：P43：4-获取给定区间全部数据 📊

![](img/9f286ad266d965f7ec44fdac59d5dd40_0.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_2.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_4.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_6.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_8.png)

在本节课中，我们将学习如何从给定的时间区间内，循环获取每一天的金融因子数据，并将这些数据整理成一个结构化的总表。这是构建量化分析数据集的关键一步。

![](img/9f286ad266d965f7ec44fdac59d5dd40_10.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_12.png)

上一节我们介绍了如何获取单日的因子数据，本节中我们来看看如何获取一个连续时间段内的全部数据。

![](img/9f286ad266d965f7ec44fdac59d5dd40_14.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_16.png)

## 遍历日期序列

![](img/9f286ad266d965f7ec44fdac59d5dd40_18.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_20.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_22.png)

我们的日期是一个序列，目标是查询其中的每一天。实现方法是使用一个 `for` 循环来遍历这个日期序列。

![](img/9f286ad266d965f7ec44fdac59d5dd40_24.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_26.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_28.png)

以下是遍历日期序列并获取每日数据的核心步骤：

![](img/9f286ad266d965f7ec44fdac59d5dd40_30.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_32.png)

1.  **确定循环范围**：首先，需要知道日期序列的总长度，即总天数。
2.  **循环获取数据**：使用 `for i in range(总天数)` 循环，在每次迭代中获取第 `i` 天的数据。
3.  **提取有效数据**：获取到的原始数据是一个三维结构。我们只需要其中第三个维度的全部数据，这包含了我们需要的因子值。

![](img/9f286ad266d965f7ec44fdac59d5dd40_34.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_36.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_38.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_40.png)

对应的核心代码逻辑如下：
```python
for i in range(len(date_sequence)):
    # 获取第i天的因子数据
    daily_data = get_factor_on_date(i)
    # 提取第三个维度的有效数据
    useful_values = daily_data[:, :, :]
```

![](img/9f286ad266d965f7ec44fdac59d5dd40_42.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_44.png)

## 构建并拼接数据表

![](img/9f286ad266d965f7ec44fdac59d5dd40_46.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_48.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_50.png)

获取到每一天的数据后，我们需要将它们汇总到一个总的数据表中。我们希望这个总表包含日期、股票名称和对应的因子值三列信息。

![](img/9f286ad266d965f7ec44fdac59d5dd40_52.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_54.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_56.png)

以下是构建和拼接数据表的具体操作：

![](img/9f286ad266d965f7ec44fdac59d5dd40_58.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_60.png)

1.  **初始化总表**：首先创建一个空的 `DataFrame` 作为总表。
2.  **构建每日数据表**：在每次循环中，将当天的因子值构建成一个临时的 `DataFrame`，并为其添加“日期”和“因子值”两列。
3.  **执行拼接操作**：使用 `pandas.concat()` 函数，将每天构建的临时 `DataFrame` 拼接到总表中。**注意**，拼接结果需要重新赋值给总表变量。

![](img/9f286ad266d965f7ec44fdac59d5dd40_62.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_64.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_66.png)

以下是实现数据拼接的关键代码：
```python
import pandas as pd

![](img/9f286ad266d965f7ec44fdac59d5dd40_68.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_70.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_72.png)

# 1. 初始化总表
total_df = pd.DataFrame()

![](img/9f286ad266d965f7ec44fdac59d5dd40_74.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_76.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_78.png)

for i in range(len(date_sequence)):
    # 2. 构建每日数据表
    daily_values = get_useful_values(i) # 假设此函数返回当天的因子值数组
    current_date = date_sequence[i] # 假设此变量代表当前日期

    daily_df = pd.DataFrame(daily_values, columns=['factor_value'])
    daily_df['date'] = current_date

    # 3. 执行拼接操作，并重新赋值
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

![](img/9f286ad266d965f7ec44fdac59d5dd40_80.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_82.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_84.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_86.png)

## 检查与验证结果

![](img/9f286ad266d965f7ec44fdac59d5dd40_88.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_90.png)

数据拼接完成后，必须检查结果是否符合预期。我们需要确认总表中是否包含了正确的日期、股票信息以及对应的因子数值。

![](img/9f286ad266d965f7ec44fdac59d5dd40_92.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_94.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_96.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_98.png)

可以通过打印总表的前几行（使用 `head()` 方法）来进行快速验证，确保数据结构正确无误。

![](img/9f286ad266d965f7ec44fdac59d5dd40_100.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_102.png)

![](img/9f286ad266d965f7ec44fdac59d5dd40_104.png)

本节课中我们一起学习了如何通过循环遍历获取给定时间区间内的全部金融因子数据，并利用 `pandas` 库的 `DataFrame` 和 `concat` 功能，将这些零散的日度数据整合成一个完整、结构化的数据集。这个过程是进行后续时间序列分析和建模的基础。虽然数据获取过程稍显繁琐，但这是构建可靠量化模型必不可少的步骤。