# Python自动化办公：S03D03：自动化办公（上）

![](img/f5dd9f240eef36e1d1256df7593ca01e_1.png)

## 概述

在本节课中，我们将要学习如何使用Python进行自动化办公。我们将从最基础的编程概念讲起，逐步深入到如何用Python批量处理文件、读取Word文档内容。即使你完全没有编程基础，也能通过本节课理解自动化办公的核心思路。

![](img/f5dd9f240eef36e1d1256df7593ca01e_3.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_5.png)

---

![](img/f5dd9f240eef36e1d1256df7593ca01e_7.png)

## 基础概念：变量与数据类型

上一节我们介绍了课程的整体目标，本节中我们来看看编程的基础——变量与数据类型。

计算机的所有数据都以电流状态存储在内存中。为了使用这些数据，我们需要用“变量”这个“盒子”来存放它们。变量可以存放不同类型的数据。

以下是几种常见的数据类型：

*   **整数**：存放如 `100`、`-5` 这样的数字。
    ```python
    salary = 100  # 将整数100存入变量salary
    ```
*   **浮点数**：存放如 `3.14`、`4.668` 这样的小数。
*   **字符串**：存放文本，用单引号或双引号括起来，相当于Excel中的文本类型。
    ```python
    text = '今天很热'  # 将字符串存入变量text
    ```
*   **列表**：存放一组有序的数据，可以方便地管理大量数据。列表中的元素从0开始编号。
    ```python
    data_list = [3, 7, 11, 8]  # 创建一个列表
    first_data = data_list[0]  # 访问列表的第一个元素，值为3
    ```

---

## 程序结构：分支与循环

理解了如何存放数据后，我们需要学习如何让计算机根据条件做出判断，以及如何重复执行任务。这就要用到分支和循环结构。

### 分支结构 (if-else)

分支结构用于根据条件执行不同的代码块。它就像给计算机一个明确的指令清单：如果遇到情况A，就执行操作A；否则，执行操作B。

![](img/f5dd9f240eef36e1d1256df7593ca01e_9.png)

```python
score = 400
if score > 450:
    print('本科')
else:
    print('专科')
```
上面的代码会判断分数是否大于450，并打印相应的结果。

![](img/f5dd9f240eef36e1d1256df7593ca01e_11.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_13.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_15.png)

### 循环结构 (for)

![](img/f5dd9f240eef36e1d1256df7593ca01e_17.png)

循环结构用于重复执行某段代码。Python的 `for` 循环特别直观，它遍历一个序列（如列表），每次取出一个元素进行处理。

![](img/f5dd9f240eef36e1d1256df7593ca01e_19.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_21.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_23.png)

```python
# 遍历一个数字序列
for i in range(5):
    print(i)  # 依次打印 0, 1, 2, 3, 4

![](img/f5dd9f240eef36e1d1256df7593ca01e_25.png)

# 遍历一个列表
files = ['a.docx', 'b.pptx', 'c.pdf']
for file in files:
    print(file)  # 依次打印列表中的每个文件名
```
你可以把 `for file in files:` 理解为：从 `files` 这个“口袋”里，依次取出每一个“文件”（`file`），然后执行冒号下面的操作。

---

![](img/f5dd9f240eef36e1d1256df7593ca01e_27.png)

## 文件与路径操作

![](img/f5dd9f240eef36e1d1256df7593ca01e_29.png)

现在，我们将基础概念应用到实际场景中：如何让Python找到并操作我们电脑里的文件。

![](img/f5dd9f240eef36e1d1256df7593ca01e_31.png)

### 使用OS模块

Python的 `os` 模块提供了许多操作文件和目录的函数。首先需要导入这个模块。

```python
import os  # 导入os工具包
```

### 列出目录下的文件

我们可以使用 `os.listdir()` 函数来获取指定文件夹内的所有文件和子文件夹名称。

