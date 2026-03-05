# Python金融量化与业务数据分析：P32：用户生命周期分析

![](img/06f7773e6a35c17749cfddf4fcd07cc2_1.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_3.png)

在本节课中，我们将学习如何对用户的生命周期进行分析。我们将基于用户的消费记录，通过一系列数据处理步骤，将用户划分为不同的活跃度类型，例如新用户、活跃用户、回头客等，并最终统计出每个月份各类用户的数量。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_5.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_7.png)

## 概述

![](img/06f7773e6a35c17749cfddf4fcd07cc2_9.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_11.png)

用户生命周期分析是业务数据分析中的重要环节，它帮助我们理解用户在不同时间点的活跃状态。本节课的核心目标是：根据用户的月度消费记录，计算每个用户每月的活跃度状态，并进行汇总统计。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_13.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_15.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_17.png)

---

![](img/06f7773e6a35c17749cfddf4fcd07cc2_19.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_21.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_23.png)

## 第一步：统计用户月度消费次数

![](img/06f7773e6a35c17749cfddf4fcd07cc2_25.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_27.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_29.png)

首先，我们需要统计每个用户在每个月的消费次数。我们将使用数据透视表（pivot table）来完成这个任务。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_31.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_33.png)

以下是具体步骤：

![](img/06f7773e6a35c17749cfddf4fcd07cc2_35.png)

```python
# 创建数据透视表，统计每个用户每月的消费次数
pivot_count = df.pivot_table(
    index='user_id',          # 行索引为用户ID
    columns='order_dt',       # 列索引为订单日期（将按月份分组）
    values='order_dt',        # 需要统计的值
    aggfunc='count',          # 聚合函数为计数
    fill_value=0              # 将空值填充为0
)
```

![](img/06f7773e6a35c17749cfddf4fcd07cc2_37.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_39.png)

执行以上代码后，我们将得到一个DataFrame。其中，行代表不同的用户，列代表不同的月份，单元格内的数字表示该用户在该月的消费次数。数值0表示该用户在该月没有消费。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_41.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_43.png)

---

![](img/06f7773e6a35c17749cfddf4fcd07cc2_45.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_47.png)

## 第二步：转换消费次数为消费状态

![](img/06f7773e6a35c17749cfddf4fcd07cc2_49.png)

上一节我们统计了消费次数，但生命周期分析更关注用户“是否消费”，而非“消费了多少次”。因此，本节我们将把消费次数转换为二元的消费状态：消费记为1，未消费记为0。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_51.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_53.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_55.png)

以下是具体步骤：

![](img/06f7773e6a35c17749cfddf4fcd07cc2_57.png)

```python
# 将消费次数转换为消费状态（1表示消费，0表示未消费）
pivot_purchase = pivot_count.applymap(lambda x: 1 if x >= 1 else 0)
```

![](img/06f7773e6a35c17749cfddf4fcd07cc2_59.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_61.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_63.png)

我们使用 `applymap` 函数对DataFrame中的每个元素进行操作。如果原值大于等于1（即消费过），则转换为1；否则转换为0。这样就得到了一个只包含0和1的矩阵，清晰地展示了用户每月的消费情况。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_65.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_67.png)

---

![](img/06f7773e6a35c17749cfddf4fcd07cc2_69.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_71.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_73.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_75.png)

## 第三步：定义用户生命周期状态算法

![](img/06f7773e6a35c17749cfddf4fcd07cc2_77.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_79.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_81.png)

在得到用户每月的消费状态（0/1序列）后，我们需要根据这个序列的规律，判断用户在每个月份属于哪种生命周期状态（如新用户、活跃用户、回头客等）。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_83.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_85.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_87.png)

这是一个固定的算法，其逻辑是根据连续的0和1的排列模式来判定状态。我们直接使用定义好的函数即可。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_89.png)

```python
def active_status(data):
    # 这是一个固定的状态判定算法
    # 输入：单个用户的一行消费状态数据（0/1序列）
    # 输出：该用户每个月对应的生命周期状态列表
    status = []
    for i in range(len(data)):
        # 算法核心逻辑：根据当前及历史消费记录判断状态
        if data[i] == 1:  # 本月消费
            if sum(data[:i]) == 0:  # 历史首次消费
                status.append('新用户')
            elif data[i-1] == 1:    # 上月也消费了
                status.append('活跃用户')
            else:                   # 上月未消费，本月消费
                status.append('回头客')
        else:  # 本月未消费
            if sum(data[:i]) == 0:  # 历史从未消费
                status.append('未注册用户')
            else:                   # 历史消费过，但本月未消费
                status.append('流失用户')
    return status

![](img/06f7773e6a35c17749cfddf4fcd07cc2_91.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_93.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_95.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_97.png)

# 将算法应用到每个用户的行数据上
purchase_status = pivot_purchase.apply(active_status, axis=1)
```

