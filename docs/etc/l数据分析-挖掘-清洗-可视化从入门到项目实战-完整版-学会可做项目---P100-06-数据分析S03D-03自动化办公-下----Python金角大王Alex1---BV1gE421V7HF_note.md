# Python自动化办公：06：数据分析S03D 03自动化办公（下） 📄➡️🤖

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_1.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_3.png)

## 概述
在本节课中，我们将学习如何使用Python自动化处理Word文档和PPT演示文稿。我们将深入探讨如何读取、修改文档内容，并理解其内部结构，从而实现批量、高效的自动化办公任务。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_5.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_7.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_9.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_11.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_13.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_15.png)

---

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_17.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_19.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_21.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_23.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_25.png)

## Word文档的自动化处理

上一节我们介绍了如何读取Word文档的基本结构。本节中，我们来看看如何通过循环迭代来精确地定位和修改文档中的特定内容。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_27.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_28.png)

### 段落与文本块的迭代
一个Word文档由多个段落(`paragraphs`)组成，每个段落又包含多个文本块(`runs`)。文本块是拥有独立格式（如字体、颜色）的最小文本单元。

以下是遍历文档中所有段落和文本块的基本代码结构：
```python
from docx import Document

doc = Document('练习.docx')
for para in doc.paragraphs:
    for run in para.runs:
        print(run.text)
```

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_30.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_32.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_34.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_36.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_38.png)

### 修改特定文本
我们的目标是将文档中所有的“document”替换为“dancer的文档”。直接对整个段落文本赋值会破坏原有格式，因此我们需要在文本块(`run`)级别进行操作。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_40.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_42.png)

**核心思路**：
1.  遍历每个文本块(`run`)。
2.  判断该文本块的文字是否包含目标词汇。
3.  如果包含，则修改该文本块的文字。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_44.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_46.png)

以下是实现代码：
```python
from docx import Document

doc = Document('练习.docx')

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_48.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_50.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_52.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_54.png)

for para in doc.paragraphs:
    for run in para.runs:
        # 判断文本块中是否包含‘document’
        if ‘document’ in run.text:
            # 进行替换
            run.text = run.text.replace(‘document‘， ’dancer的文档‘)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_56.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_58.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_60.png)

# 将修改后的文档对象保存到新文件
doc.save(‘结果.docx’)
```
**关键点**：使用 `in` 进行模糊匹配，而非 `==` 精确相等，可以避免因文档中不可见的空格或格式字符导致的判断失败。

### 操作Word中的表格
Word文档中的表格可以通过 `tables` 属性访问。表格由行(`rows`)和列(`columns`)组成，单元格(`cell`)内可以包含段落。

以下是读取表格内容的示例：
```python
from docx import Document

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_62.png)

doc = Document(‘练习2.docx’)
# 获取文档中的第一个表格
table = doc.tables[0]

# 遍历表格的每一行
for row in table.rows:
    # 遍历行中的每一个单元格
    for cell in row.cells:
        # 单元格内的文字可能由多个段落组成
        for para in cell.paragraphs:
            print(para.text)
```
**重要提示**：对于涉及数据统计、计算和分析的表格操作，Python的 `docx` 库并非最佳选择。这类任务应使用专门的数据分析库 **pandas**，它能更高效地处理数据聚合、分组、透视等复杂运算。

---

## PPT演示文稿的自动化处理

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_64.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_66.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_68.png)

理解了Word文档的操作后，PPT的处理逻辑是相似的。PPT可以看作是多层嵌套的结构。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_70.png)

### PPT文档的结构解析
一个PPT文件(`Presentation`)由多个幻灯片页(`Slides`)组成。每一页又包含多个形状(`Shapes`)。这些形状可能是文本框、图片、图表等。我们主要操作包含文本的形状。

**结构层级关系**：
`Presentation` -> `Slides` -> `Shapes` (包含 `Text Frame`) -> `Paragraphs` -> `Runs` -> `Text`

### 修改PPT中的文本
以下示例演示了如何将PPT第二页中所有以“情况”结尾的文本替换为“信息”。

```python
from pptx import Presentation

# 1. 加载PPT文档
ppt = Presentation(‘数据报告模板.pptx’)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_72.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_74.png)

# 2. 获取第二页幻灯片 (索引从0开始)
slide = ppt.slides[1]

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_76.png)

# 3. 遍历该页的所有形状
for shape in slide.shapes:
    # 4. 判断形状是否包含文本框
    if shape.has_text_frame:
        text_frame = shape.text_frame
        # 5. 遍历文本框中的所有段落
        for para in text_frame.paragraphs:
            # 6. 遍历段落中的所有文本块
            for run in para.runs:
                # 7. 判断并替换文本
                if run.text.endswith(‘情况’):
                    run.text = run.text.replace(‘情况‘， ’信息‘)

# 8. 保存修改后的PPT
ppt.save(‘结论.pptx’)
```

---

## 综合实战：批量生成奖状 🏆

现在，我们将运用所学知识完成一个综合任务。

**任务描述**：
1.  有一个Word文档 (`名单.docx`)，其中包含一个学员姓名表格。
2.  有一个PPT奖状模板 (`职业奖励.pptx`)，其中“某某某”和标题“职业奖励”是待替换的占位符。
3.  编写Python程序，读取学员名单，为每位学员生成一份独立的奖状PPT文件。新文件应以学员姓名命名，且内容中的姓名和标题需相应替换。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_78.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_80.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_82.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_84.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_86.png)

**实现思路**：
1.  使用 `docx` 库读取 `名单.docx` 中的表格，获取所有学员姓名列表。
2.  使用 `pptx` 库加载奖状模板。
3.  循环遍历学员姓名列表：
    *   复制或重新加载模板。
    *   在复制的模板中，遍历所有形状、段落和文本块，查找“某某某”和“职业奖励”进行替换。
    *   将修改后的PPT以 `{学员姓名}的奖励.pptx` 为名保存。

---

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_88.png)

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_90.png)

## 总结
本节课中我们一起学习了Python自动化办公的核心技能：
1.  **Word自动化**：掌握了通过 `docx` 库迭代文档段落(`paragraphs`)、文本块(`runs`)来精确读取和修改文本及表格内容的方法，并理解了在内存中修改后必须调用 `save()` 方法才能保存到磁盘。
2.  **PPT自动化**：理解了PPT文档 `Presentation` -> `Slide` -> `Shape` -> `Text Frame` 的层级结构，学会了如何逐层遍历并修改幻灯片中的文本内容。
3.  **核心原则**：对于格式复杂的文档，应在最细粒度的文本块(`run`)级别进行操作，以保留原始格式。对于数据分析任务，应优先使用 `pandas` 而非基础的文件操作库。

![](img/a6441a5bb4f6337f4b10a8bab2ee2740_92.png)

通过将这些技术组合运用，你可以高效地完成诸如批量文档生成、内容替换、报告自动化等繁琐办公任务，极大提升工作效率。