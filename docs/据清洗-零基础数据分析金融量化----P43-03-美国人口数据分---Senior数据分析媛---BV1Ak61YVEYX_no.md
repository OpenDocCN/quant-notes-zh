# 数据分析+金融量化+数据清洗：P43：03 美国人口数据分析-02

## 概述
在本节课中，我们将继续处理美国人口数据。我们将学习如何合并面积数据表，处理数据缺失值，并最终计算各州的人口密度。课程将涵盖数据合并、条件筛选、索引设置以及使用`query`方法进行数据查询等核心操作。

---

## 合并面积数据

上一节我们成功将人口数据与州名缩写表对应起来。本节中，我们来看看如何将面积数据合并进来，以便后续计算人口密度。

目前，我们的`pop_app`表包含了人口信息，但缺少各州的面积数据。面积数据存储在另一个名为`areas`的DataFrame中，其结构包含州名（全称）和对应的面积。

首先，我们需要确认两张表的数据量是否匹配。

以下是检查两张表中唯一州数量的代码：
```python
# 检查面积表中的唯一州数量
areas['state'].unique().size
# 检查人口表中的唯一州数量
pop_app['state'].unique().size
```
检查发现，`areas`表有52个州，而`pop_app`表有53个州。这意味着在合并时，我们需要决定以哪张表为准。通常，我们会选择保留数据更全的表，这里我们使用左合并（以`pop_app`为准）。

以下是执行左合并的代码：
```python
total = pd.merge(pop_app, areas, how='left')
```
合并后，新表`total`包含了人口、州名和面积信息。

![](img/b7a70892af05c238a61092211fd9440a_1.png)

![](img/b7a70892af05c238a61092211fd9440a_3.png)

---

## 处理缺失值

![](img/b7a70892af05c238a61092211fd9440a_5.png)

合并完成后，我们需要检查新表中是否存在缺失值。

![](img/b7a70892af05c238a61092211fd9440a_7.png)

![](img/b7a70892af05c238a61092211fd9440a_8.png)

以下是检查缺失值的代码：
```python
total.isna().any()
```
检查结果显示，`area`（面积）列存在空值。我们需要找出是哪些州的面积数据缺失。

以下是查找面积缺失州的方法：
```python
# 找出面积为空值的行
total[total['area'].isna()]
```
我们发现，缺失面积数据的行对应的`state`是“USA”，即美国全国的数据。由于我们分析的是各州数据，且全国面积数据在此上下文中不适用，因此决定删除这一行。

以下是删除缺失值行的两种方法：
1.  **删除空值行**：`total.dropna(inplace=True)`
2.  **保留非空行**（本例采用此方法）：
```python
total = total[total.notna().all(axis=1)]
```
删除后，再次检查确认已无缺失值：
```python
total.isna().any()
```

---

## 计算人口密度

数据准备就绪后，现在可以计算人口密度了。人口密度的计算公式为：
**人口密度 = 人口 / 面积**

我们的数据包含多个年份（`year`）和年龄分组（`ages`，如“total”总人口和“under18”未成年人口）。例如，我们想计算2010年各州的总人口密度。

这需要两步：
1.  筛选出`year`为2010且`ages`为“total”的数据行。
2.  用筛选后数据中的人口列除以面积列。

以下是实现步骤1的两种条件筛选方法：

**方法一：使用布尔索引**
```python
condition = (total['year'] == 2010) & (total['ages'] == 'total')
data_2010 = total[condition]
```

**方法二：使用`query`方法（更简洁）**
```python
data_2010 = total.query("year == 2010 and ages == 'total'")
```

接着，计算密度并排序，找出人口密度最高的州：
```python
# 计算密度
density_2010 = data_2010['population'] / data_2010['area']
# 按密度降序排序
density_2010_sorted = density_2010.sort_values(ascending=False)
# 查看前五个州
density_2010_sorted.head()
```
但此时的结果只显示密度值，我们不知道它们分别对应哪个州。

---

## 设置索引以保留州名信息

为了使结果更清晰，我们可以将州名（`state`）设置为`data_2010`表的索引。这样，在后续计算中，州名信息就会与数据绑定。

以下是设置索引的代码：
```python
data_2010 = data_2010.set_index('state')
```
设置索引后，重新计算密度。此时，得到的`Series`将带有州名索引，排序后便能清晰地看到每个州对应的密度。
```python
density_2010 = data_2010['population'] / data_2010['area']
density_2010_sorted = density_2010.sort_values(ascending=False)
print(density_2010_sorted.head())
```

---

## 扩展练习：计算其他数据

我们可以用同样的方法计算其他数据，例如2012年未成年人口（`under18`）的密度。

以下是计算步骤：
```python
# 查询2012年未成年人口数据
data_2012 = total.query("year == 2012 and ages == 'under18'")
# 设置州名为索引
data_2012 = data_2012.set_index('state')
# 计算密度并排序
density_2012 = data_2012['population'] / data_2012['area']
density_2012_sorted = density_2012.sort_values(ascending=False)
print(density_2012_sorted.head())
```

---

## 总结

本节课中我们一起学习了：
1.  **数据合并**：使用`pd.merge`函数进行表的左合并，整合所需信息。
2.  **缺失值处理**：识别并删除了不相关的缺失数据行（全国面积数据）。
3.  **条件筛选**：掌握了使用布尔条件组合和`query`方法筛选特定数据子集。
4.  **索引设置**：使用`set_index`方法将某一列设置为索引，便于数据标识和后续操作。
5.  **计算与排序**：应用公式进行数据计算，并使用`sort_values`方法对结果进行排序分析。

![](img/b7a70892af05c238a61092211fd9440a_10.png)

通过这些步骤，我们完成了从原始数据到计算出各州人口密度的完整流程，这是数据分析中常见的数据清洗、整合与计算过程。