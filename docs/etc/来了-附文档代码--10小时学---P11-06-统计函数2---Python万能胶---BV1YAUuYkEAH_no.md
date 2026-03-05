# Python数据分析：P11：统计函数2

![](img/3b09b800cd4f76f7a1e5451c62a07beb_1.png)

![](img/3b09b800cd4f76f7a1e5451c62a07beb_2.png)

在本节课中，我们将学习条件统计函数，包括条件求和与条件计数。这些函数能帮助我们在数据分析时，根据特定条件筛选数据并进行计算。

上一节我们介绍了基础的统计函数，如求和、计数、平均值等。本节中我们来看看如何在计算中加入条件。

## 条件求和：SUMIF函数

当我们需要对满足特定条件的数据进行求和时，可以使用 `SUMIF` 函数。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_4.png)

![](img/3b09b800cd4f76f7a1e5451c62a07beb_5.png)

**函数公式**：
`=SUMIF(range, criteria, [sum_range])`

以下是 `SUMIF` 函数的组成部分：
*   **range**： 条件区域，即用于条件判断的单元格区域。
*   **criteria**： 条件，其形式可以是数字、表达式或文本。
*   **sum_range**： 求和区域，即需要求和的单元格区域。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_7.png)

**应用示例**：计算员工级别为“A类”的销售额总和。
1.  条件区域 `range` 选择“员工级别”列。
2.  条件 `criteria` 输入 `"A类"`。
3.  求和区域 `sum_range` 选择“销售额”列。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_8.png)

执行函数后，即可得到A类员工的销售额总和。

## 多条件求和：SUMIFS函数

当求和条件不止一个时，我们需要使用 `SUMIFS` 函数。

**函数公式**：
`=SUMIFS(sum_range, criteria_range1, criteria1, [criteria_range2, criteria2], ...)`

以下是 `SUMIFS` 函数的组成部分：
*   **sum_range**： 求和区域，即需要求和的单元格区域。
*   **criteria_range1**： 第一个条件区域。
*   **criteria1**： 与第一个条件区域关联的条件。
*   **criteria_range2, criteria2, ...**： 附加的条件区域及其条件（可选）。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_10.png)

**应用示例**：计算工作年限大于5年且员工级别为“A类”的销售额总和。
1.  求和区域 `sum_range` 选择“销售额”列。
2.  第一个条件区域 `criteria_range1` 选择“工作年限”列，条件 `criteria1` 输入 `">5"`。
3.  第二个条件区域 `criteria_range2` 选择“员工级别”列，条件 `criteria2` 输入 `"A类"`。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_12.png)

![](img/3b09b800cd4f76f7a1e5451c62a07beb_13.png)

执行函数后，即可得到同时满足两个条件的销售额总和。

## 条件计数：COUNTIF函数

![](img/3b09b800cd4f76f7a1e5451c62a07beb_15.png)

如果我们需要统计满足特定条件的单元格数量，可以使用 `COUNTIF` 函数。

**函数公式**：
`=COUNTIF(range, criteria)`

![](img/3b09b800cd4f76f7a1e5451c62a07beb_17.png)

以下是 `COUNTIF` 函数的组成部分：
*   **range**： 计数的区域。
*   **criteria**： 计数的条件。

**应用示例**：统计员工级别为“A类”的员工数量。
1.  区域 `range` 选择“员工级别”列。
2.  条件 `criteria` 输入 `"A类"`。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_19.png)

执行函数后，即可得到A类员工的数量。

## 多条件计数：COUNTIFS函数

当计数条件有多个时，我们需要使用 `COUNTIFS` 函数。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_21.png)

![](img/3b09b800cd4f76f7a1e5451c62a07beb_22.png)

**函数公式**：
`=COUNTIFS(criteria_range1, criteria1, [criteria_range2, criteria2], ...)`

![](img/3b09b800cd4f76f7a1e5451c62a07beb_23.png)

以下是 `COUNTIFS` 函数的组成部分：
*   **criteria_range1**： 第一个条件区域。
*   **criteria1**： 与第一个条件区域关联的条件。
*   **criteria_range2, criteria2, ...**： 附加的条件区域及其条件（可选）。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_25.png)

![](img/3b09b800cd4f76f7a1e5451c62a07beb_27.png)

**应用示例**：统计工作年限大于等于10年且销售额大于等于2000的员工数量。
1.  第一个条件区域 `criteria_range1` 选择“工作年限”列，条件 `criteria1` 输入 `">=10"`。
2.  第二个条件区域 `criteria_range2` 选择“销售额”列，条件 `criteria2` 输入 `">=2000"`。

执行函数后，即可得到同时满足两个条件的员工数量。

![](img/3b09b800cd4f76f7a1e5451c62a07beb_29.png)

![](img/3b09b800cd4f76f7a1e5451c62a07beb_31.png)

本节课中我们一起学习了四个重要的条件统计函数：`SUMIF`（单条件求和）、`SUMIFS`（多条件求和）、`COUNTIF`（单条件计数）和 `COUNTIFS`（多条件计数）。掌握这些函数，能极大地提升我们在数据分析中处理复杂条件计算的能力。