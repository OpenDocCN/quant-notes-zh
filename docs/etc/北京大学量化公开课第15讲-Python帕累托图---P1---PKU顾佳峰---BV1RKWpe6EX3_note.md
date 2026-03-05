# 北京大学量化公开课：第15讲：Python帕累托图绘制教程 📊

在本节课中，我们将学习如何使用Python绘制帕累托图。帕累托图是一种结合了条形图和折线图的图表，常用于分析主要因素或项目。我们将使用`pandas`和`matplotlib`库，通过一个具体的支出数据示例，一步步完成图表的创建。

---

## 数据准备与导入

![](img/e75040112dc31e8c72d58737fb8a8f3e_1.png)

上一节我们介绍了课程概述，本节中我们来看看如何准备数据。首先，我们需要导入必要的Python库并加载数据。

我们使用`pandas`进行数据处理，使用`matplotlib`进行绘图。假设我们有一个包含八个支出项目和四个地区（北京、天津、上海、重庆）支出情况的CSV文件。

```python
import pandas as pd
import matplotlib.pyplot as plt

# 读取数据
df = pd.read_csv('your_data.csv')
print(df.head())
```

数据框`df`包含八行五列。第一列是支出项目，后续各列是不同地区的支出金额。本节课我们将以“北京”地区的支出数据为例进行排序和分析。

---

## 数据处理与排序

在绘制图表前，需要对数据进行处理。我们将按照“北京”地区的支出金额进行降序排序，并计算累计百分比。

![](img/e75040112dc31e8c72d58737fb8a8f3e_3.png)

以下是数据处理的关键步骤：
1.  按“北京”支出金额排序。
2.  计算每个项目的支出占比。
3.  计算累计百分比。

```python
# 按‘北京’列进行降序排序
df_sorted = df.sort_values(by='北京', ascending=False)

# 计算‘北京’列的总和
total_expenditure = df_sorted['北京'].sum()

# 计算每个项目的百分比和累计百分比
df_sorted['百分比'] = (df_sorted['北京'] / total_expenditure) * 100
df_sorted['累计百分比'] = df_sorted['百分比'].cumsum()

print(df_sorted[['支出项目', '北京', '百分比', '累计百分比']])
```

处理完成后，数据框中新增了“百分比”和“累计百分比”两列，为绘图做好了准备。

---

## 绘制帕累托图

现在，我们使用处理好的数据来绘制帕累托图。该图由两部分组成：代表各项目支出金额的条形图，以及代表累计百分比的折线图。

以下是绘图步骤的分解说明：

1.  **创建图形和坐标轴**：我们创建一个图形，并设置共享X轴的双坐标轴。
2.  **绘制条形图**：在第一个坐标轴上，用条形图展示各支出项目的金额。
3.  **绘制折线图**：在第二个坐标轴上，用折线图展示累计百分比。
4.  **添加标签与格式**：为图表添加标题、轴标签、数据标签并进行美化。

```python
# 设置中文字体（确保系统支持）
plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus'] = False    # 用来正常显示负号

fig, ax1 = plt.subplots(figsize=(10, 6))

# 1. 在ax1上绘制条形图（支出金额）
bars = ax1.bar(df_sorted['支出项目'], df_sorted['北京'], color='skyblue')
ax1.set_xlabel('支出项目')
ax1.set_ylabel('支出金额（北京）', color='skyblue')
ax1.tick_params(axis='y', labelcolor='skyblue')
plt.xticks(rotation=30)  # 将X轴标签旋转30度，防止重叠

# 2. 创建共享X轴的第二个坐标轴ax2，用于绘制折线图
ax2 = ax1.twinx()
line = ax2.plot(df_sorted['支出项目'], df_sorted['累计百分比'], color='coral', marker='D', linewidth=2)
ax2.set_ylabel('累计百分比 (%)', color='coral')
ax2.tick_params(axis='y', labelcolor='coral')
ax2.set_ylim(0, 110)  # 设置Y轴范围，为标签留出空间

# 3. 在折线图的每个点上添加数据标签
for x, y in zip(df_sorted['支出项目'], df_sorted['累计百分比']):
    ax2.text(x, y+1, f'{y:.1f}%', ha='center', va='bottom', color='black')

plt.title('北京地区支出帕累托图')
fig.tight_layout()  # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

执行以上代码后，将生成一张清晰的帕累托图。条形图按降序显示了各项目的支出，折线图则展示了这些项目支出累计占总支出的比例。这种图表能直观地帮助我们识别出哪些是主要的支出项目（通常遵循“二八定律”）。

---

![](img/e75040112dc31e8c72d58737fb8a8f3e_5.png)

## 课程总结与延伸

本节课中我们一起学习了如何使用Python绘制帕累托图。我们从数据导入和排序开始，逐步完成了累计百分比的计算，并最终使用`matplotlib`库将条形图与折线图结合，生成了可用于关键因素分析的帕累托图。

帕累托图的核心思想与经济学中的“帕累托改进”概念一致，都体现了**抓住关键少数**的分析方法。通过本教程，你不仅掌握了一个实用的数据可视化技能，也理解了其背后的分析逻辑。你可以尝试将此方法应用于自己领域的数据，例如分析客户投诉类型、产品缺陷原因或销售贡献度等。