# Python金融量化与业务数据分析：P31：5.用户消费行为分析

![](img/cc82808c798261ee070ef165fde3c97f_1.png)

![](img/cc82808c798261ee070ef165fde3c97f_2.png)

![](img/cc82808c798261ee070ef165fde3c97f_3.png)

在本节课中，我们将学习如何对用户消费行为进行分析。主要内容包括计算新老用户占比，以及基于RFM模型对用户进行价值分层。

## 用户首次与末次消费时间分析

![](img/cc82808c798261ee070ef165fde3c97f_5.png)

![](img/cc82808c798261ee070ef165fde3c97f_6.png)

![](img/cc82808c798261ee070ef165fde3c97f_7.png)

![](img/cc82808c798261ee070ef165fde3c97f_9.png)

上一节我们介绍了数据分析的基础，本节中我们来看看如何分析用户的消费时间。首先，我们需要计算用户第一次消费和最后一次消费的月份分布及人数统计，并绘制成折线图。

![](img/cc82808c798261ee070ef165fde3c97f_11.png)

![](img/cc82808c798261ee070ef165fde3c97f_12.png)

### 计算用户首次消费月份

![](img/cc82808c798261ee070ef165fde3c97f_14.png)

![](img/cc82808c798261ee070ef165fde3c97f_15.png)

![](img/cc82808c798261ee070ef165fde3c97f_17.png)

用户第一次消费的月份，可以通过对用户分组后，取其消费月份的最小值来获得。具体步骤如下：

![](img/cc82808c798261ee070ef165fde3c97f_19.png)

![](img/cc82808c798261ee070ef165fde3c97f_21.png)

![](img/cc82808c798261ee070ef165fde3c97f_22.png)

以下是计算每个用户首次消费月份的代码：
```python
first_purchase_month = df.groupby('user_id')['order_month'].min()
```

![](img/cc82808c798261ee070ef165fde3c97f_24.png)

![](img/cc82808c798261ee070ef165fde3c97f_25.png)

![](img/cc82808c798261ee070ef165fde3c97f_26.png)

### 统计各月份首次消费人数

![](img/cc82808c798261ee070ef165fde3c97f_27.png)

![](img/cc82808c798261ee070ef165fde3c97f_29.png)

获得每个用户的首次消费月份后，我们需要统计每个月有多少用户是首次消费。

![](img/cc82808c798261ee070ef165fde3c97f_31.png)

![](img/cc82808c798261ee070ef165fde3c97f_32.png)

![](img/cc82808c798261ee070ef165fde3c97f_33.png)

![](img/cc82808c798261ee070ef165fde3c97f_35.png)

以下是统计各月份首次消费人数的代码：
```python
first_purchase_count = first_purchase_month.value_counts()
```

![](img/cc82808c798261ee070ef165fde3c97f_36.png)

### 绘制首次消费人数折线图

![](img/cc82808c798261ee070ef165fde3c97f_38.png)

![](img/cc82808c798261ee070ef165fde3c97f_39.png)

数据统计完成后，我们可以将其可视化。将月份作为X轴，人数作为Y轴绘制折线图。

![](img/cc82808c798261ee070ef165fde3c97f_41.png)

以下是绘制折线图的代码：
```python
first_purchase_count.sort_index().plot()
```

![](img/cc82808c798261ee070ef165fde3c97f_42.png)

![](img/cc82808c798261ee070ef165fde3c97f_44.png)

![](img/cc82808c798261ee070ef165fde3c97f_46.png)

### 计算用户末次消费月份

![](img/cc82808c798261ee070ef165fde3c97f_47.png)

![](img/cc82808c798261ee070ef165fde3c97f_49.png)

![](img/cc82808c798261ee070ef165fde3c97f_51.png)

与首次消费类似，用户最后一次消费的月份是其消费月份的最大值。

![](img/cc82808c798261ee070ef165fde3c97f_53.png)

![](img/cc82808c798261ee070ef165fde3c97f_54.png)

![](img/cc82808c798261ee070ef165fde3c97f_55.png)

以下是计算每个用户末次消费月份的代码：
```python
last_purchase_month = df.groupby('user_id')['order_month'].max()
```

![](img/cc82808c798261ee070ef165fde3c97f_57.png)

