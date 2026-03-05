# Python Excel 自动化：P78：11 给 Excel 表数据设置各种样式 📊

在本节课中，我们将学习如何使用 Python 的 `openpyxl` 库为 Excel 表格设置各种样式，包括字体、对齐方式、单元格尺寸以及边框。掌握这些技能后，你可以自动化生成格式美观的报表。

---

上一节我们介绍了如何读写 Excel 数据，本节中我们来看看如何美化这些数据，使其更具可读性。

## 导入必要的类

要为单元格设置样式，首先需要从 `openpyxl.styles` 模块中导入几个核心类。

```python
from openpyxl.styles import Font, Alignment, colors
from openpyxl.styles import Border, Side
```

![](img/5173c3ad7fd16b9e607f703cee368f72_1.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_3.png)

以下是这些类的作用：
*   **`Font`**：用于设置字体样式。
*   **`Alignment`**：用于设置单元格内文本的对齐方式。
*   **`colors`**：提供预定义的颜色常量。
*   **`Border`** 和 **`Side`**：用于设置单元格的边框。

![](img/5173c3ad7fd16b9e607f703cee368f72_5.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_7.png)

## 设置字体样式 🖋️

![](img/5173c3ad7fd16b9e607f703cee368f72_9.png)

我们可以创建 `Font` 对象来定义字体名称、大小、是否斜体、颜色和是否加粗等属性。

![](img/5173c3ad7fd16b9e607f703cee368f72_11.png)

以下是设置字体的步骤：
1.  创建一个 `Font` 对象，并设置其属性。
2.  将该对象赋值给目标单元格的 `.font` 属性。

![](img/5173c3ad7fd16b9e607f703cee368f72_13.png)

```python
# 创建一个字体样式对象
my_font = Font(name=‘宋体‘, size=20, italic=True, color=colors.YELLOW, bold=True)

# 将样式应用到 B5 单元格
ws[‘B5‘].font = my_font
```
执行上述代码后，B5 单元格的文本将变为宋体、20号、黄色、斜体并加粗。

![](img/5173c3ad7fd16b9e607f703cee368f72_15.png)

## 设置对齐方式 ↔️

![](img/5173c3ad7fd16b9e607f703cee368f72_17.png)

使用 `Alignment` 对象可以控制单元格内文本的水平和垂直对齐方式。

以下是设置对齐方式的步骤：
1.  创建一个 `Alignment` 对象，并设置其 `horizontal`（水平）和 `vertical`（垂直）属性。
2.  将该对象赋值给目标单元格的 `.alignment` 属性。

![](img/5173c3ad7fd16b9e607f703cee368f72_19.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_21.png)

```python
# 创建一个居中对齐样式对象
my_alignment = Alignment(horizontal=‘center‘, vertical=‘center‘)

![](img/5173c3ad7fd16b9e607f703cee368f72_23.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_25.png)

# 将样式应用到 B5 单元格
ws[‘B5‘].alignment = my_alignment
```
执行后，B5 单元格的文本将在单元格内水平和垂直居中显示。

![](img/5173c3ad7fd16b9e607f703cee368f72_27.png)

## 调整行高与列宽 📏

![](img/5173c3ad7fd16b9e607f703cee368f72_29.png)

我们可以通过工作表对象的行和列维度属性来调整特定行或列的大小。

![](img/5173c3ad7fd16b9e607f703cee368f72_31.png)

以下是调整行高和列宽的步骤：
*   设置行高：`ws.row_dimensions[行号].height = 新高度`
*   设置列宽：`ws.column_dimensions[列字母].width = 新宽度`

![](img/5173c3ad7fd16b9e607f703cee368f72_33.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_35.png)

```python
# 设置第2行的行高为40
ws.row_dimensions[2].height = 40
# 设置C列（第3列）的列宽为20
ws.column_dimensions[‘C‘].width = 20
```
执行后，工作表的第二行将变高，C列将变宽。

![](img/5173c3ad7fd16b9e607f703cee368f72_37.png)

## 设置单元格边框 🟥

通过组合 `Border` 和 `Side` 对象，可以为单元格的四个边分别设置边框样式和颜色。

![](img/5173c3ad7fd16b9e607f703cee368f72_39.png)

以下是设置边框的步骤：
1.  创建 `Side` 对象，定义边框的样式（如线型）和颜色。
2.  创建 `Border` 对象，将 `Side` 对象分别赋值给 `left`, `right`, `top`, `bottom` 属性。
3.  将该 `Border` 对象赋值给目标单元格的 `.border` 属性。

![](img/5173c3ad7fd16b9e607f703cee368f72_41.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_43.png)

```python
# 定义一个红色的单实线边框样式
side_style = Side(style=‘thin‘, color=colors.RED)
my_border = Border(left=side_style, right=side_style, top=side_style, bottom=side_style)

![](img/5173c3ad7fd16b9e607f703cee368f72_45.png)

![](img/5173c3ad7fd16b9e607f703cee368f72_47.png)

# 将边框样式应用到 C5 单元格
ws[‘C5‘].border = my_border
```
执行后，C5 单元格的四周将出现红色的细边框。

![](img/5173c3ad7fd16b9e607f703cee368f72_49.png)

---

![](img/5173c3ad7fd16b9e607f703cee368f72_51.png)

本节课中我们一起学习了如何使用 `openpyxl` 库为 Excel 单元格设置丰富的样式，包括字体、对齐、尺寸和边框。这些功能在自动化生成需要高亮显示或特定格式的商业报表时非常有用。在接下来的练习中，我们将运用这些知识来完成一个实际项目，例如自动生成员工工资条。