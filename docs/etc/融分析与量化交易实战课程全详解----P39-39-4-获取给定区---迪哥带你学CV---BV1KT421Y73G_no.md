# Python金融分析与量化交易实战课程：P39：获取给定区间全部数据 📊

在本节课中，我们将学习如何从给定的时间区间内，循环获取每一天的股票因子数据，并将这些数据整理成一个结构化的总表。这个过程是量化分析中数据准备的关键步骤。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_1.png)

![](img/8ae2aa155dd30f409aadd31f3d09f45d_3.png)

## 概述与思路

上一节我们介绍了如何获取单日的因子数据。本节中我们来看看如何获取一个连续时间区间内的全部数据。核心思路是遍历区间内的每一天，获取当天的数据，然后将每天的数据拼接成一个完整的DataFrame。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_5.png)

![](img/8ae2aa155dd30f409aadd31f3d09f45d_7.png)

## 数据遍历与获取

首先，我们需要确定要遍历的日期序列的长度。这个长度代表了时间区间内的总天数。

```python
# 假设 dates 是包含所有日期的序列
total_days = len(dates)
```

接下来，我们使用一个循环来遍历每一天。

```python
for i in range(total_days):
    # 在此处获取第 i 天的数据
```

![](img/8ae2aa155dd30f409aadd31f3d09f45d_9.png)

在循环体内，我们需要获取每一天对应的因子值。这需要调用数据获取函数，并传入索引 `i`。

```python
    # 获取第 i 天的因子数据
    daily_value = get_factor_value(i)
```

![](img/8ae2aa155dd30f409aadd31f3d09f45d_11.png)

获取到的 `daily_value` 通常是一个多维数组。根据之前的知识，我们只需要其中代表实际指标值的部分。

```python
    # 提取有用的指标值（假设在第三个维度）
    useful_value = daily_value[0, 2, :]
```

![](img/8ae2aa155dd30f409aadd31f3d09f45d_13.png)

这样，我们就得到了第 `i` 天的有效数据。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_15.png)

## 构建每日数据表

![](img/8ae2aa155dd30f409aadd31f3d09f45d_17.png)

获取到单日数据后，我们不能仅仅打印它，而是需要将其存储起来。我们希望最终的总表包含三列信息：日期、股票名称和当日的因子值。

因此，在每次循环中，我们需要创建一个临时的DataFrame来存放当天的数据。

以下是构建单日DataFrame的步骤：

1.  创建一个新的DataFrame，并将提取出的指标值作为一列数据传入。
2.  为这一列数据指定一个列名，例如 `‘factor_value’`。
3.  新增一列 `‘date’`，其值为当前循环对应的日期 `dates[i]`。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_19.png)

```python
    # 创建单日数据的DataFrame
    daily_df = pd.DataFrame({‘factor_value‘: useful_value})
    # 添加日期列
    daily_df[‘date‘] = dates[i]
```

现在，`daily_df` 就包含了日期和因子值两列数据。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_21.png)

## 数据拼接与汇总

![](img/8ae2aa155dd30f409aadd31f3d09f45d_23.png)

每次循环都会生成一个新的 `daily_df`。我们需要将这些分散的每日数据表拼接成一个总表。

在循环开始前，我们先初始化一个空的DataFrame作为总表。

```python
# 初始化一个空的总表
total_df = pd.DataFrame()
```

![](img/8ae2aa155dd30f409aadd31f3d09f45d_25.png)

在每次循环生成 `daily_df` 后，我们使用 `pd.concat` 函数将其拼接到总表中。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_27.png)

```python
    # 将当天的数据拼接到总表中
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

这里使用 `ignore_index=True` 是为了在拼接后重新生成连续的行索引。

## 执行与结果验证

完成循环和拼接后，我们就得到了包含整个时间区间所有数据的总表 `total_df`。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_29.png)

![](img/8ae2aa155dd30f409aadd31f3d09f45d_31.png)

可以打印前几行数据来验证结果是否符合预期。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_33.png)

```python
print(total_df.head())
```

![](img/8ae2aa155dd30f409aadd31f3d09f45d_35.png)

输出应显示包含 `‘date’` 和 `‘factor_value’` 列的DataFrame，每一行对应特定日期下所有股票的因子值。

![](img/8ae2aa155dd30f409aadd31f3d09f45d_37.png)

## 总结

![](img/8ae2aa155dd30f409aadd31f3d09f45d_39.png)

本节课中我们一起学习了如何获取给定时间区间内的全部因子数据。我们通过遍历日期序列，循环获取每一天的数据，并将每日数据构建成DataFrame，最后使用 `pd.concat` 函数将所有日期的数据拼接成一个完整的总表。这个过程虽然步骤较多，但它是将原始时间序列数据整理为可供分析的结构化表格的标准方法。