![](img/cc82808c798261ee070ef165fde3c97f_59.png)

![](img/cc82808c798261ee070ef165fde3c97f_61.png)

### 统计各月份末次消费人数并绘图

同样地，统计各月份末次消费的用户数量并绘制折线图，可以观察用户的流失情况。折线上升趋势表明该时间段用户流失率较高。

![](img/cc82808c798261ee070ef165fde3c97f_63.png)

![](img/cc82808c798261ee070ef165fde3c97f_64.png)

![](img/cc82808c798261ee070ef165fde3c97f_65.png)

以下是统计并绘图的代码：
```python
last_purchase_count = last_purchase_month.value_counts()
last_purchase_count.sort_index().plot()
```

![](img/cc82808c798261ee070ef165fde3c97f_67.png)

## 新老用户占比分析

在分析了消费时间后，我们进一步来区分新用户和老用户。定义如下：**消费一次为新用户，消费多次为老用户**。

### 判断用户消费次数

![](img/cc82808c798261ee070ef165fde3c97f_69.png)

![](img/cc82808c798261ee070ef165fde3c97f_71.png)

![](img/cc82808c798261ee070ef165fde3c97f_73.png)

判断用户是新用户还是老用户，关键在于比较其首次消费时间与末次消费时间。如果两者相同，则说明该用户只消费了一次。

![](img/cc82808c798261ee070ef165fde3c97f_75.png)

![](img/cc82808c798261ee070ef165fde3c97f_77.png)

以下是计算每个用户首次和末次消费时间的代码：
```python
user_purchase_times = df.groupby('user_id')['order_dt'].agg(['min', 'max'])
```

![](img/cc82808c798261ee070ef165fde3c97f_79.png)

`agg`函数用于对分组后的结果进行多种指定形式的聚合运算。

![](img/cc82808c798261ee070ef165fde3c97f_81.png)

![](img/cc82808c798261ee070ef165fde3c97f_82.png)

![](img/cc82808c798261ee070ef165fde3c97f_84.png)

### 计算新老用户人数与占比

![](img/cc82808c798261ee070ef165fde3c97f_86.png)

![](img/cc82808c798261ee070ef165fde3c97f_87.png)

![](img/cc82808c798261ee070ef165fde3c97f_88.png)

根据上一步得到的`DataFrame`，我们可以通过比较‘min’和‘max’列是否相等来判断用户类型。

![](img/cc82808c798261ee070ef165fde3c97f_89.png)

![](img/cc82808c798261ee070ef165fde3c97f_90.png)

![](img/cc82808c798261ee070ef165fde3c97f_92.png)

以下是判断并统计新老用户数量的代码：
```python
is_new_user = (user_purchase_times['min'] == user_purchase_times['max'])
user_type_counts = is_new_user.value_counts()
# 新用户占比 = 新用户数量 / 总用户数量
new_user_ratio = user_type_counts[True] / user_type_counts.sum()
```

## 基于RFM模型的用户分层

![](img/cc82808c798261ee070ef165fde3c97f_94.png)

![](img/cc82808c798261ee070ef165fde3c97f_96.png)

最后，我们将根据RFM模型对用户进行价值分层。RFM模型包含三个核心指标：
*   **R（Recency）**：客户最近一次消费的时间间隔。**值越小越好**。
*   **F（Frequency）**：客户购买商品的总数量。**值越大越好**。
*   **M（Monetary）**：客户消费的总金额。**值越大越好**。

![](img/cc82808c798261ee070ef165fde3c97f_98.png)

![](img/cc82808c798261ee070ef165fde3c97f_99.png)

![](img/cc82808c798261ee070ef165fde3c97f_100.png)

![](img/cc82808c798261ee070ef165fde3c97f_102.png)

### 计算RFM指标

![](img/cc82808c798261ee070ef165fde3c97f_104.png)

![](img/cc82808c798261ee070ef165fde3c97f_106.png)

首先，我们需要为每个用户计算出R、F、M这三个指标的值。

![](img/cc82808c798261ee070ef165fde3c97f_108.png)

![](img/cc82808c798261ee070ef165fde3c97f_109.png)

![](img/cc82808c798261ee070ef165fde3c97f_111.png)

