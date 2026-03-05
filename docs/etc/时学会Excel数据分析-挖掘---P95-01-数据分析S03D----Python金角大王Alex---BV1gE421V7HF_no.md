# Excel数据分析集训营：S03D01：图表可视化基础（上）

![](img/cff2424a8badef05b9e083ffd7753cba_1.png)

![](img/cff2424a8badef05b9e083ffd7753cba_3.png)

在本节课中，我们将要学习Excel图表可视化的核心概念与基础操作。我们将从理解图表的使用场景开始，逐步掌握饼图、圆环图、折线图、条形图等常用图表的创建、数据配置与美化方法。通过本节课的学习，你将能够根据不同的数据分析目的，选择合适的图表并清晰地展示数据。

![](img/cff2424a8badef05b9e083ffd7753cba_5.png)

![](img/cff2424a8badef05b9e083ffd7753cba_7.png)

## 图表的使用场景

![](img/cff2424a8badef05b9e083ffd7753cba_9.png)

![](img/cff2424a8badef05b9e083ffd7753cba_11.png)

上一节我们介绍了课程的整体安排，本节中我们来看看图表的核心价值。使用图表的根本目的是为了更快速、更直观地将信息传达给受众，让对方以最短的路径理解我们想表达的含义。因此，在动手绘制图表前，首先应明确我们想表达什么。

本质上，数据展示无外乎以下几种核心关系：

*   **比较关系**：用于展示多个类别之间数值的大小对比。例如，比较A、B、C三种产品的销量。
*   **趋势关系**：用于展示数据随时间或其他连续变量变化的走势。例如，展示股票价格随时间的变化。
*   **分布关系**：用于展示单个或多个变量的数据分布形态。例如，展示不同收入区间的人口数量。
*   **构成关系**：用于展示整体中各组成部分的占比或结构。例如，展示全年总销售额中各类产品的贡献比例。
*   **关系关系**：用于展示两个或多个变量之间的关联或对比。例如，展示不同职业在各项能力上的评分。

![](img/cff2424a8badef05b9e083ffd7753cba_13.png)

理解了这些核心场景，我们选择图表时才能做到“图达其意”。

![](img/cff2424a8badef05b9e083ffd7753cba_15.png)

## 基础图表操作详解

理解了图表的应用场景后，本节我们将通过具体的图表类型来学习Excel的核心操作逻辑。掌握了一种图表的完整设置方法，其他图表便可触类旁通。

### 饼图：展示单一数据的构成

饼图最擅长表达单一数据系列的构成关系。以下是创建和设置饼图的详细步骤。

**1. 创建基础饼图**
选中数据区域（例如A1:B5），点击【插入】->【饼图】，即可生成基础饼图。

![](img/cff2424a8badef05b9e083ffd7753cba_17.png)

**2. 修改数据源**
若想将展示的数据从“销售数量”改为“销售额”，无需重画。右键点击图表，选择【选择数据】。
*   在左侧“图例项（系列）”中，选中现有系列并点击【编辑】。
*   在“系列名称”中，可以输入文字或点击箭头绑定到新标题的单元格（如C1）。
*   在“系列值”中，重新选择新的数据区域（如C2:C5）。
*   点击【确定】完成数据源修改。

**3. 添加与设置图表元素**
点击图表旁的【+】号，可以添加或隐藏图表元素，如“图表标题”、“图例”、“数据标签”。
*   若要详细设置某个元素（如数据标签），可点击该元素旁的箭头，选择【更多选项】。
*   在右侧窗格中，可进行详细设置。例如，在“标签选项”中，可以：
    *   勾选“类别名称”来显示每个扇区对应的分类（如地区）。
    *   勾选“百分比”来显示占比而非具体数值。
    *   通过“标签位置”调整标签显示位置。

**4. 更改图表样式与颜色**
点击图表旁的画笔图标，可以快速应用预设的“图表样式”和“颜色”方案。对于更专业的美化，建议使用设计好的模板。

### 圆环图：展示多组数据的构成

圆环图是饼图的拓展，可以在一张图中展示多组数据（多个环）的构成，便于进行组间对比。

**1. 创建多环圆环图**
与饼图类似，首先插入一个空白的圆环图。右键点击图表，选择【选择数据】。
*   点击【添加】第一个系列，设置“系列名称”和“系列值”（如销售数量）。
*   在右侧“水平（分类）轴标签”处点击【编辑】，选择分类标签区域（如地区）。
*   再次点击【添加】，用同样方法加入第二个系列（如销售额）。两个系列可以共用同一组分类标签。

**2. 自定义数据标签**
添加“数据标签”后，在右侧“标签选项”中，可以绑定“单元格中的值”来显示自定义文本，或同时显示“类别名称”、“值”、“百分比”等。

**3. 调整环的大小与筛选数据**
*   选中任意数据环，在右侧窗格“系列选项”中，调整“圆环图内径大小”百分比，可以改变环的粗细。
*   点击图表下的漏斗图标，可以使用“图表筛选器”选择性地显示或隐藏某些分类或系列。

### 复合饼图：处理长尾数据

