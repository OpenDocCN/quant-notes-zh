# Python Excel 数据处理：P77：10：花式遍历Excel数据 📊

![](img/eeaebbb449b5fcf3056442588a08fafa_1.png)

在本节课中，我们将学习如何使用Python的`openpyxl`库来读取和遍历一个已存在的Excel文件。我们将掌握多种遍历数据的方法，包括按行、按列、指定区域遍历，并学习如何修改单元格数据。

![](img/eeaebbb449b5fcf3056442588a08fafa_3.png)

![](img/eeaebbb449b5fcf3056442588a08fafa_5.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_7.png)

## 打开与加载Excel文件

![](img/eeaebbb449b5fcf3056442588a08fafa_9.png)

![](img/eeaebbb449b5fcf3056442588a08fafa_11.png)

上一节我们介绍了如何创建新的Excel文件。本节中，我们来看看如何打开一个已存在的文件并加载其数据。

首先，我们需要使用`openpyxl.load_workbook`函数来加载一个已有的Excel工作簿。

```python
from openpyxl import load_workbook

# 加载名为“集训营报名人数.xlsx”的Excel文件
wb = load_workbook('集训营报名人数.xlsx')
```

加载成功后，`wb`对象就代表了整个Excel工作簿。

![](img/eeaebbb449b5fcf3056442588a08fafa_13.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_15.png)

## 获取与选择工作表

一个工作簿可以包含多个工作表（Sheet）。在操作数据前，我们需要确定要操作哪个工作表。

![](img/eeaebbb449b5fcf3056442588a08fafa_17.png)

以下是获取工作表信息的几种方法：

![](img/eeaebbb449b5fcf3056442588a08fafa_19.png)

![](img/eeaebbb449b5fcf3056442588a08fafa_21.png)

```python
# 1. 获取所有工作表的名称列表
print(wb.sheetnames)

# 2. 通过名称获取特定的工作表对象
sheet1 = wb['Sheet1']  # 方法一：像字典一样通过名称索引
# 或
sheet1 = wb.get_sheet_by_name('Sheet1')  # 方法二：使用get_sheet_by_name方法

print(sheet1)  # 打印工作表对象信息
```

---

![](img/eeaebbb449b5fcf3056442588a08fafa_23.png)

## 读取单个单元格的值

![](img/eeaebbb449b5fcf3056442588a08fafa_25.png)

获取到工作表对象后，我们可以读取特定单元格的数据。每个单元格都是一个`Cell`对象，要获取其存储的值，需要访问其`.value`属性。

![](img/eeaebbb449b5fcf3056442588a08fafa_27.png)

```python
# 读取B列第5行（即B5单元格）的值
cell_b5 = sheet1['B5']
print(cell_b5)          # 打印单元格对象
print(cell_b5.value)    # 打印单元格的值
```

![](img/eeaebbb449b5fcf3056442588a08fafa_29.png)

![](img/eeaebbb449b5fcf3056442588a08fafa_31.png)

---

## 读取单元格区域（切片）

我们不仅可以读取单个单元格，还可以像列表切片一样，读取一个连续的单元格区域。

![](img/eeaebbb449b5fcf3056442588a08fafa_33.png)

```python
# 读取从B6到E6这个区域的单元格
cell_range = sheet1['B6':'E6']
print(cell_range)  # 得到一个包含单元格元组的元组

![](img/eeaebbb449b5fcf3056442588a08fafa_35.png)

# 遍历这个区域内的每个单元格并打印其值
for row in cell_range:      # 外层循环每一行（本例只有一行）
    for cell in row:        # 内层循环该行的每个单元格
        print(cell.value)
```

![](img/eeaebbb449b5fcf3056442588a08fafa_37.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_39.png)

## 按行遍历整个工作表

![](img/eeaebbb449b5fcf3056442588a08fafa_41.png)

最常见的需求是逐行遍历工作表中的所有数据。

![](img/eeaebbb449b5fcf3056442588a08fafa_43.png)

以下是按行遍历并打印所有数据的标准方法：

![](img/eeaebbb449b5fcf3056442588a08fafa_45.png)

```python
# 遍历工作表的每一行
for row in sheet1.iter_rows():
    # 遍历当前行的每一列（单元格）
    for cell in row:
        print(cell.value, end='\t')  # 使用制表符分隔，不换行
    print()  # 每行结束后换行
```

`iter_rows()`方法默认会遍历所有包含数据的行和列。

![](img/eeaebbb449b5fcf3056442588a08fafa_47.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_49.png)

## 按行遍历指定区域

![](img/eeaebbb449b5fcf3056442588a08fafa_51.png)