以下是使用数据透视表一次性计算F、M和最近消费时间的代码：
```python
rfm_df = df.pivot_table(
    index='user_id',
    values=['order_products', 'order_amount', 'order_dt'],
    aggfunc={'order_products': 'sum',  # F: 总购买量
             'order_amount': 'sum',    # M: 总消费金额
             'order_dt': 'max'}        # 最近一次消费时间
)
```

![](img/cc82808c798261ee070ef165fde3c97f_113.png)

![](img/cc82808c798261ee070ef165fde3c97f_115.png)

接着，我们计算R值（最近一次消费时间间隔）。假设数据集中最新的日期为“今天”，R值即为用户末次消费日期与“今天”的差值。

![](img/cc82808c798261ee070ef165fde3c97f_117.png)

![](img/cc82808c798261ee070ef165fde3c97f_118.png)

![](img/cc82808c798261ee070ef165fde3c97f_119.png)

![](img/cc82808c798261ee070ef165fde3c97f_120.png)

以下是计算R值的代码：
```python
max_date = df['order_dt'].max()
# 计算每个用户的末次消费时间
last_purchase = df.groupby('user_id')['order_dt'].max()
# 计算时间间隔（天数）
rfm_df['R'] = (max_date - last_purchase).dt.days
```

![](img/cc82808c798261ee070ef165fde3c97f_122.png)

![](img/cc82808c798261ee070ef165fde3c97f_123.png)

最后，重命名列以符合RFM命名规范。
```python
rfm_df.columns = ['F', 'M', 'R']
```

![](img/cc82808c798261ee070ef165fde3c97f_125.png)

### 基于RFM进行用户分层

![](img/cc82808c798261ee070ef165fde3c97f_127.png)

![](img/cc82808c798261ee070ef165fde3c97f_128.png)

![](img/cc82808c798261ee070ef165fde3c97f_129.png)

我们使用一个固定的算法，根据每个用户的R、F、M值与其平均值的比较结果，将其划分到不同的客户类别中（如重要价值客户、重要发展客户等）。

![](img/cc82808c798261ee070ef165fde3c97f_131.png)

![](img/cc82808c798261ee070ef165fde3c97f_133.png)

![](img/cc82808c798261ee070ef165fde3c97f_134.png)

![](img/cc82808c798261ee070ef165fde3c97f_135.png)

以下是进行用户分层的封装函数：
```python
def rfm_classify(x):
    # x 是包含 R, F, M 三个值的序列
    level = x.map(lambda x: '1' if x > 0 else '0')
    label = level['R'] + level['F'] + level['M']
    d = {
        '111': '重要价值客户',
        '011': '重要保持客户',
        '101': '重要发展客户',
        '001': '重要挽留客户',
        '110': '一般价值客户',
        '010': '一般保持客户',
        '100': '一般发展客户',
        '000': '一般挽留客户'
    }
    result = d.get(label, '未知')
    return result

![](img/cc82808c798261ee070ef165fde3c97f_137.png)

![](img/cc82808c798261ee070ef165fde3c97f_139.png)

# 应用分类函数
# 首先将数据标准化（减去均值），然后对每一行应用分类函数
rfm_df['label'] = (rfm_df - rfm_df.mean()).apply(rfm_classify, axis=1)
```

![](img/cc82808c798261ee070ef165fde3c97f_141.png)

![](img/cc82808c798261ee070ef165fde3c97f_142.png)

`apply`函数是`DataFrame`的运算工具，可以将其传入的函数应用到数据的每一行或每一列上。

![](img/cc82808c798261ee070ef165fde3c97f_143.png)

---

![](img/cc82808c798261ee070ef165fde3c97f_145.png)

![](img/cc82808c798261ee070ef165fde3c97f_146.png)

![](img/cc82808c798261ee070ef165fde3c97f_147.png)

![](img/cc82808c798261ee070ef165fde3c97f_148.png)

![](img/cc82808c798261ee070ef165fde3c97f_150.png)

本节课中我们一起学习了用户消费行为分析的核心方法。我们首先通过分组聚合分析了用户的首次和末次消费时间分布。然后，通过比较用户的首次与末次消费时间，计算了新老用户的占比。最后，我们深入应用了RFM模型，计算了每个用户的R、F、M指标，并利用一个固定算法成功实现了用户的价值分层。这些分析为理解用户行为、制定运营策略提供了坚实的数据基础。