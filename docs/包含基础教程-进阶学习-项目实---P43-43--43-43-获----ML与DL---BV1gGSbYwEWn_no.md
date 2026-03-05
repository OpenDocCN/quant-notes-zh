# 量化交易教程：P43：获取给定区间全部数据 📊

![](img/55d952e56d0d2ebfd1a7b74d2d949119_1.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_3.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_5.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_7.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_9.png)

## 概述
在本节课中，我们将学习如何从量化交易数据中，获取一个指定时间区间内的全部数据。我们将通过编写一个循环，逐日提取数据，并将这些数据整理成一个结构化的表格，以便于后续的分析和处理。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_11.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_13.png)

---

![](img/55d952e56d0d2ebfd1a7b74d2d949119_15.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_17.png)

上一节我们介绍了如何获取单日的数据。本节中我们来看看如何获取一个连续时间段内的所有数据。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_19.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_21.png)

如果你的数据是一个日期序列，你需要查询其中每一天的数据。最直接的方法是使用一个 `for` 循环来遍历序列中的每一天。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_23.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_25.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_27.png)

以下是实现这一过程的核心步骤：

![](img/55d952e56d0d2ebfd1a7b74d2d949119_29.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_31.png)

1.  **确定数据总天数**：首先，你需要知道你的日期序列总共有多少天。
    ```python
    total_days = len(date_sequence)
    ```
2.  **遍历每一天**：使用 `for` 循环遍历从第0天到最后一天。
    ```python
    for i in range(total_days):
        # 处理第i天的数据
    ```
3.  **获取每日数据**：在循环内部，调用获取单日数据的函数，并传入当前索引 `i`。
    ```python
    daily_data = get_single_day_data(i)
    ```
    这个函数返回的是一个三维数据。我们通常只关心第三个维度的全部数据，因为它包含了所有有用的指标。
    ```python
    useful_values = daily_data[0, 2, :]  # 示例：获取特定维度的数据
    ```
4.  **构建每日数据表**：对于每一天，我们都需要构建一个小的数据表（DataFrame）。这个表至少应包含三列信息：
    *   **日期**：数据对应的具体日期。
    *   **股票名称/代码**：标识是哪一只股票。
    *   **指标值**：该股票在该日期的具体数值。
    ```python
    daily_df = pd.DataFrame({
        'date': current_date,
        'stock': stock_name,
        'factor_value': useful_values
    })
    ```
5.  **汇总所有数据**：在循环开始前，创建一个空的总表。在每次循环中，将构建好的每日数据表拼接到这个总表中。
    ```python
    # 初始化总表
    total_df = pd.DataFrame()
    
    for i in range(total_days):
        # ... 构建 daily_df ...
        # 将每日数据拼接到总表
        total_df = pd.concat([total_df, daily_df], ignore_index=True)
    ```
    注意，`pd.concat` 操作后需要将结果重新赋值给 `total_df`，否则总表的内容不会更新。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_33.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_35.png)

---

![](img/55d952e56d0d2ebfd1a7b74d2d949119_37.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_39.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_41.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_43.png)

## 代码执行与结果
按照上述步骤编写并执行代码后，程序会遍历指定时间段（例如从2019年1月1日到2020年1月1日）的每一天。由于数据量可能较大，这个过程可能需要一些时间。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_45.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_47.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_49.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_51.png)

执行完毕后，你可以通过 `total_df.head()` 来查看总表的前几行数据，确认其是否包含了日期、股票信息和指标值这三列，并且数据是按日期排列的。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_53.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_55.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_57.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_59.png)

这样，我们就完成了从原始数据中提取并整理指定区间全部数据的第一步。虽然这个过程略显繁琐，但这是进行批量数据分析和构建模型的基础。

![](img/55d952e56d0d2ebfd1a7b74d2d949119_61.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_63.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_65.png)

---

![](img/55d952e56d0d2ebfd1a7b74d2d949119_67.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_69.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_71.png)

![](img/55d952e56d0d2ebfd1a7b74d2d949119_73.png)

## 总结
本节课中我们一起学习了如何获取给定时间区间内的全部量化交易数据。关键点在于使用循环遍历日期，逐日提取数据，并将结果整合到一个统一的 `DataFrame` 中。这个方法虽然基础，但却是处理时间序列金融数据不可或缺的技能，为后续的数据分析、特征工程和模型训练做好了准备。