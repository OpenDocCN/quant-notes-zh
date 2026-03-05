# Python数据分析与Excel处理：P76：09 Excel文件处理 📊

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_1.png)

在本节课中，我们将学习如何使用Python操作Excel文件。与处理普通文本文件不同，Excel有其特殊的格式，因此需要使用专门的模块来读取、写入和修改其中的数据。

## 安装Openpyxl模块

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_3.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_5.png)

上一节我们介绍了操作Excel的必要性。本节中，我们来看看如何安装所需的工具。

Openpyxl是一个第三方Python模块，专门用于读写Excel 2010 xlsx/xlsm/xltx/xltm文件。在使用前需要单独安装。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_7.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_9.png)

以下是安装方法：
*   使用PyCharm等IDE：在项目解释器设置中，搜索“openpyxl”并点击安装。
*   使用命令行：运行 `pip install openpyxl` 命令进行安装。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_11.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_13.png)

安装完成后，我们就可以开始使用Python操作Excel了。

## 创建与保存Excel文件

学会了安装模块，接下来我们学习如何创建一个全新的Excel文件。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_15.png)

首先需要从`openpyxl`模块中导入`Workbook`类。实例化`Workbook`类相当于在内存中创建了一个新的Excel工作簿。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_17.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_19.png)

```python
from openpyxl import Workbook

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_21.png)

# 在内存中创建一个Excel工作簿
wb = Workbook()
```

一个Excel工作簿可以包含多个工作表（Sheet）。`wb.active`属性用于获取当前活跃的工作表。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_23.png)

```python
# 获取当前活跃的工作表
ws = wb.active
# 打印工作表的默认标题
print(ws.title)
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_25.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_27.png)

默认的工作表标题是“Sheet”。我们可以修改它。

```python
# 修改工作表的标题
ws.title = "ALEX大王的姑娘们"
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_29.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_31.png)

目前所有操作都在内存中进行。最后，我们需要使用`save()`方法将工作簿保存为物理文件。

```python
# 将工作簿保存为名为“excel_test.xlsx”的文件
wb.save(“excel_test.xlsx”)
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_33.png)

执行上述代码后，会在当前目录生成一个Excel文件，打开即可看到名为“ALEX大王的姑娘们”的工作表。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_35.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_37.png)

## 向单元格写入数据

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_39.png)

创建好文件后，自然要向其中填入数据。本节我们学习两种向单元格写入数据的方法。

写入数据的基本原理是通过工作表对象，像操作字典一样，为特定的单元格坐标赋值。单元格坐标使用Excel的列字母+行号格式（例如“B9”）。

```python
# 向B9单元格写入数据
ws[“B9”] = “Black girl”
# 向C9单元格写入数据
ws[“C9”] = “171”
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_41.png)

除了逐个单元格赋值，还可以使用`append()`方法一次性添加一整行数据。该方法接收一个列表，列表中的每个元素会依次填入一行中的连续单元格，并自动添加到工作表现有数据的下一行。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_43.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_45.png)

```python
# 添加一行新数据，包含三个值
new_row = [“Rachel”, 170, 49]
ws.append(new_row)
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_47.png)

写入数据后，别忘了调用`wb.save()`保存文件，更改才会生效。

## 读取已存在的Excel文件

我们不仅需要创建新文件，更多时候需要处理已有的Excel数据。本节我们来学习如何加载和读取现有的Excel文件。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_49.png)

使用`openpyxl`的`load_workbook`函数可以加载一个已存在的Excel文件。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_51.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_53.png)

```python
from openpyxl import load_workbook

# 加载已存在的Excel文件
wb = load_workbook(“excel_test.xlsx”)
# 获取活跃的工作表
ws = wb.active
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_55.png)

加载后，就可以读取单元格中的数据了。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_57.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_59.png)

```python
# 读取B9单元格的值
cell_value = ws[“B9”].value
print(cell_value)  # 输出：Black girl
```

也可以遍历工作表中的行或列来读取大量数据。

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_61.png)

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_63.png)

```python
# 示例：遍历A列的所有单元格（假设有数据）
for cell in ws[“A”]:
    print(cell.value)
```

![](img/1f982e3b5b60fd7b1877f13fbb3a72e7_65.png)

本节课中我们一起学习了使用Python操作Excel文件的核心技能。我们掌握了如何安装`openpyxl`模块、创建新的Excel工作簿、向单元格中写入数据（包括单个写入和整行追加），以及如何加载和读取已有的Excel文件。这些是进行Excel数据自动化处理的基础，后续可以在此基础上学习更复杂的操作，如样式修改、公式计算等。