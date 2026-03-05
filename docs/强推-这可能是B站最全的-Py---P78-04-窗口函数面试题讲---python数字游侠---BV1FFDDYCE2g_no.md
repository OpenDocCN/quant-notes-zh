# Python金融量化+业务数据分析：P78：04 窗口函数面试题讲解 🧮

![](img/aac0eef5c7ac41de31d176d2afbb0dad_1.png)

在本节课中，我们将学习如何利用SQL窗口函数解决一个实际的数据处理问题：从每日多次更新的商品数据中，筛选出每天最新的那条记录。我们将通过一个具体的面试题来讲解实现思路和步骤。

## 题目背景与数据理解

首先，我们来看一下题目中数据表的结构。数据包含以下几个核心字段：
*   **item_id**：商品编号。
*   **shop_id**：店铺编号。
*   **insert_time**：记录插入或更新时间，精确到秒。
*   **price**：商品价格。

通过查询特定商品的数据，我们可以观察到一种现象：**同一个商品在同一天内可能存在多条更新记录**。例如，某商品在一天中既有凌晨（00:00）的更新，也有深夜（23:00）的更新。

我们的目标是：**对于每个商品，在同一天内，只保留时间最晚（即`insert_time`最大）的那一条数据，过滤掉同一天内较早的记录**。

## 核心解决思路 🎯

上一节我们理解了数据特征和需求，本节中我们来看看如何利用窗口函数实现数据筛选。核心思路分为两步：
1.  在按“商品”和“日期”分组的数据内部，计算出每条记录对应的“组内最大时间”。
2.  将每条记录的原始时间与组内最大时间进行比较，只保留两者相等的记录。

以下是实现此需求的关键步骤。

![](img/aac0eef5c7ac41de31d176d2afbb0dad_3.png)

### 步骤一：提取日期字段

![](img/aac0eef5c7ac41de31d176d2afbb0dad_5.png)

原始数据中的`insert_time`是精确到秒的时间戳。为了按“天”进行分组，我们需要从中提取出日期部分（年-月-日）。

![](img/aac0eef5c7ac41de31d176d2afbb0dad_7.png)

![](img/aac0eef5c7ac41de31d176d2afbb0dad_9.png)

我们通过以下SQL语句新增一个日期字段并更新数据：
```sql
-- 1. 添加新字段
ALTER TABLE taobao_data ADD insert_date DATE;

![](img/aac0eef5c7ac41de31d176d2afbb0dad_11.png)

-- 2. 更新新字段，从insert_time中提取日期
UPDATE taobao_data SET insert_date = DATE_FORMAT(insert_time, ‘%Y-%m-%d’);
```
执行后，表中将新增`insert_date`字段，其值为每条记录对应的日期。

### 步骤二：使用窗口函数计算组内最大时间

![](img/aac0eef5c7ac41de31d176d2afbb0dad_13.png)

现在，我们需要在由`item_id`和`insert_date`定义的每个分组内，找出最大的`insert_time`。这里可以使用`LAST_VALUE()`窗口函数。

以下是计算逻辑：
```sql
SELECT
    *,
    LAST_VALUE(insert_time) OVER (
        PARTITION BY item_id, insert_date -- 按商品和日期分组
        ORDER BY insert_time ASC -- 按时间升序排列
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING -- 指定窗口范围为整个分组
    ) AS max_time -- 将此列命名为max_time
FROM taobao_data;
```
**代码解释**：
*   `PARTITION BY item_id, insert_date`：这确保了计算在每个商品-日期的组合内独立进行。
*   `ORDER BY insert_time ASC`：将组内数据按时间从早到晚排序。
*   `ROWS BETWEEN ... AND UNBOUNDED FOLLOWING`：这个子句将窗口范围定义为从分组的**第一行到最后一行**。这是关键，它确保`LAST_VALUE()`取到的是整个分组排序后的最后一个值（即最大时间），而不是默认的“到当前行为止的最后一个值”。
*   执行后，结果集中每一行都会多出一个`max_time`列，其值就是该行所属商品在对应日期下的最晚更新时间。

![](img/aac0eef5c7ac41de31d176d2afbb0dad_15.png)

### 步骤三：过滤数据

![](img/aac0eef5c7ac41de31d176d2afbb0dad_17.png)

我们已经得到了每条记录及其所属分组的最大时间。最后一步是进行过滤，只保留那些`insert_time`等于`max_time`的记录，这些就是每天最新的记录。

我们将上一步的查询作为子查询，然后进行过滤：
```sql
SELECT *
FROM (
    -- 此为步骤二的查询，作为子查询
    SELECT
        *,
        LAST_VALUE(insert_time) OVER (
            PARTITION BY item_id, insert_date
            ORDER BY insert_time ASC
            ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
        ) AS max_time
    FROM taobao_data
) AS subquery
WHERE insert_time = max_time; -- 过滤条件：只保留时间等于组内最大时间的行
```
执行此查询后，得到的结果集即为每个商品每天的最新一条数据。

## 其他实现方案 💡

除了`LAST_VALUE()`，我们还可以用其他窗口函数达到相同目的，思路类似：先计算组内标识，再过滤。

![](img/aac0eef5c7ac41de31d176d2afbb0dad_19.png)

以下是两种替代方法的核心公式：

**1. 使用 MAX() 函数**
直接计算组内时间的最大值，然后过滤。
```sql
SELECT *
FROM (
    SELECT
        *,
        MAX(insert_time) OVER (PARTITION BY item_id, insert_date) AS max_time
    FROM taobao_data
) AS t
WHERE insert_time = max_time;
```

**2. 使用 RANK() 函数**
为组内数据按时间排序并编号，然后取排名第一的记录（需降序排列）。
```sql
SELECT *
FROM (
    SELECT
        *,
        RANK() OVER (PARTITION BY item_id, insert_date ORDER BY insert_time DESC) AS time_rank
    FROM taobao_data
) AS t
WHERE time_rank = 1; -- 取排名为1的记录，即最晚的时间
```

## 总结与思考

![](img/aac0eef5c7ac41de31d176d2afbb0dad_21.png)

本节课中我们一起学习了如何运用SQL窗口函数解决“保留每日最新记录”的实际问题。我们掌握了核心步骤：提取日期、使用`LAST_VALUE()`（或`MAX()`、`RANK()`）计算组内极值、最后通过条件过滤得到结果。

![](img/aac0eef5c7ac41de31d176d2afbb0dad_23.png)

![](img/aac0eef5c7ac41de31d176d2afbb0dad_25.png)

![](img/aac0eef5c7ac41de31d176d2afbb0dad_27.png)

这个案例很好地体现了窗口函数在数据清洗和分析中的价值，它允许我们在不聚合数据的前提下，进行复杂的组内计算和比较。请尝试用不同的窗口函数实现本题，并思考如何将此类逻辑应用于更广泛的数据处理场景中。