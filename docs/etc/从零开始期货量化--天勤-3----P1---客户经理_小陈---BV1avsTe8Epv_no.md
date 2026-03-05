# 从零开始期货量化--天勤：3.12：Pandas中的数据分组处理 📊

![](img/1fc3d2a15be5500e7eff9b730df0713f_1.png)

在本节课中，我们将学习如何使用Pandas进行数据的分组处理。分组处理是数据分析中的核心技能，尤其在量化策略回测中，它能帮助我们高效地从合并后的“大表”中提取截面数据或按品种分析时序数据。

## 数据准备与导入

首先，我们需要导入本节课所需的数据。数据是一个包含多个期货品种日线信息的“大表”。

```python
import pandas as pd

# 导入数据
file_path = ‘你的文件路径/全品种日线数据.csv‘
df = pd.read_csv(file_path, encoding=‘GBK‘)

# 对数据进行排序，方便后续观察
df = df.sort_values(by=[‘合约代码‘, ‘交易日期‘])
print(df.head(200))
```

执行以上代码后，我们会看到一个按合约代码和交易日期排序的DataFrame。数据先按品种排列，每个品种下是其时序数据。

## 初识GroupBy操作

`groupby`是Pandas中用于分组的核心操作。它本身并不立即执行计算，而是创建一个“分组对象”，等待后续的聚合指令。

```python
# 按‘交易日期‘分组，此时仅创建分组对象
grouped_by_date = df.groupby(‘交易日期‘)
print(grouped_by_date)  # 输出：<pandas.core.groupby.generic.DataFrameGroupBy object at 0x...>
```

## 基础分组统计

上一节我们创建了分组对象，本节中我们来看看如何基于它进行简单的统计。

以下是几种常见的分组统计操作：

*   **统计每日交易品种数量**：使用`.size()`方法可以统计每个分组下的数据行数，即每日有多少个品种在交易。
    ```python
    daily_ticker_count = df.groupby(‘交易日期‘).size()
    print(daily_ticker_count.head())
    ```

*   **统计每个品种的交易天数**：按‘合约代码‘分组后统计大小，即可得到每个品种在数据集中有多少个交易日。
    ```python
    ticker_days_count = df.groupby(‘合约代码‘).size()
    print(ticker_days_count.head())
    ```

*   **获取特定分组数据**：使用`.get_group()`方法可以提取出指定分组的所有数据，例如获取某一天或某一个品种的全部记录。
    ```python
    # 获取2023-11-01所有品种的数据（截面数据）
    df_20231101 = df.groupby(‘交易日期‘).get_group(‘2023-11-01‘)
    print(df_20231101.head())

    # 获取‘螺纹钢主力‘品种的所有时序数据
    df_rb_main = df.groupby(‘合约代码‘).get_group(‘螺纹钢主力‘)
    print(df_rb_main.head())
    ```

## 分组描述与数据选取

在掌握了基础统计后，我们可以对每个分组进行更详细的描述性分析，或灵活地选取特定行。

以下是分组描述与数据选取的常用方法：

*   **分组描述性统计**：`.describe()`方法可以对每个分组内的数值列进行快速统计概览（如计数、均值、标准差、分位数等）。
    ```python
    desc_by_ticker = df.groupby(‘合约代码‘).describe()
    print(desc_by_ticker.head())
    ```

*   **获取每个分组的前/后N行**：使用`.head(n)`或`.tail(n)`可以分别获取每个分组的前n行或后n行数据。
    ```python
    # 每个品种取前3行数据
    first_three_rows = df.groupby(‘合约代码‘).head(3)
    print(first_three_rows.head(12)) # 查看前几个品种的结果

    # 每个品种取后3行数据（最新数据）
    last_three_rows = df.groupby(‘合约代码‘).tail(3)
    print(last_three_rows.tail(12))
    ```

*   **获取每个分组的第一行或最后一行**：`.first()`和`.last()`方法专门用于此目的。
    ```python
    first_row_each = df.groupby(‘合约代码‘).first()
    last_row_each = df.groupby(‘合约代码‘).last()
    ```