![](img/06f7773e6a35c17749cfddf4fcd07cc2_99.png)

执行 `apply` 函数后，我们得到一个Series对象。其索引是用户ID，每个值是一个列表，包含了该用户在每个月对应的生命周期状态标签。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_101.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_103.png)

---

![](img/06f7773e6a35c17749cfddf4fcd07cc2_105.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_107.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_109.png)

## 第四步：整理生命周期状态数据

![](img/06f7773e6a35c17749cfddf4fcd07cc2_111.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_113.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_115.png)

上一节我们得到了一个包含状态列表的Series。为了更直观地分析，本节我们将把这个Series转换成一个与原始消费状态表结构一致的DataFrame。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_117.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_119.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_121.png)

以下是具体步骤：

![](img/06f7773e6a35c17749cfddf4fcd07cc2_123.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_125.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_126.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_128.png)

```python
# 1. 将Series中的状态列表提取出来，转换成列表的列表
status_list = purchase_status.tolist()

![](img/06f7773e6a35c17749cfddf4fcd07cc2_130.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_132.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_134.png)

# 2. 用这个列表创建新的DataFrame
status_df = pd.DataFrame(
    status_list,                     # 数据源
    index=pivot_purchase.index,      # 行索引（用户ID）
    columns=pivot_purchase.columns   # 列索引（月份）
)
```

![](img/06f7773e6a35c17749cfddf4fcd07cc2_136.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_138.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_140.png)

现在，`status_df` 这个DataFrame的行和列结构与之前的消费状态表完全一致，但单元格内不再是0或1，而是具体的用户状态标签，如“新用户”、“活跃用户”等。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_142.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_144.png)

---

![](img/06f7773e6a35c17749cfddf4fcd07cc2_146.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_148.png)

## 第五步：按月统计各状态用户数量

![](img/06f7773e6a35c17749cfddf4fcd07cc2_150.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_152.png)

最后，我们需要统计在每个月份中，处于不同生命周期状态的用户分别有多少人。这需要对上一步得到的状态表进行按列统计。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_154.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_156.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_158.png)

以下是具体步骤：

![](img/06f7773e6a35c17749cfddf4fcd07cc2_160.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_162.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_164.png)

```python
# 对状态DataFrame的每一列（即每个月）进行值计数
status_count = status_df.apply(pd.Series.value_counts)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_166.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_168.png)

# 为了便于阅读，通常将结果转置，让月份作为行，状态作为列
status_count_t = status_count.T.fillna(0)  # 转置并填充NaN为0
```

![](img/06f7773e6a35c17749cfddf4fcd07cc2_170.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_172.png)

执行以上代码后，`status_count_t` 就是一个清晰的统计表。每一行代表一个月份，每一列代表一种用户状态，表格中的数字就是该月处于该状态的用户数量。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_174.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_176.png)

---

![](img/06f7773e6a35c17749cfddf4fcd07cc2_178.png)

## 总结

![](img/06f7773e6a35c17749cfddf4fcd07cc2_180.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_182.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_184.png)

本节课中我们一起学习了用户生命周期分析的完整流程：

![](img/06f7773e6a35c17749cfddf4fcd07cc2_186.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_188.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_190.png)

1.  **统计消费次数**：使用数据透视表统计每个用户每月的消费次数。
2.  **转换消费状态**：将消费次数简化为“是否消费”的二元状态（0/1）。
3.  **应用状态算法**：利用固定算法，根据用户的消费状态序列判断其每月的生命周期状态。
4.  **整理状态数据**：将算法结果整理成结构清晰的DataFrame。
5.  **汇总状态统计**：按月统计各类状态用户的数量，得到最终的分析报表。

![](img/06f7773e6a35c17749cfddf4fcd07cc2_192.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_194.png)

![](img/06f7773e6a35c17749cfddf4fcd07cc2_196.png)

通过这五个步骤，我们就能从原始的消费记录中，提炼出用户活跃度随时间变化的洞察，为业务决策提供数据支持。本课的重点在于理解整体分析逻辑，其中的状态判定算法可以作为工具直接应用。