```python
file_list = os.listdir('./')  # 获取当前目录下的所有文件和文件夹名
print(file_list)
```
`'./'` 表示当前文件所在的目录，这是一个**相对路径**。与之相对的是**绝对路径**，它从盘符开始完整描述文件位置，例如 `C:\Users\Desktop\我的文件.docx`。

### 筛选特定类型的文件

结合循环和字符串方法，我们可以轻松筛选出特定类型的文件，例如所有Word文档。

```python
for file in file_list:
    if file.endswith('.docx') or file.endswith('.doc'):
        print(f'找到Word文档: {file}')
```
`str.endswith()` 方法用于判断字符串是否以指定的后缀结尾。

---

## 操作Word文档

![](img/f5dd9f240eef36e1d1256df7593ca01e_33.png)

掌握了文件操作后，我们进入核心环节：用Python读取和操作Word文档内容。

![](img/f5dd9f240eef36e1d1256df7593ca01e_35.png)

### 安装与导入python-docx库

Python本身不直接处理Word文档，我们需要安装第三方库 `python-docx`。

1.  打开命令行终端。
2.  激活你的Python环境（例如 `activate base`）。
3.  输入安装命令：
    ```bash
    pip install python-docx
    ```

安装成功后，在代码中导入它：

![](img/f5dd9f240eef36e1d1256df7593ca01e_37.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_39.png)

```python
import docx
```

### 理解Word文档的结构

在编程视角下，一个Word文档（`Document`）被抽象为包含多个段落（`Paragraph`）和表格（`Table`）的对象。
*   每个**段落**中可能包含多个**文字块（Run）**。一个Run是一段具有相同样式（字体、颜色等）的连续文字。
*   每个**表格**由行（`Rows`）和列（`Columns`）组成，每个单元格（`Cell`）内又可以包含段落。

这种结构化的理解，是让计算机处理复杂文档的关键。

### 读取文档内容

以下是读取一个Word文档所有段落文本的步骤：

![](img/f5dd9f240eef36e1d1256df7593ca01e_41.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_43.png)

```python
# 1. 将Word文档加载到内存中，创建一个Document对象
doc = docx.Document('练习.docx')

# 2. 获取文档中的所有段落
paragraphs = doc.paragraphs

![](img/f5dd9f240eef36e1d1256df7593ca01e_45.png)

# 3. 遍历每个段落，并打印其文本内容
for para in paragraphs:
    print(para.text)
    print('---' * 10)  # 打印分割线
```

![](img/f5dd9f240eef36e1d1256df7593ca01e_47.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_49.png)

![](img/f5dd9f240eef36e1d1256df7593ca01e_51.png)

### 操作文字块（Run）

![](img/f5dd9f240eef36e1d1256df7593ca01e_53.png)

如果只想修改文字内容而保留原有的格式（如加粗、颜色），就需要操作到Run级别。

```python
# 获取第一个段落
first_para = doc.paragraphs[0]

# 获取该段落中的所有Run
runs = first_para.runs
print(f'该段落包含 {len(runs)} 个Run。')

# 遍历并打印每个Run的文本
for run in runs:
    print(run.text)
```
通过修改 `run.text` 的值，即可在保留原格式的前提下更新文本。

---

## 总结

本节课中我们一起学习了Python自动化办公的基础知识。我们从变量、数据类型、分支循环等核心编程概念出发，逐步学会了如何使用 `os` 模块操作文件路径和筛选文件，并重点讲解了利用 `python-docx` 库读取和操作Word文档内容的方法。

![](img/f5dd9f240eef36e1d1256df7593ca01e_55.png)

关键点在于理解计算机处理问题的逻辑：将复杂任务（如处理Word文档）拆解为可被代码描述的结构化步骤。虽然初次接触代码细节可能有些陌生，但核心思想与我们用Excel函数解决问题的思路是相通的。通过针对性地练习，你完全可以掌握这些技能，让Python成为你高效办公的得力助手。