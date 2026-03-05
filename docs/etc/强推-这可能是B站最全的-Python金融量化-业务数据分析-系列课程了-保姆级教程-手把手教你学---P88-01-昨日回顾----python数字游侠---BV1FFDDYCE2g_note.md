# Python金融量化+业务数据分析：P88：01 昨日回顾

在本节课中，我们将回顾前两日课程的核心内容，主要包括SQL窗口函数和统计学基础知识。我们将逐一梳理关键概念，并通过示例帮助大家巩固理解。

## SQL窗口函数回顾

![](img/7e12d12dedf70517728dad9bc13755a1_1.png)

上一节我们介绍了SQL的基本查询与增删改查。本节中，我们来看看SQL中一个强大的功能——窗口函数。

窗口函数的语法结构如下：
```sql
函数名(字段) OVER (PARTITION BY 分组字段 ORDER BY 排序字段)
```
其中，`PARTITION BY` 用于指定分组依据，`ORDER BY` 用于指定排序依据。

### 窗口函数与聚合函数的区别

以下是窗口函数与聚合函数的核心区别。聚合函数（如SUM、COUNT）会对分组数据进行汇总计算，并生成一个新的汇总表结构，原始数据行数会减少。

例如，有一个员工表 `employees`：

| id | department | name |
| :--- | :--- | :--- |
| 1 | 教学部 | 佩奇 |
| 2 | 教学部 | Elsa |
| 3 | 销售部 | 贝贝 |
| 4 | 销售部 | 琪琪 |

![](img/7e12d12dedf70517728dad9bc13755a1_3.png)

使用聚合函数 `COUNT` 计算每个部门的人数：
```sql
SELECT department, COUNT(*) AS number
FROM employees
GROUP BY department;
```
结果会合并重复部门，生成两行数据：

| department | number |
| :--- | :--- |
| 教学部 | 2 |
| 销售部 | 2 |

而窗口函数则会在保留原表所有数据行的基础上，新增一列计算结果。例如：
```sql
SELECT id, department, name,
       COUNT(*) OVER (PARTITION BY department) AS number
FROM employees;
```
结果会在原表旁新增一列，显示该员工所在部门的总人数：

| id | department | name | number |
| :--- | :--- | :--- | :--- |
| 1 | 教学部 | 佩奇 | 2 |
| 2 | 教学部 | Elsa | 2 |
| 3 | 销售部 | 贝贝 | 2 |
| 4 | 销售部 | 琪琪 | 2 |

![](img/7e12d12dedf70517728dad9bc13755a1_5.png)

### 排名函数详解

窗口函数在排名场景中应用广泛。以下是四种主要的排名函数及其区别。

假设有一个成绩表 `scores`：

| id | score |
| :--- | :--- |
| 1 | 100 |
| 2 | 100 |
| 3 | 99 |
| 4 | 98 |
| 5 | 90 |

使用不同排名函数的结果如下：

```sql
SELECT id, score,
       ROW_NUMBER() OVER (ORDER BY score DESC) AS row_number,
       RANK() OVER (ORDER BY score DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY score DESC) AS dense_rank,
       NTILE(2) OVER (ORDER BY score DESC) AS ntile
FROM scores;
```

![](img/7e12d12dedf70517728dad9bc13755a1_7.png)

| id | score | row_number | rank | dense_rank | ntile |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 100 | 1 | 1 | 1 | 1 |
| 2 | 100 | 2 | 1 | 1 | 1 |
| 3 | 99 | 3 | 3 | 2 | 1 |
| 4 | 98 | 4 | 4 | 3 | 2 |
| 5 | 90 | 5 | 5 | 4 | 2 |

*   **ROW_NUMBER**： 单纯按顺序编号，不考虑并列。
*   **RANK**： 标准排名，并列会占用名次，导致后续名次跳跃。
*   **DENSE_RANK**： 紧密排名，并列不占用名次，名次连续。
*   **NTILE(n)**： 将数据平均分配到n个桶中，并返回桶编号。

### 其他窗口函数：LEAD, LAG, FIRST_VALUE, LAST_VALUE

![](img/7e12d12dedf70517728dad9bc13755a1_9.png)

接下来我们看看用于访问行内其他数据的窗口函数。

![](img/7e12d12dedf70517728dad9bc13755a1_11.png)

**LEAD 和 LAG 函数** 用于获取当前行之前或之后某行的值，常用于计算差值或变化趋势。