![](img/cff2424a8badef05b9e083ffd7753cba_19.png)

![](img/cff2424a8badef05b9e083ffd7753cba_21.png)

![](img/cff2424a8badef05b9e083ffd7753cba_23.png)

![](img/cff2424a8badef05b9e083ffd7753cba_25.png)

![](img/cff2424a8badef05b9e083ffd7753cba_27.png)

当数据中存在“长尾现象”（即少数项目占据大部分比例，其余众多项目占比很小）时，使用普通饼图会导致小占比区域拥挤不清。复合饼图可以将小占比部分提取到第二个饼图中进行放大展示。

![](img/cff2424a8badef05b9e083ffd7753cba_29.png)

![](img/cff2424a8badef05b9e083ffd7753cba_31.png)

![](img/cff2424a8badef05b9e083ffd7753cba_33.png)

![](img/cff2424a8badef05b9e083ffd7753cba_35.png)

![](img/cff2424a8badef05b9e083ffd7753cba_37.png)

![](img/cff2424a8badef05b9e083ffd7753cba_39.png)

**1. 创建复合饼图**
选中数据，插入【复合饼图】。Excel会自动进行初步分割。

![](img/cff2424a8badef05b9e083ffd7753cba_41.png)

![](img/cff2424a8badef05b9e083ffd7753cba_43.png)

![](img/cff2424a8badef05b9e083ffd7753cba_45.png)

![](img/cff2424a8badef05b9e083ffd7753cba_47.png)

![](img/cff2424a8badef05b9e083ffd7753cba_49.png)

![](img/cff2424a8badef05b9e083ffd7753cba_51.png)

![](img/cff2424a8badef05b9e083ffd7753cba_53.png)

![](img/cff2424a8badef05b9e083ffd7753cba_55.png)

**2. 设置分割规则**
选中图表，在右侧“系列选项”中，可以设置“系列分割依据”。
*   **按位置**：指定将最后N项放入次饼图。
*   **按值**：指定小于某个值的项放入次饼图。
*   **按百分比值**：指定小于某个百分比值的项放入次饼图。
*   **自定义**：手动将特定数据点分配到主/次饼图。

![](img/cff2424a8badef05b9e083ffd7753cba_57.png)

![](img/cff2424a8badef05b9e083ffd7753cba_59.png)

![](img/cff2424a8badef05b9e083ffd7753cba_61.png)

![](img/cff2424a8badef05b9e083ffd7753cba_63.png)

![](img/cff2424a8badef05b9e083ffd7753cba_65.png)

### 折线图：展示数据趋势

![](img/cff2424a8badef05b9e083ffd7753cba_67.png)

![](img/cff2424a8badef05b9e083ffd7753cba_69.png)

![](img/cff2424a8badef05b9e083ffd7753cba_71.png)

![](img/cff2424a8badef05b9e083ffd7753cba_73.png)

![](img/cff2424a8badef05b9e083ffd7753cba_75.png)

![](img/cff2424a8badef05b9e083ffd7753cba_77.png)

![](img/cff2424a8badef05b9e083ffd7753cba_79.png)

![](img/cff2424a8badef05b9e083ffd7753cba_81.png)

折线图通常用于展示数据随时间变化的趋势。其核心是数据点与连接线。

![](img/cff2424a8badef05b9e083ffd7753cba_83.png)

![](img/cff2424a8badef05b9e083ffd7753cba_85.png)

![](img/cff2424a8badef05b9e083ffd7753cba_87.png)

![](img/cff2424a8badef05b9e083ffd7753cba_89.png)

![](img/cff2424a8badef05b9e083ffd7753cba_91.png)

![](img/cff2424a8badef05b9e083ffd7753cba_93.png)

**1. 创建与美化折线图**
选中包含时间序列和多组数据的数据区域，插入【折线图】。
*   添加“数据标签”可在每个点上显示数值。
*   添加“坐标轴标题”可说明横纵轴含义。
*   选中某条折线，在右侧“系列选项”的“标记”中，可以为数据点添加形状（如圆形、方形），并调整大小和填充颜色。

![](img/cff2424a8badef05b9e083ffd7753cba_95.png)

![](img/cff2424a8badef05b9e083ffd7753cba_97.png)

![](img/cff2424a8badef05b9e083ffd7753cba_99.png)

![](img/cff2424a8badef05b9e083ffd7753cba_101.png)

![](img/cff2424a8badef05b9e083ffd7753cba_103.png)

![](img/cff2424a8badef05b9e083ffd7753cba_105.png)

![](img/cff2424a8badef05b9e083ffd7753cba_107.png)

![](img/cff2424a8badef05b9e083ffd7753cba_109.png)

![](img/cff2424a8badef05b9e083ffd7753cba_111.png)

**2. 折线图的变体：堆积与百分比堆积**
*   **堆积折线图**：不仅能看各系列趋势，还能看每一期数据的总量及各系列的构成。
*   **百分比堆积折线图**：将每一期数据总量视为100%，展示各系列占比的趋势变化。

![](img/cff2424a8badef05b9e083ffd7753cba_113.png)