有时我们只需要处理数据表的某一部分，而不是整个工作表。`iter_rows`方法允许我们指定起始行、结束行、起始列和结束列。

例如，我们想遍历从第4行到第21行，共5列的数据区域：

![](img/eeaebbb449b5fcf3056442588a08fafa_53.png)

```python
# 遍历指定区域：最小行=4，最大行=21，最小列=1，最大列=5
for row in sheet1.iter_rows(min_row=4, max_row=21, max_col=5):
    for cell in row:
        print(cell.value, end='\t')
    print()
```
参数说明：
*   `min_row`：起始行号
*   `max_row`：结束行号
*   `min_col`：起始列号（默认为1）
*   `max_col`：结束列号

![](img/eeaebbb449b5fcf3056442588a08fafa_55.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_57.png)

## 按列遍历整个工作表

![](img/eeaebbb449b5fcf3056442588a08fafa_59.png)

除了按行遍历，我们也可以按列来遍历数据。这在需要集中处理某一列数据时非常有用。

![](img/eeaebbb449b5fcf3056442588a08fafa_61.png)

```python
# 遍历工作表的每一列
for column in sheet1.iter_cols():
    # 遍历当前列的每一行（单元格）
    for cell in column:
        print(cell.value, end='\t')
    print()
```
`iter_cols()`方法会先遍历第一列的所有行，然后是第二列，依此类推。

![](img/eeaebbb449b5fcf3056442588a08fafa_63.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_65.png)

## 按列遍历指定区域

![](img/eeaebbb449b5fcf3056442588a08fafa_67.png)

与按行遍历类似，我们也可以指定按列遍历的区域。

例如，我们只想遍历C列和D列（即第3列和第4列）的数据：

![](img/eeaebbb449b5fcf3056442588a08fafa_69.png)

```python
# 遍历指定列区域：最小列=3，最大列=4
for column in sheet1.iter_cols(min_col=3, max_col=4):
    for cell in column:
        print(cell.value, end='\t')
    print()
```

![](img/eeaebbb449b5fcf3056442588a08fafa_71.png)

---

![](img/eeaebbb449b5fcf3056442588a08fafa_73.png)

![](img/eeaebbb449b5fcf3056442588a08fafa_75.png)

## 修改单元格数据并保存

![](img/eeaebbb449b5fcf3056442588a08fafa_77.png)

遍历和读取数据后，我们也可以修改单元格的内容，并将更改保存到文件。

![](img/eeaebbb449b5fcf3056442588a08fafa_79.png)

修改数据分为两步：
1.  对单元格的`.value`属性赋予新值。
2.  调用工作簿的`.save()`方法保存。

```python
# 1. 修改B5单元格的值
sheet1['B5'].value = '新数据 - 金角大王'

![](img/eeaebbb449b5fcf3056442588a08fafa_81.png)

# 2. 保存工作簿
# 保存为新文件
wb.save('集训营报名人数_修改后.xlsx')
# 或覆盖原文件（谨慎操作）
# wb.save('集训营报名人数.xlsx')
```
**注意**：`save`方法会将内存中的所有更改写入文件。如果指定原文件名，则会覆盖原文件。

![](img/eeaebbb449b5fcf3056442588a08fafa_83.png)

---

## 总结

![](img/eeaebbb449b5fcf3056442588a08fafa_85.png)

![](img/eeaebbb449b5fcf3056442588a08fafa_87.png)

本节课中我们一起学习了使用`openpyxl`库进行Excel数据遍历的核心操作：

![](img/eeaebbb449b5fcf3056442588a08fafa_89.png)

1.  **加载文件**：使用`load_workbook`打开已有Excel文件。
2.  **选择工作表**：通过名称获取或选择特定的`Sheet`对象。
3.  **读取数据**：掌握了读取单个单元格（`.value`）和单元格区域的方法。
4.  **多种遍历方式**：
    *   **按行遍历** (`iter_rows()`)：逐行处理数据，是最常用的方式。
    *   **按列遍历** (`iter_cols()`)：逐列处理数据。
    *   **遍历指定区域**：通过设置`min_row`， `max_row`， `min_col`， `max_col`参数，可以灵活地处理数据的任何子集。
5.  **修改与保存**：通过赋值修改单元格值，并使用`.save()`方法将更改持久化到文件。

![](img/eeaebbb449b5fcf3056442588a08fafa_91.png)

通过组合运用这些方法，你已经可以高效地读取、检查和修改Excel表格中的数据，为后续的数据分析、清洗和可视化工作打下坚实基础。下一节，我们将学习如何为单元格设置样式，让表格变得更加美观。