*   `LEAD(column, n)`： 获取当前行**向下**第n行的值。
*   `LAG(column, n)`： 获取当前行**向上**第n行的值。

![](img/7e12d12dedf70517728dad9bc13755a1_13.png)

例如，计算相邻时间点的差值：
```sql
SELECT time, value,
       LAG(value, 1) OVER (ORDER BY time) AS prev_value,
       value - LAG(value, 1) OVER (ORDER BY time) AS diff
FROM time_series_data;
```

![](img/7e12d12dedf70517728dad9bc13755a1_15.png)

**FIRST_VALUE 和 LAST_VALUE 函数** 用于获取窗口内第一个或最后一个值。使用时通常需要配合 `ROWS BETWEEN` 子句来明确窗口范围。

默认窗口范围是 `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`，即从分区第一行到当前行。若要获取整个分区的最后一个值，需将范围设置为整个分区：
```sql
SELECT column,
       LAST_VALUE(column) OVER (
           PARTITION BY group_column
           ORDER BY order_column
           ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) AS last_val_in_group
FROM table_name;
```
其中 `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` 将窗口范围定义为从分区开始到结束的所有行。

## 统计学基础回顾

在掌握了数据处理工具后，我们需要理解数据背后的规律。本节我们来回顾统计学的基础概念。

### 描述统计

![](img/7e12d12dedf70517728dad9bc13755a1_17.png)

描述统计旨在对数据整体进行概括性描述，主要包括三个方面：

1.  **集中趋势**： 描述数据的中心位置。
    *   均数 (Mean)： 所有数据的平均值。
    *   中位数 (Median)： 将数据排序后位于中间的值。
    *   众数 (Mode)： 出现频率最高的值。

2.  **离散趋势**： 描述数据的波动或分散程度。
    *   方差 (Variance)： 各数据与均值之差的平方的平均数。公式为 $\sigma^2 = \frac{\sum (x_i - \mu)^2}{N}$。
    *   标准差 (Standard Deviation)： 方差的平方根，单位与原始数据一致。
    *   四分位距 (IQR)： 第三四分位数与第一四分位数之差，反映中间50%数据的范围。
    *   变异系数 (CV)： 标准差与均值的比值，用于比较不同尺度数据的离散程度。

3.  **分布形态**：
    *   偏度 (Skewness)： 描述数据分布不对称的方向和程度。
    *   峰度 (Kurtosis)： 描述数据分布峰态的陡缓程度。

### 推断统计基础概念

推断统计是通过样本数据来推断总体特征的统计方法。以下是其核心概念：

*   **变量**：
    *   分类变量： 表示类别，分为有序（如教育程度）和无序（如性别）。
    *   数值型变量： 表示数量，分为连续（如身高、体重）和离散（如人数）。
*   **概率**： 度量随机事件发生的可能性大小，基于大量重复实验的规律得出。
*   **小概率事件**： 在一次随机抽样中，发生概率小于等于0.05的事件。它是假设检验的重要判断依据。
*   **随机变量**： 将随机事件的结果数量化，以便于数学处理和分析。
*   **总体与样本**：
    *   总体： 基于研究目的的所有观测对象的集合。
    *   样本： 从总体中随机抽取的一部分个体的集合。
*   **总体参数与统计量**：
    *   总体参数： 刻画总体特征的指标，如总体均值 $\mu$、总体比例 $\pi$。
    *   统计量： 刻画样本特征的指标，如样本均值 $\bar{x}$、样本比例 $p$。
*   **抽样误差**： 由于随机抽样，样本统计量与总体参数之间的差异。这种误差不可避免，但可以估计和控制。

## 总结

本节课中我们一起回顾了SQL窗口函数和统计学基础知识两大模块。

在SQL部分，我们重点区分了窗口函数与聚合函数的本质不同，详细讲解了ROW_NUMBER、RANK、DENSE_RANK、NTILE等排名函数，以及LEAD、LAG、FIRST_VALUE、LAST_VALUE等用于跨行计算和取值的函数，并强调了`ROWS BETWEEN`子句在定义窗口范围时的关键作用。

在统计学部分，我们梳理了描述统计中集中趋势、离散趋势和分布形态的常用指标，并建立了推断统计的基本概念框架，包括变量类型、概率、总体与样本、参数与统计量等核心思想，为后续学习统计推断方法打下坚实基础。

![](img/7e12d12dedf70517728dad9bc13755a1_19.png)

理解这些概念是进行有效数据分析和金融量化建模的重要前提。