![](img/cff2424a8badef05b9e083ffd7753cba_115.png)

![](img/cff2424a8badef05b9e083ffd7753cba_117.png)

![](img/cff2424a8badef05b9e083ffd7753cba_119.png)

![](img/cff2424a8badef05b9e083ffd7753cba_121.png)

### 条形图：进行数据比较

![](img/cff2424a8badef05b9e083ffd7753cba_123.png)

![](img/cff2424a8badef05b9e083ffd7753cba_125.png)

![](img/cff2424a8badef05b9e083ffd7753cba_127.png)

条形图（柱形图）擅长进行多组数据间的大小比较。方向可以是垂直（柱形）或水平（条形）。

![](img/cff2424a8badef05b9e083ffd7753cba_129.png)

![](img/cff2424a8badef05b9e083ffd7753cba_131.png)

![](img/cff2424a8badef05b9e083ffd7753cba_133.png)

![](img/cff2424a8badef05b9e083ffd7753cba_135.png)

![](img/cff2424a8badef05b9e083ffd7753cba_137.png)

![](img/cff2424a8badef05b9e083ffd7753cba_139.png)

![](img/cff2424a8badef05b9e083ffd7753cba_141.png)

![](img/cff2424a8badef05b9e083ffd7753cba_143.png)

![](img/cff2424a8badef05b9e083ffd7753cba_145.png)

**1. 切换数据行列视角**
创建簇状条形图后，在【图表设计】选项卡中点击【切换行/列】，可以快速将图表从“比较不同产品在各地区的销量”转换为“比较不同地区各产品的销量”。

![](img/cff2424a8badef05b9e083ffd7753cba_147.png)

![](img/cff2424a8badef05b9e083ffd7753cba_149.png)

![](img/cff2424a8badef05b9e083ffd7753cba_151.png)

![](img/cff2424a8badef05b9e083ffd7753cba_153.png)

![](img/cff2424a8badef05b9e083ffd7753cba_155.png)

![](img/cff2424a8badef05b9e083ffd7753cba_157.png)

![](img/cff2424a8badef05b9e083ffd7753cba_159.png)

![](img/cff2424a8badef05b9e083ffd7753cba_161.png)

![](img/cff2424a8badef05b9e083ffd7753cba_163.png)

**2. 条形图的变体：堆积条形图**
堆积条形图将同一分类下的多个系列数值堆叠起来。它既能比较同一分类下各组成部分的大小，也能通过总长度比较不同分类之间的总和大小。

![](img/cff2424a8badef05b9e083ffd7753cba_165.png)

![](img/cff2424a8badef05b9e083ffd7753cba_167.png)

![](img/cff2424a8badef05b9e083ffd7753cba_169.png)

![](img/cff2424a8badef05b9e083ffd7753cba_171.png)

![](img/cff2424a8badef05b9e083ffd7753cba_172.png)

**关于数据规范的特别提醒**
在绘制图表（尤其是带时间轴的折线图）前，确保源数据格式规范至关重要。例如，日期应使用“2022-07-19”或“2022年7月19日”等Excel可识别的标准格式，而非“2022-年-7-19”。数值不应混合货币符号等文本，而应使用单元格格式设置来显示。规范的数据是高效、准确分析的基础。

![](img/cff2424a8badef05b9e083ffd7753cba_174.png)

![](img/cff2424a8badef05b9e083ffd7753cba_176.png)

![](img/cff2424a8badef05b9e083ffd7753cba_178.png)

![](img/cff2424a8badef05b9e083ffd7753cba_180.png)

![](img/cff2424a8badef05b9e083ffd7753cba_182.png)

![](img/cff2424a8badef05b9e083ffd7753cba_184.png)

![](img/cff2424a8badef05b9e083ffd7753cba_186.png)

![](img/cff2424a8badef05b9e083ffd7753cba_188.png)

## 总结

![](img/cff2424a8badef05b9e083ffd7753cba_190.png)

![](img/cff2424a8badef05b9e083ffd7753cba_192.png)

![](img/cff2424a8badef05b9e083ffd7753cba_194.png)

![](img/cff2424a8badef05b9e083ffd7753cba_196.png)

![](img/cff2424a8badef05b9e083ffd7753cba_198.png)

![](img/cff2424a8badef05b9e083ffd7753cba_200.png)

![](img/cff2424a8badef05b9e083ffd7753cba_202.png)

![](img/cff2424a8badef05b9e083ffd7753cba_204.png)

![](img/cff2424a8badef05b9e083ffd7753cba_206.png)

本节课中我们一起学习了Excel图表可视化的基础。我们首先明确了图表的核心使用场景：比较、趋势、分布、构成和关系。然后，我们以饼图为例，深入讲解了图表的通用操作逻辑：如何创建、如何通过【选择数据】修改数据源、如何添加和设置图表元素、如何调整样式。随后，我们将这些逻辑应用到圆环图、复合饼图、折线图和条形图中，并了解了每种图表的适用场景及其变体（如堆积图）。记住，图表是工具，清晰传达信息才是目的。在动手前，先思考“我想表达什么”，再选择“用什么图表表达”。