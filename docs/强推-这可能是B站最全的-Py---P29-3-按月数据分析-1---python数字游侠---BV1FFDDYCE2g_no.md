# Python金融量化与业务数据分析：P29：3. 按月数据分析 📊

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_1.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_3.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_5.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_7.png)

在本节课中，我们将学习如何按月对用户消费数据进行分组分析。我们将使用Pandas的`groupby`功能，计算每月的总消费金额、产品购买总量、消费总次数以及消费人数，并通过可视化图表直观地展示数据趋势。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_9.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_11.png)

---

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_13.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_15.png)

上一节我们介绍了数据的基本结构，本节中我们来看看如何按月进行分组聚合分析。原始数据中已经包含一个名为`month`的列，它记录了每条消费记录发生的月份。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_17.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_19.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_21.png)

以下是按月分析的具体需求与实现步骤：

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_23.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_25.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_27.png)

### 1. 计算每月总消费金额 💰

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_29.png)

首先，我们需要计算所有用户在每个月的总消费金额。这可以通过按`month`列分组，然后对`order_amount`列进行求和来实现。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_31.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_33.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_35.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_37.png)

**实现公式**：
```python
monthly_total_amount = df.groupby('month')['order_amount'].sum()
```

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_39.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_41.png)

我们可以使用Matplotlib将结果绘制成折线图，以观察消费金额随月份变化的趋势。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_43.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_45.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_47.png)

```python
monthly_total_amount.plot()
```

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_49.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_51.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_53.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_55.png)

### 2. 计算每月产品购买总量 📦

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_57.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_59.png)

接下来，我们计算所有用户每月购买的产品总量。这个需求与第一个类似，只是聚合的列变成了`order_product`。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_61.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_63.png)

**实现公式**：
```python
monthly_total_products = df.groupby('month')['order_product'].sum()
```

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_65.png)

同样，我们可以绘制折线图。你会发现，由于消费金额与购买数量强相关，这个趋势图与总消费金额的图几乎一致。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_67.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_69.png)

```python
monthly_total_products.plot()
```

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_71.png)

### 3. 计算每月消费总次数 🔢

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_73.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_75.png)

这个需求是统计每月发生的消费记录总次数。在原始数据中，每一行代表一次独立的消费记录。因此，我们只需按月份分组后，统计每个分组中的行数即可。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_77.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_79.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_81.png)

**实现公式**：
```python
monthly_total_transactions = df.groupby('month').size()
# 或者使用 count() 方法
monthly_total_transactions = df.groupby('month')['user_id'].count()
```

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_83.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_85.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_87.png)

### 4. 计算每月消费人数 👥

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_89.png)

最后，我们需要统计每月有多少不同的用户进行了消费。这与“消费总次数”不同，因为同一个用户在同一个月内可能消费多次。因此，我们需要在按月份分组后，对`user_id`进行去重，然后统计去重后的数量。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_91.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_93.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_95.png)

**实现公式**：
```python
monthly_unique_users = df.groupby('month')['user_id'].nunique()
```
这里使用了`.nunique()`方法，它直接返回分组后指定列的唯一值数量，是“去重后计数”的便捷操作。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_97.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_99.png)

---

本节课中我们一起学习了如何使用Pandas的`groupby`方法进行按月数据分析。我们掌握了四个核心操作：
1.  使用`.sum()`计算月度总消费金额。
2.  使用`.sum()`计算月度产品购买总量。
3.  使用`.size()`或`.count()`计算月度消费总次数。
4.  使用`.nunique()`计算月度消费人数。

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_101.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_103.png)

![](img/296dbcc2af7fdc9d5c9ff5c5f10501b5_105.png)

通过这些分析，我们可以清晰地看到业务指标随时间（月份）的变化趋势，例如识别出消费高峰期和低谷期，为业务决策提供数据支持。