*   **获取每个分组的第N行**：使用`.nth(n)`可以获取每个分组中指定位置的行（从0开始计数）。
    ```python
    # 获取每个品种的第2行数据（索引为1）
    second_row_each = df.groupby(‘合约代码‘).nth(1)
    print(second_row_each.head())
    ```

## 分组聚合计算

分组聚合是`groupby`的核心应用之一，我们可以对分组后的数据列进行求和、求平均、找最大值等计算。

以下是分组聚合计算的示例：

*   **单列聚合**：对分组后的某一列直接应用聚合函数，如求每个品种的收盘价均值、成交量总和。
    ```python
    mean_close = df.groupby(‘合约代码‘)[‘收盘价‘].mean()
    sum_volume = df.groupby(‘合约代码‘)[‘成交量‘].sum()
    print(mean_close.head())
    print(sum_volume.head())
    ```

*   **多列聚合**：可以同时对多列应用相同的聚合函数。
    ```python
    mean_values = df.groupby(‘合约代码‘)[[‘收盘价‘, ‘成交量‘]].mean()
    print(mean_values.head())
    ```

*   **分组内排序**：在分组的基础上进行排序，例如计算每日各品种成交量的排名。
    ```python
    # 按交易日期分组，对‘成交量‘列进行排名
    df[‘volume_rank‘] = df.groupby(‘交易日期‘)[‘成交量‘].rank(ascending=False)
    # 使用百分比排名
    df[‘volume_rank_pct‘] = df.groupby(‘交易日期‘)[‘成交量‘].rank(pct=True)
    print(df[[‘交易日期‘, ‘合约代码‘, ‘成交量‘, ‘volume_rank‘, ‘volume_rank_pct‘]].head(20))
    ```

## 高级分组技巧：多级分组与自定义

最后，我们来看一些更灵活的分组操作，例如按多个条件分组，或在分组后应用自定义函数。

*   **多级分组**：可以按多个列进行分组，形成层次化的索引结构。
    ```python
    # 示例：先按品种，再按我们自定义的‘旬度‘分组
    # 首先创建一个‘旬度‘列
    df[‘交易日期_dt‘] = pd.to_datetime(df[‘交易日期‘])
    df[‘day_of_month‘] = df[‘交易日期_dt‘].dt.day
    conditions = [
        (df[‘day_of_month‘] <= 10),
        (df[‘day_of_month‘] > 10) & (df[‘day_of_month‘] <= 20),
        (df[‘day_of_month‘] > 20)
    ]
    choices = [‘上旬‘, ‘中旬‘, ‘下旬‘]
    df[‘旬度‘] = np.select(conditions, choices)

    # 进行多级分组并统计大小
    multi_group_size = df.groupby([‘合约代码‘, ‘旬度‘]).size()
    print(multi_group_size.head(20))
    ```

*   **分组后应用自定义操作**：通过`.apply()`方法或循环，可以在每个分组内执行更复杂的逻辑。
    ```python
    # 示例：计算每个品种收盘价与成交量的横向平均值（行级别）
    def calc_cross_mean(sub_df):
        # sub_df 是单个品种的DataFrame
        sub_df[‘均价_自定义‘] = sub_df[[‘收盘价‘, ‘成交量‘]].mean(axis=1)
        return sub_df

    df_with_cross_mean = df.groupby(‘合约代码‘, group_keys=False).apply(calc_cross_mean)
    print(df_with_cross_mean[[‘合约代码‘, ‘收盘价‘, ‘成交量‘, ‘均价_自定义‘]].head())
    ```

## 总结

![](img/1fc3d2a15be5500e7eff9b730df0713f_3.png)

![](img/1fc3d2a15be5500e7eff9b730df0713f_5.png)

本节课中我们一起学习了Pandas中强大的`groupby`数据分组处理功能。我们从创建分组对象开始，逐步掌握了基础统计、数据选取、聚合计算以及多级分组等核心操作。这些技能是处理面板数据、进行截面分析以及为多因子策略回测准备数据的基础。请记住，`groupby`的核心思想是“拆分-应用-合并”，灵活运用它可以极大地提升数据处理的效率和表达能力。在后续的实战项目中，我们将反复运用这些技巧来解决实际的量化分析问题。