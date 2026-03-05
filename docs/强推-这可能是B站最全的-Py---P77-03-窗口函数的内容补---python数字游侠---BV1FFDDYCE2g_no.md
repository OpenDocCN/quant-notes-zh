# Python金融量化+业务数据分析：P77：03 窗口函数的内容补充 📊

在本节课中，我们将深入学习SQL窗口函数中的几个关键补充知识点，包括`LAG`函数、`FIRST_VALUE`与`LAST_VALUE`函数的详细用法，以及如何通过`ROWS BETWEEN`子句控制窗口范围。我们还将通过一个累计销售额计算的实战案例，巩固对窗口函数的理解。

## 回顾`LEAD`函数

上一节我们介绍了`LEAD`函数，它用于获取当前行之后指定偏移量的行的值。其基本公式如下：
```sql
LEAD(column_name, offset, default_value) OVER (PARTITION BY ... ORDER BY ...)
```
其中，`offset`默认为1，表示向下取一行。`default_value`是当没有后续行时返回的默认值。

## 理解`LAG`函数

本节中我们来看看`LAG`函数。它与`LEAD`函数功能相反，用于获取当前行之前指定偏移量的行的值。

以下是`LAG`函数的核心概念：
*   **功能**：向上（向前）获取指定行的数据。
*   **公式**：`LAG(column_name, offset, default_value) OVER (PARTITION BY ... ORDER BY ...)`
*   **与`LEAD`的关系**：方向相反。`LEAD`是向后看，`LAG`是向前看。
*   **应用场景**：常用于计算当前行与前一行的差值。例如，用当前时间减去上一行的时间。

如果理解了`LEAD`函数，那么掌握`LAG`函数就非常容易，它们遵循相同的逻辑。

## 深入`FIRST_VALUE`与`LAST_VALUE`函数

接下来，我们探讨`FIRST_VALUE`和`LAST_VALUE`函数。它们用于获取窗口内排序后的第一个或最后一个值。

### `FIRST_VALUE`函数

`FIRST_VALUE`函数返回窗口内按指定顺序排序后的第一个值。

我们通过一个示例来理解。假设有一个学生成绩表`stu_info`，我们想找出每个学生所有科目中的最高分。

![](img/6228eb5a99352f7d9787f8a6b045f061_1.png)

![](img/6228eb5a99352f7d9787f8a6b045f061_3.png)

```sql
SELECT
    s_name,
    number,
    FIRST_VALUE(number) OVER (
        PARTITION BY s_name
        ORDER BY number DESC
    ) AS max_number
FROM stu_info
WHERE number > 0;
```
*   `PARTITION BY s_name`：按学生姓名分组。
*   `ORDER BY number DESC`：在每个分组内按成绩降序排列。
*   `FIRST_VALUE(number)`：取分组内排序后的第一个值，即最高分。

执行后，每个学生对应的所有行中，`max_number`列都会显示该学生的最高成绩。

![](img/6228eb5a99352f7d9787f8a6b045f061_5.png)

![](img/6228eb5a99352f7d9787f8a6b045f061_7.png)

### `LAST_VALUE`函数的陷阱与解决

![](img/6228eb5a99352f7d9787f8a6b045f061_9.png)

现在，如果我们想用`LAST_VALUE`函数求每个学生的最低分，直觉上可能认为只需将`FIRST`改为`LAST`，并将排序改为升序(`ASC`)。但直接这样做可能得不到预期结果。

![](img/6228eb5a99352f7d9787f8a6b045f061_11.png)

![](img/6228eb5a99352f7d9787f8a6b045f061_13.png)

```sql
-- 这可能无法得到正确的最低分
SELECT
    s_name,
    number,
    LAST_VALUE(number) OVER (
        PARTITION BY s_name
        ORDER BY number ASC
    ) AS min_number
FROM stu_info
WHERE number > 0;
```
问题在于窗口函数默认的窗口范围是`ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`。这意味着对于每一行，窗口范围是从分组的开头到**当前行**。

对于`FIRST_VALUE`，无论窗口截止到哪一行，取第一个值始终是分组开头那个值，因此不受影响。
但对于`LAST_VALUE`，当窗口截止到当前行时，它取的是当前窗口的最后一个值，也就是**当前行本身的值**。这导致结果中每一行的`min_number`都等于其自身的`number`，而非整个分组的最小值。

**解决方案**是显式地指定窗口范围，使其覆盖整个分区。

```sql
SELECT
    s_name,
    number,
    LAST_VALUE(number) OVER (
        PARTITION BY s_name
        ORDER BY number ASC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS min_number
FROM stu_info
WHERE number > 0;
```
关键修改在于`ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`。这指定了窗口范围从分组的开头到结尾，这样`LAST_VALUE`就能正确取到整个分组排序后的最后一个值（即最低分）。

**`ROWS BETWEEN`子句详解**：
*   `UNBOUNDED PRECEDING`：窗口开始于分组的首行（无前界）。
*   `UNBOUNDED FOLLOWING`：窗口结束于分组的末行（无后界）。
*   `CURRENT ROW`：窗口结束于当前行。
*   也可以使用具体数字，如`1 PRECEDING`表示当前行的前一行。

## 实战案例：计算累计销售额

最后，我们通过一个常见的业务场景——计算月度累计销售额，来综合应用窗口函数。

假设有销售表`sell`，包含`year`（年份）、`month`（月份）、`amount`（销售额）等字段。

首先，我们需要计算每个月的总销售额：

```sql
SELECT
    year,
    month,
    SUM(amount) AS monthly_amount
FROM sell
GROUP BY year, month
ORDER BY year, month;
```

![](img/6228eb5a99352f7d9787f8a6b045f061_15.png)

接着，在以上结果的基础上，使用窗口函数计算每一年内的累计销售额：

![](img/6228eb5a99352f7d9787f8a6b045f061_17.png)

```sql
SELECT
    year,
    month,
    monthly_amount,
    SUM(monthly_amount) OVER (
        PARTITION BY year
        ORDER BY month
        -- 默认即为 ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_amount
FROM ( ... ) AS monthly_sales; -- 此处嵌入上面的月度汇总查询
```
*   `PARTITION BY year`：按年份分组，保证累计计算在每年内独立进行。
*   `ORDER BY month`：按月份排序，这是累计计算的基础。
*   `SUM(monthly_amount) OVER (...)`：对`monthly_amount`进行求和。由于使用了默认窗口范围（从当年开头到当前行），因此实现了累计求和的效果。

这个案例完美展示了默认窗口范围`ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`的实用价值。

![](img/6228eb5a99352f7d9787f8a6b045f061_19.png)

## 总结

![](img/6228eb5a99352f7d9787f8a6b045f061_21.png)

本节课中我们一起学习了：
1.  **`LAG`函数**：用于获取当前行之前的数据，是`LEAD`函数的反向操作。
2.  **`FIRST_VALUE`与`LAST_VALUE`函数**：用于获取窗口内的第一个和最后一个值。特别注意`LAST_VALUE`在使用时需要根据实际情况通过`ROWS BETWEEN`子句调整窗口范围，以避免取到当前行的值。
3.  **`ROWS BETWEEN`子句**：用于精确控制窗口函数的计算范围，是处理复杂窗口计算的关键。
4.  **累计计算实战**：利用`SUM`配合窗口函数和默认的窗口范围，可以优雅地实现累计求和等业务需求。

![](img/6228eb5a99352f7d9787f8a6b045f061_23.png)

掌握这些补充知识后，你对SQL窗口函数的应用将更加得心应手，能够解决更复杂的数据分析问题。