# Python金融量化分析：P43：获取给定区间全部数据 📊

![](img/9fd00404fa68c9fcf42405913bc1aa73_0.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_2.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_4.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_6.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_8.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_10.png)

在本节课中，我们将学习如何通过循环遍历一个日期序列，获取指定时间区间内每一天的股票因子数据，并将这些数据整理成一个结构化的总表。我们将使用`pandas`库来创建和拼接`DataFrame`，最终得到一个包含日期、股票名称和因子值的完整数据集。

![](img/9fd00404fa68c9fcf42405913bc1aa73_12.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_14.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_16.png)

---

![](img/9fd00404fa68c9fcf42405913bc1aa73_18.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_20.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_22.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_24.png)

上一节我们介绍了如何获取单日的因子数据。本节中，我们来看看如何获取一个连续时间区间内的全部数据。

![](img/9fd00404fa68c9fcf42405913bc1aa73_26.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_28.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_30.png)

我们的核心思路是：**遍历日期序列中的每一天，获取当天的因子数据，然后将每天的数据拼接成一个总的`DataFrame`**。

![](img/9fd00404fa68c9fcf42405913bc1aa73_32.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_34.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_36.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_38.png)

以下是实现这一过程的具体步骤：

![](img/9fd00404fa68c9fcf42405913bc1aa73_40.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_42.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_44.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_46.png)

1.  **确定日期范围**：首先，我们需要明确要获取数据的时间区间。这个区间由一个日期序列定义。
2.  **遍历每一天**：使用`for`循环遍历这个日期序列中的每一个日期。
3.  **获取单日数据**：在循环内部，调用获取单日因子数据的函数，传入当前遍历的日期索引`i`，以获取第`i`天的数据。
4.  **处理数据维度**：获取的数据通常是三维的。我们通过索引操作提取有用的部分，例如使用`[0, 2]`和`[:, 2]`来获取特定维度的数据。
5.  **构建每日`DataFrame`**：将处理好的单日数据（因子值）放入一个新的`DataFrame`中。同时，为这个`DataFrame`添加一列，记录对应的日期。
6.  **拼接总表**：在循环开始前，初始化一个空的`DataFrame`作为总表。在每次循环中，将构建好的每日`DataFrame`拼接到这个总表中。
7.  **检查结果**：循环结束后，打印总表的前几行数据，检查数据格式是否符合预期（包含日期、股票名称、因子值）。

![](img/9fd00404fa68c9fcf42405913bc1aa73_48.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_50.png)

以下是核心步骤的代码框架：

![](img/9fd00404fa68c9fcf42405913bc1aa73_52.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_54.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_56.png)

```python
import pandas as pd

![](img/9fd00404fa68c9fcf42405913bc1aa73_58.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_60.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_62.png)

# 假设 dates 是日期序列，get_daily_factor 是获取单日数据的函数
total_df = pd.DataFrame() # 初始化总表

![](img/9fd00404fa68c9fcf42405913bc1aa73_64.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_66.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_68.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_70.png)

for i in range(len(dates)):
    # 1. 获取第i天的原始数据
    daily_raw_data = get_daily_factor(i)

    # 2. 提取所需的因子值（假设是三维数据的第三维全部）
    factor_values = daily_raw_data[0, 2, :] # 请根据实际数据结构调整索引

    # 3. 构建每日的DataFrame
    daily_df = pd.DataFrame({
        'date': dates[i], # 日期列
        'stock': stock_list, # 股票名称列表，需要预先定义或从数据中提取
        'factor_value': factor_values # 因子值列
    })

    # 4. 将每日DataFrame拼接到总表
    total_df = pd.concat([total_df, daily_df], ignore_index=True)

![](img/9fd00404fa68c9fcf42405913bc1aa73_72.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_74.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_76.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_78.png)

# 5. 查看结果
print(total_df.head())
```

![](img/9fd00404fa68c9fcf42405913bc1aa73_80.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_82.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_84.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_86.png)

**注意**：获取一年或更长时间的数据可能需要一些计算时间。如果数据量很大，循环遍历会相对较慢，但这是获取区间完整数据的常用方法。

![](img/9fd00404fa68c9fcf42405913bc1aa73_88.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_90.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_92.png)

---

![](img/9fd00404fa68c9fcf42405913bc1aa73_94.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_96.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_98.png)

![](img/9fd00404fa68c9fcf42405913bc1aa73_100.png)

本节课中我们一起学习了如何获取给定时间区间内的全部因子数据。关键点在于**循环遍历日期并逐日获取数据**，然后使用`pd.concat`函数将所有日期的数据**拼接**成一个完整的表格。这个过程虽然步骤较多，但逻辑清晰，是构建量化分析数据集的基础操作。