# Python金融量化+业务数据分析：P74：06 MySQL开窗函数 📊

在本节课中，我们将要学习MySQL中一个非常强大的功能——开窗函数。开窗函数能够在不改变原始数据行数的情况下，为每一行数据执行计算，这对于解决复杂的排名、累计求和、前后行差值计算等问题非常有帮助。

上一节我们复习了数据库的基础操作，包括库、表、记录的增删改查，并重点讲解了单表与多表查询。本节中我们来看看如何利用开窗函数来解决普通SQL查询难以处理的问题。

---

## 视图简介

在深入讲解开窗函数之前，我们先简要回顾一下视图的概念，因为理解视图有助于我们理解某些查询结果的呈现方式。

视图是一个虚拟的表。它本身并不存储数据，而是基于一个或多个真实表（我们称之为“基表”）的查询结果。你可以把视图看作是一个保存起来的SQL查询语句。

**创建视图的语法如下：**
```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

例如，一个经常使用的复杂联表查询，可以保存为视图，之后直接像查询普通表一样查询这个视图，简化了操作。

**但是，通常不建议频繁创建视图，原因如下：**
*   **主要用途是查询**：视图主要用于简化查询，对其进行增删改操作通常没有意义，且可能受限。
*   **效率问题**：视图的查询效率可能不如直接查询基表，尤其是在视图定义非常复杂时。
*   **灵活性差**：如果底层基表结构或业务逻辑发生变化，需要手动更新视图的定义。

视图适用于那些查询频率极高、且涉及数据量和逻辑相对固定的场景。

---

## 开窗函数详解

当我们遇到诸如“查询每个科目成绩的前两名”、“计算每个学生的成绩与平均分的差距”这类问题时，使用普通的`GROUP BY`聚合会改变数据维度，而使用子查询又非常繁琐。这时，开窗函数就成为了理想的工具。

### 开窗函数基本语法

开窗函数的完整语法结构如下：
```
函数名([参数]) OVER (
    [PARTITION BY 列名, ...]
    [ORDER BY 列名 [ASC|DESC], ...]
)
```

*   **函数名**：可以是聚合函数（如`SUM`, `AVG`, `COUNT`），也可以是专用的窗口函数（如`ROW_NUMBER`, `RANK`, `LAG`）。
*   **OVER()**：定义窗口的范围。
*   **PARTITION BY**：类似于`GROUP BY`，用于将数据分成不同的组（窗口），函数会在每个组内独立计算。
*   **ORDER BY**：决定窗口内数据的排序顺序，对于排名类函数和累计计算至关重要。

---

### 1. 聚合开窗函数

聚合开窗函数允许我们在保留所有原始行的同时，计算出基于窗口的聚合值。

**示例1：计算每个学生的及格科目数**

以下是使用普通聚合函数和开窗函数的对比：

*   **普通聚合函数（改变行数）**
    ```sql
    SELECT s_name, COUNT(*) AS 及格科目数
    FROM stu
    WHERE number >= 60
    GROUP BY s_name;
    ```
    结果：每个学生只返回一行，显示其及格科目总数。

*   **开窗函数（保留所有行）**
    ```sql
    SELECT *,
           COUNT(*) OVER (PARTITION BY s_name) AS 及格科目数
    FROM stu
    WHERE number >= 60;
    ```
    结果：返回所有及格的成绩记录，并在每条记录旁显示该学生的总及格科目数。同一学生的这个数值是重复的。

**核心区别**：普通聚合函数会折叠行，而聚合开窗函数是在每行数据上“开一扇窗”，看到它所在分组的聚合结果。

**示例2：计算每科成绩与科目平均分的差距**

这个需求使用开窗函数非常方便，因为我们需要保留每一行原始成绩，同时知道该科目的平均分。

```sql
SELECT s_name,
       c_name,
       number AS 成绩,
       AVG(number) OVER (PARTITION BY c_name) AS 科目平均分,
       number - AVG(number) OVER (PARTITION BY c_name) AS 与平均分差距
FROM stu;
```
在这个查询中，`AVG(number) OVER (PARTITION BY c_name)`为每一行计算了它所属科目（`c_name`）的平均分，然后我们直接用当前成绩减去这个平均值。

---

### 2. 排序开窗函数

![](img/aa343bfe4fad80f8a890a9e7dc3e4169_1.png)

排序开窗函数是开窗函数中最常用、最具优势的部分，主要用于生成排名。它们通常不需要参数，但必须配合`ORDER BY`子句。

![](img/aa343bfe4fad80f8a890a9e7dc3e4169_3.png)

以下是四种主要的排序函数及其区别：

假设我们有一张成绩表，按科目分组、成绩降序排列后，使用不同函数：

```sql
SELECT s_name,
       c_name,
       number,
       ROW_NUMBER() OVER (PARTITION BY c_name ORDER BY number DESC) AS `row_number`,
       RANK() OVER (PARTITION BY c_name ORDER BY number DESC) AS `rank`,
       DENSE_RANK() OVER (PARTITION BY c_name ORDER BY number DESC) AS `dense_rank`,
       NTILE(4) OVER (PARTITION BY c_name ORDER BY number DESC) AS `ntile`
