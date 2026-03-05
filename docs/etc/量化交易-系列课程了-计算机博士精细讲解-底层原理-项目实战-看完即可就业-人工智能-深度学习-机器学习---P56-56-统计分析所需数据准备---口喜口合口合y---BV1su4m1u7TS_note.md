# Python金融分析+量化交易：P56：统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析准备数据。我们将基于股票价格的前两天和前一天的变化情况，来统计第三天涨跌的概率。这是一种基于历史频率的简单策略分析方法，虽然传统，但能帮助我们熟悉Python在金融数据处理中的应用。

## 数据准备与思路分析

![](img/4b9f7e96abfa213a51d21b58702e6ddd_1.png)

上一节我们介绍了统计分析的基本概念，本节中我们来看看如何具体实现数据准备。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_3.png)

统计分析的核心思路是：基于历史数据，统计在特定“前两天”和“前一天”的价格变化组合下，第三天出现“涨”或“跌”的频率。例如，如果连续两天都下跌，那么第三天上涨的概率有多大？通过统计所有可能的组合，我们可以得到一个基于历史概率的简单交易策略。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_5.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_7.png)

以下是实现此分析需要完成的主要步骤：
1.  准备包含“前一天”和“前两天”价格变化的数据。
2.  将价格变化数据转换为“涨”（标记为1）或“跌”（标记为0）的二元标签。
3.  将转换后的标签与实际第三天的价格方向进行组合。
4.  使用分组统计方法，计算每种“前两天”组合下，第三天涨跌的频次。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_9.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_11.png)

## 数据转换：将价格变化转为涨跌标签

![](img/4b9f7e96abfa213a51d21b58702e6ddd_13.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_15.png)

首先，我们需要一个函数来处理原始数据，将连续的价格变化值（如涨跌幅）转换为简单的涨（1）或跌（0）标签。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_17.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_19.png)

```python
def create_binary_data(data):
    """
    将价格变化数据转换为二元标签（涨/跌）。
    参数:
        data: 包含价格变化特征的DataFrame。
    """
    # 定义需要转换的列名列表，这里假设是‘L1’（前一天）和‘L2’（前两天）
    global col_names
    col_names = ['L1', 'L2']  # 根据你的实际列名修改

    # 遍历每一列进行转换
    for col in col_names:
        # 为新列命名，例如‘L1’ -> ‘L1_GB’
        new_col_name = col + '_GB'
        # 判断：如果值大于0（涨）则标记为1，否则标记为0
        data[new_col_name] = (data[col] > 0).astype(int)
```

![](img/4b9f7e96abfa213a51d21b58702e6ddd_21.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_23.png)

执行这个函数，将转换应用到我们的数据上。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_25.png)

```python
# 假设df是包含‘L1’和‘L2’列的DataFrame
create_binary_data(df)
# 查看转换后的前几行数据
print(df[['L1_GB', 'L2_GB']].head())
```

![](img/4b9f7e96abfa213a51d21b58702e6ddd_27.png)

执行后，数据中会增加`L1_GB`和`L2_GB`两列。值为1表示对应周期价格上涨，值为0表示价格下跌或不变。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_29.png)

## 整合实际价格方向数据

![](img/4b9f7e96abfa213a51d21b58702e6ddd_31.png)

为了进行统计，我们还需要知道每一天实际的涨跌情况。这通常已经存在于原始数据中，例如一个名为 `direction` 的列，其中记录了当天价格相对于前一天的变动方向（涨、跌、平）。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_33.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_35.png)

我们需要将这个实际方向数据与刚刚生成的二元标签数据结合起来。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_37.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_39.png)

```python
# 假设原始数据中‘direction’列表示实际价格方向
# 我们选取相关的列进行后续分析
analysis_df = df[['L1_GB', 'L2_GB', 'direction']].copy()
print(analysis_df.head())
```

![](img/4b9f7e96abfa213a51d21b58702e6ddd_41.png)

现在，`analysis_df` 包含了“前一天的涨跌标签”、“前两天的涨跌标签”以及“当天的实际方向”，这正是我们进行分组统计所需的数据结构。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_43.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_45.png)

## 使用GroupBy进行分组频次统计

![](img/4b9f7e96abfa213a51d21b58702e6ddd_47.png)

有了准备好的数据，接下来就可以使用Pandas的`groupby`方法进行统计了。我们将按照`L1_GB`（前一天状态）和`L2_GB`（前两天状态）进行分组，并统计每组内`direction`（实际方向）的频次。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_49.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_51.png)

以下是具体操作：

![](img/4b9f7e96abfa213a51d21b58702e6ddd_53.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_55.png)

```python
# 按‘L1_GB’和‘L2_GB’分组，并统计每个分组的大小
grouped_result = analysis_df.groupby(['L1_GB', 'L2_GB', 'direction']).size()
print(grouped_result)
```

![](img/4b9f7e96abfa213a51d21b58702e6ddd_57.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_59.png)

这行代码会输出一个序列（Series），其索引是`(L1_GB状态, L2_GB状态, 实际方向)`的组合，值是该组合在历史数据中出现的次数。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_61.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_63.png)

例如，输出`(0, 0, -1) 45`表示在历史数据中，出现“前一天跌（0）且前两天跌（0），而当天实际也跌（-1）”的情况共有45次。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_65.png)

![](img/4b9f7e96abfa213a51d21b58702e6ddd_67.png)

通过这个统计结果，我们就可以计算在任意给定的历史价格变化模式下，未来一天上涨或下跌的条件概率，从而为简单的统计套利策略提供依据。

![](img/4b9f7e96abfa213a51d21b58702e6ddd_69.png)

## 课程总结

![](img/4b9f7e96abfa213a51d21b58702e6ddd_71.png)

本节课中我们一起学习了为统计分析准备数据的关键步骤。我们首先将连续的价格变化数据转换为“涨/跌”二元标签，然后整合实际的价格方向数据，最后利用Pandas的`groupby`功能高效地统计了各种历史模式出现的频次。这个过程虽然实现了一个较为传统的分析思路，但清晰地展示了如何使用Python进行金融数据的处理和初步分析，为后续更复杂的量化策略开发打下了基础。