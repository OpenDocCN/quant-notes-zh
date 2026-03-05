# Python金融量化交易：P43：获取给定区间全部数据 📊

![](img/6dd35962ccb55e103a36b5d74cd1f808_0.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_2.png)

在本节课中，我们将学习如何通过循环遍历指定日期区间内的每一天，来获取并整合金融因子数据。我们将使用`pandas`库来构建一个包含日期、股票名称和因子值的总数据表。

![](img/6dd35962ccb55e103a36b5d74cd1f808_4.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_6.png)

---

![](img/6dd35962ccb55e103a36b5d74cd1f808_8.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_10.png)

## 概述与思路

![](img/6dd35962ccb55e103a36b5d74cd1f808_12.png)

上一节我们介绍了如何获取单日的因子数据。本节中，我们来看看如何获取一个连续日期区间内的全部数据。

![](img/6dd35962ccb55e103a36b5d74cd1f808_14.png)

核心思路是：我们有一个日期序列，需要遍历其中的每一天。对于每一天，我们都执行一次获取单日数据的操作，然后将每天获取的数据拼接起来，最终形成一个总的数据表。

![](img/6dd35962ccb55e103a36b5d74cd1f808_16.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_18.png)

---

![](img/6dd35962ccb55e103a36b5d74cd1f808_20.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_22.png)

## 实现步骤详解

![](img/6dd35962ccb55e103a36b5d74cd1f808_24.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_26.png)

以下是实现这一过程的具体步骤。

![](img/6dd35962ccb55e103a36b5d74cd1f808_28.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_30.png)

### 1. 确定日期区间长度

![](img/6dd35962ccb55e103a36b5d74cd1f808_32.png)

首先，我们需要知道要遍历的日期区间总共有多少天。这决定了我们的循环次数。

![](img/6dd35962ccb55e103a36b5d74cd1f808_34.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_36.png)

```python
# 假设 dates 是一个日期序列（例如 pandas 的 DatetimeIndex）
total_days = len(dates)
```

![](img/6dd35962ccb55e103a36b5d74cd1f808_38.png)

### 2. 遍历每一天并获取数据

![](img/6dd35962ccb55e103a36b5d74cd1f808_40.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_42.png)

接下来，我们使用一个`for`循环来遍历日期区间内的每一天。

![](img/6dd35962ccb55e103a36b5d74cd1f808_44.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_46.png)

```python
for i in range(total_days):
    # 获取第 i 天的日期
    current_date = dates[i]
    # 获取第 i 天的因子数据（假设 get_factor_data 是获取单日数据的函数）
    daily_data = get_factor_data(current_date)
```

![](img/6dd35962ccb55e103a36b5d74cd1f808_48.png)

在获取单日数据时，返回的结果可能是一个多维数组。根据之前的学习，我们通常只关心其中特定的维度（例如第三个维度包含了所有股票的因子值）。

![](img/6dd35962ccb55e103a36b5d74cd1f808_50.png)

```python
    # 提取有用的数据部分，例如第三个维度的全部数据
    useful_values = daily_data[0, 2, :]  # 这是一个示例索引，具体根据数据结构调整
```

![](img/6dd35962ccb55e103a36b5d74cd1f808_52.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_54.png)

### 3. 构建每日数据框

![](img/6dd35962ccb55e103a36b5d74cd1f808_56.png)

对于每一天获取的数据，我们希望将其组织成一个结构化的`DataFrame`，包含日期和对应的因子值。

![](img/6dd35962ccb55e103a36b5d74cd1f808_58.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_60.png)

```python
    # 为当前日期创建一个临时的 DataFrame
    daily_df = pd.DataFrame()
    # 添加一列，命名为‘value’，存放因子值
    daily_df['value'] = useful_values
    # 添加一列，命名为‘date’，存放当前日期
    daily_df['date'] = current_date
```

![](img/6dd35962ccb55e103a36b5d74cd1f808_62.png)

### 4. 拼接至总表

![](img/6dd35962ccb55e103a36b5d74cd1f808_64.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_66.png)

我们需要一个初始为空的“总表”`DataFrame`，在每次循环中将当天的`daily_df`拼接进去。

![](img/6dd35962ccb55e103a36b5d74cd1f808_68.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_70.png)

```python
# 在循环开始前，初始化一个空的 DataFrame 作为总表
total_df = pd.DataFrame()

![](img/6dd35962ccb55e103a36b5d74cd1f808_72.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_74.png)

# 在循环内部，进行拼接操作
for i in range(total_days):
    # ... (获取数据并构建 daily_df 的代码)
    
    # 将当天的数据框拼接到总表中
    total_df = pd.concat([total_df, daily_df], ignore_index=True)
```

![](img/6dd35962ccb55e103a36b5d74cd1f808_76.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_78.png)

**注意**：`pd.concat`操作会返回一个新的`DataFrame`，必须将其赋值回`total_df`，否则总表的内容不会更新。

![](img/6dd35962ccb55e103a36b5d74cd1f808_80.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_82.png)

### 5. 检查与验证

![](img/6dd35962ccb55e103a36b5d74cd1f808_84.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_86.png)

循环执行完毕后，我们可以查看总表的前几行，以验证数据是否按预期整合。

![](img/6dd35962ccb55e103a36b5d74cd1f808_88.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_90.png)

```python
print(total_df.head())
```

![](img/6dd35962ccb55e103a36b5d74cd1f808_92.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_94.png)

预期的输出应该包含`date`和`value`两列，每一行对应特定日期下某只股票的因子值。

![](img/6dd35962ccb55e103a36b5d74cd1f808_96.png)

---

![](img/6dd35962ccb55e103a36b5d74cd1f808_98.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_100.png)

## 总结

![](img/6dd35962ccb55e103a36b5d74cd1f808_102.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_104.png)

本节课中我们一起学习了如何获取给定日期区间内的全部金融因子数据。

![](img/6dd35962ccb55e103a36b5d74cd1f808_106.png)

![](img/6dd35962ccb55e103a36b5d74cd1f808_108.png)

我们通过**遍历日期序列**中的每一天，对每一天都执行**获取单日数据**的操作。然后，将每天的数据**构建成结构化的`DataFrame`**，并最终**拼接成一个总的数据表**。这个过程虽然需要手动循环处理，但它是批量获取时间序列数据的有效方法。