FROM stu;
```

**以下是四种排序方式的解释：**

*   **ROW_NUMBER()**：为窗口内的每一行生成一个唯一的连续序号（1, 2, 3, ...），即使值相同，序号也不同。
*   **RANK()**：排名。值相同时，排名相同，但下一个排名会跳过重复的位数（例如：1, 2, 2, 4）。
*   **DENSE_RANK()**：密集排名。值相同时排名相同，但下一个排名连续（例如：1, 2, 2, 3）。
*   **NTILE(n)**：将窗口内的数据尽可能平均地分成`n`个桶，并返回每个行所在的桶编号。

**应用场景**：要筛选每个科目的前三名，使用`DENSE_RANK() <= 3`会更合适，因为它能正确处理并列情况并给出紧密的前三名。

---

### 3. 前后行函数：LAG 和 LEAD

`LAG`和`LEAD`函数允许我们访问当前行之前（`LAG`）或之后（`LEAD`）指定偏移量的行的值。这对于计算行与行之间的差异（如时间间隔、环比增长）极其有用。

**语法：**
```sql
LAG(column, offset, default_value) OVER (...)
LEAD(column, offset, default_value) OVER (...)
```
*   `column`：要访问的列。
*   `offset`：向前或向后偏移的行数（默认为1）。
*   `default_value`：当偏移行不存在时返回的默认值（默认为NULL）。

**经典案例：识别用户作弊登录**

**需求**：统计用户登录记录表（`login_table`），如果同一用户（`uid`）在2分钟内连续登录，则视为作弊，需要统计每个用户的作弊次数。

**表结构示例：**
| uid | login_time          |
|-----|---------------------|
| 1   | 2023-10-01 12:00:00 |
| 1   | 2023-10-01 12:01:23 |
| 1   | 2023-10-01 12:03:59 |

**解决思路**：
1.  按用户分组，按登录时间排序。
2.  使用`LEAD`函数，将下一行的登录时间“拿到”当前行，形成新的一列`next_login_time`。
3.  计算当前行的`login_time`与`next_login_time`的时间差。
4.  筛选出时间差小于2分钟的记录。
5.  按用户分组计数。

**SQL实现：**
```sql
SELECT uid,
       COUNT(*) AS 作弊次数
FROM (
    SELECT uid,
           login_time,
           LEAD(login_time) OVER (PARTITION BY uid ORDER BY login_time) AS next_login_time,
           TIMESTAMPDIFF(SECOND, login_time, LEAD(login_time) OVER (PARTITION BY uid ORDER BY login_time)) AS diff_seconds
    FROM login_table
) AS t
WHERE diff_seconds <= 120 -- 2分钟 = 120秒
GROUP BY uid;
```

**关键点解析**：
`LEAD(login_time) OVER (PARTITION BY uid ORDER BY login_time)` 为每个用户的当前登录记录，获取了下一次登录的时间。这样，两列时间在同一行，计算差值就变得非常简单。`LAG`函数的逻辑与此相反，是获取上一次的值。

---

![](img/aa343bfe4fad80f8a890a9e7dc3e4169_5.png)

![](img/aa343bfe4fad80f8a890a9e7dc3e4169_7.png)

### 4. 首尾值函数：FIRST_VALUE 和 LAST_VALUE

这两个函数返回窗口内第一行（`FIRST_VALUE`）或最后一行（`LAST_VALUE`）指定列的值。

**示例：查找每个科目的最高分和最低分**
```sql
SELECT s_name,
       c_name,
       number,
       FIRST_VALUE(number) OVER (PARTITION BY c_name ORDER BY number DESC) AS 科目最高分,
       LAST_VALUE(number) OVER (PARTITION BY c_name ORDER BY number DESC ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS 科目最低分
FROM stu;
```
**注意**：`LAST_VALUE`的默认窗口范围是“从第一行到当前行”，为了得到整个分组的最后一行，通常需要指定完整的窗口框架：`ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`。

---

## 总结

本节课中我们一起学习了MySQL中强大的开窗函数（Window Function）。我们从视图的简单回顾开始，然后深入探讨了开窗函数的四大类主要应用：

1.  **聚合开窗**：在保留原数据行的基础上进行分组聚合计算（如`SUM() OVER`）。
2.  **排序开窗**：为数据生成各种类型的排名（`ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`），是解决Top-N问题的利器。
3.  **前后行函数**：访问当前行之前或之后的数据（`LAG`, `LEAD`），完美解决同行列差值计算、环比等问题。
4.  **首尾值函数**：轻松获取分组内的第一个或最后一个值（`FIRST_VALUE`, `LAST_VALUE`）。

![](img/aa343bfe4fad80f8a890a9e7dc3e4169_9.png)

开窗函数的核心思想是“定义窗口，进行计算”，它通过`OVER()`子句中的`PARTITION BY`和`ORDER BY`灵活控制计算的范围和顺序，极大地增强了SQL处理复杂分析需求的能力，是数据分析师和数据库开发人员必须掌握的技能。