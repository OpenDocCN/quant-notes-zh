# 北京大学量化公开课：第14课：使用Python绘制条形图 📊

在本节课中，我们将学习如何使用Python的Matplotlib库来绘制条形图。条形图是数据可视化中非常直观的工具，能够清晰地展示不同类别数据的比较情况。我们将通过一个具体的例子，演示如何创建水平条形图和垂直条形图，以分析不同地区在不同支出项目上的数据。

![](img/048a9604a527ce139c5c99f5472f52ba_1.png)

---

## 概述与数据准备

上一节我们介绍了数据可视化的重要性。本节中，我们来看看如何用Python实现条形图的绘制。首先，我们需要导入必要的库并加载数据。

我们使用Pandas进行数据处理，使用Matplotlib进行绘图。以下是导入库和加载数据的代码：

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/048a9604a527ce139c5c99f5472f52ba_3.png)

# 加载数据
data = pd.read_csv('your_data_path.csv')
print(data.describe())
```

运行上述代码后，我们将得到一个数据表。该表包含八个支出项目和四个地区（北京、天津、上海、重庆）的对应数据。直接查看数据表不够直观，因此我们需要将其可视化。

---

## 绘制水平条形图

![](img/048a9604a527ce139c5c99f5472f52ba_5.png)

接下来，我们将绘制一个水平条形图，以展示北京市各项支出的具体情况。水平条形图适用于类别名称较长或类别较多的场景。

![](img/048a9604a527ce139c5c99f5472f52ba_7.png)

以下是绘制水平条形图的步骤和代码：

1.  **创建图形和子图**：首先，我们设定图形的大小和子图布局。
2.  **准备数据**：提取北京市的支出数据作为绘图数据。
3.  **绘制条形图**：使用`barh`函数绘制水平条形。
4.  **添加标签和标题**：为图表添加清晰的标题、坐标轴标签。
5.  **设置坐标轴范围**：调整X轴的范围，使图表展示更合理。

![](img/048a9604a527ce139c5c99f5472f52ba_9.png)

```python
# 创建图形和子图
fig, ax = plt.subplots(figsize=(10, 6))

![](img/048a9604a527ce139c5c99f5472f52ba_11.png)

# 准备数据：假设‘北京’是数据表中的一列
expenditure_items = data['支出项目']  # 支出项目列表
beijing_expenses = data['北京']       # 北京市各项支出金额

![](img/048a9604a527ce139c5c99f5472f52ba_13.png)

# 绘制水平条形图
ax.barh(expenditure_items, beijing_expenses)

![](img/048a9604a527ce139c5c99f5472f52ba_15.png)

![](img/048a9604a527ce139c5c99f5472f52ba_17.png)

# 添加标签和标题
ax.set_xlabel('支出金额')
ax.set_ylabel('支出项目')
ax.set_title('北京市各项支出项目', size=15)

![](img/048a9604a527ce139c5c99f5472f52ba_19.png)

# 设置X轴范围
ax.set_xlim(0, 19000)

# 显示图形
plt.tight_layout()
plt.show()
```

![](img/048a9604a527ce139c5c99f5472f52ba_21.png)

执行以上代码后，我们将得到一个清晰的水平条形图，可以直观地比较北京市在不同项目上的支出情况，例如“居住”支出最高，“其他服务”支出最低。

![](img/048a9604a527ce139c5c99f5472f52ba_23.png)

---

![](img/048a9604a527ce139c5c99f5472f52ba_25.png)

## 绘制垂直条形图

![](img/048a9604a527ce139c5c99f5472f52ba_27.png)

![](img/048a9604a527ce139c5c99f5472f52ba_29.png)

上一节我们绘制了展示单一地区多项数据的水平条形图。本节中，我们来看看如何绘制垂直条形图，以比较不同地区的支出总额。

![](img/048a9604a527ce139c5c99f5472f52ba_31.png)

垂直条形图是最常见的条形图，适用于比较不同组别的数据。以下是绘制步骤：

![](img/048a9604a527ce139c5c99f5472f52ba_33.png)

1.  **计算数据**：首先，我们需要计算每个地区的支出总额。
2.  **创建图形**：设定新的图形。
3.  **绘制垂直条形**：使用`bar`函数绘制条形。
4.  **添加数值标签**：在每个条形顶端添加具体的数值标签，使图表信息更明确。
5.  **美化图表**：添加标题、坐标轴标签，并调整布局。

![](img/048a9604a527ce139c5c99f5472f52ba_35.png)

以下是实现代码：

![](img/048a9604a527ce139c5c99f5472f52ba_36.png)

```python
# 计算各地区总支出（假设数据列名为地区名）
regions = ['北京', '天津', '上海', '重庆']
total_expenses = [data[region].sum() for region in regions]

![](img/048a9604a527ce139c5c99f5472f52ba_38.png)

# 创建图形和子图
fig, ax = plt.subplots(figsize=(8, 5))

# 绘制垂直条形图
bars = ax.bar(regions, total_expenses)

![](img/048a9604a527ce139c5c99f5472f52ba_40.png)

![](img/048a9604a527ce139c5c99f5472f52ba_42.png)

# 添加数值标签
for bar in bars:
    height = bar.get_height()
    ax.text(bar.get_x() + bar.get_width()/2., height,
            f'{int(height)}',
            ha='center', va='bottom', color='black')

![](img/048a9604a527ce139c5c99f5472f52ba_44.png)

# 添加标签和标题
ax.set_xlabel('地区')
ax.set_ylabel('总支出')
ax.set_title('各地区总支出对比')

![](img/048a9604a527ce139c5c99f5472f52ba_46.png)

# 设置Y轴刻度间隔
ax.set_yticks(range(0, int(max(total_expenses))+20000, 20000))

![](img/048a9604a527ce139c5c99f5472f52ba_48.png)

# 显示图形
plt.tight_layout()
plt.show()
```

![](img/048a9604a527ce139c5c99f5472f52ba_50.png)

运行代码后，我们将得到一个垂直条形图。从图中可以清晰地看出，上海的总支出最高，其次是北京、重庆和天津。通过顶部的数据标签，我们可以精确了解每个地区的总支出数值。

![](img/048a9604a527ce139c5c99f5472f52ba_52.png)

---

![](img/048a9604a527ce139c5c99f5472f52ba_54.png)

## 总结

![](img/048a9604a527ce139c5c99f5472f52ba_56.png)

本节课中，我们一起学习了如何使用Python的Matplotlib库绘制条形图。

![](img/048a9604a527ce139c5c99f5472f52ba_58.png)

*   我们首先介绍了**水平条形图**的绘制方法，用于展示单个类别（如北京市）下多个项目的数据对比。
*   接着，我们学习了**垂直条形图**的绘制，用于比较不同类别（如四个地区）的汇总数据。

![](img/048a9604a527ce139c5c99f5472f52ba_60.png)

通过这两种基本的条形图，我们可以将数据表格转化为直观的视觉信息，从而更有效地进行数据分析和洞察。掌握这些技能是进行量化分析的重要基础。