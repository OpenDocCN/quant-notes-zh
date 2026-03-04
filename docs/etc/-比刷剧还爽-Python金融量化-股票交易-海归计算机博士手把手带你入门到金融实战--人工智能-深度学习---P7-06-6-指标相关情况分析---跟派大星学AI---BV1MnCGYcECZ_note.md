# 量化交易全教程：P7：06-6-指标相关情况分析 📈

![](img/baa0f6a6e63f608417a14babde91d80a_1.png)

在本节课中，我们将学习如何使用Python进行金融数据的回归分析，以探索不同指标（如标普500指数和VIX恐慌指数）之间的相互关系。我们将通过数据可视化（折线图、散点图）和统计方法，直观地理解变量间的关联性。

![](img/baa0f6a6e63f608417a14babde91d80a_3.png)

![](img/baa0f6a6e63f608417a14babde91d80a_5.png)

## 概述
回归分析的核心是探索变量之间的关系。本节我们将选取两个金融指标，通过绘制图表和计算，分析它们之间是否存在相互影响或关联。

![](img/baa0f6a6e63f608417a14babde91d80a_7.png)

![](img/baa0f6a6e63f608417a14babde91d80a_9.png)

## 第一步：数据准备与提取
首先，我们需要从数据集中提取出要分析的两个指标列。这里我们选择`SPX`（标普500指数）和`VIX`（恐慌指数）。

![](img/baa0f6a6e63f608417a14babde91d80a_11.png)

![](img/baa0f6a6e63f608417a14babde91d80a_13.png)

![](img/baa0f6a6e63f608417a14babde91d80a_15.png)

```python
# 从data2数据集中提取SPX和VIX两列数据
data = data2[['SPX', 'VIX']]
print(data.head())
```

![](img/baa0f6a6e63f608417a14babde91d80a_17.png)

执行以上代码后，我们便得到了一个仅包含`SPX`和`VIX`两列的新DataFrame，用于后续分析。

![](img/baa0f6a6e63f608417a14babde91d80a_19.png)

![](img/baa0f6a6e63f608417a14babde91d80a_21.png)

## 第二步：初步可视化观察
为了直观感受两个指标的关系，最直接的方法是绘制图表。我们先尝试将它们画在两个独立的子图中。

![](img/baa0f6a6e63f608417a14babde91d80a_23.png)

![](img/baa0f6a6e63f608417a14babde91d80a_25.png)

```python
# 将两个指标分别绘制在两个子图中
data.plot(subplots=True)
```

![](img/baa0f6a6e63f608417a14babde91d80a_27.png)

![](img/baa0f6a6e63f608417a14babde91d80a_29.png)

然而，将两个指标分开绘制不利于观察它们之间的联动关系。因此，我们需要改进可视化方法。

![](img/baa0f6a6e63f608417a14babde91d80a_31.png)

![](img/baa0f6a6e63f608417a14babde91d80a_33.png)

上一节我们介绍了如何分别绘制两个指标，本节中我们来看看如何将它们整合到同一张图中进行对比。

![](img/baa0f6a6e63f608417a14babde91d80a_35.png)

![](img/baa0f6a6e63f608417a14babde91d80a_37.png)

![](img/baa0f6a6e63f608417a14babde91d80a_39.png)

![](img/baa0f6a6e63f608417a14babde91d80a_41.png)

## 第三步：改进可视化——双Y轴图表
为了在同一坐标系下对比两个指标，我们可以使用双Y轴图表。这样，两个序列可以共享同一个X轴（时间），但拥有各自的Y轴刻度。

![](img/baa0f6a6e63f608417a14babde91d80a_43.png)

```python
# 在同一张图中绘制双Y轴折线图
data.plot(secondary_y=‘VIX’, figsize=(10, 6))
```

![](img/baa0f6a6e63f608417a14babde91d80a_45.png)

![](img/baa0f6a6e63f608417a14babde91d80a_47.png)

通过这张图，我们可以初步观察`SPX`和`VIX`的变化趋势。为了更清晰地观察短期关系，我们可以只选取特定时间段（例如2010年至2012年）的数据进行绘制。

![](img/baa0f6a6e63f608417a14babde91d80a_49.png)

![](img/baa0f6a6e63f608417a14babde91d80a_51.png)

![](img/baa0f6a6e63f608417a14babde91d80a_53.png)

```python
# 选取2010年至2012年的数据
data_subset = data[‘2010-01-01’:‘2012-12-31’]
data_subset.plot(secondary_y=‘VIX’, figsize=(10, 6))
```

![](img/baa0f6a6e63f608417a14babde91d80a_55.png)

观察改进后的图表，我们可以发现一个初步规律：当`SPX`指数下降时，`VIX`指数往往上升，反之亦然。这表明两者可能存在负相关关系。

![](img/baa0f6a6e63f608417a14babde91d80a_57.png)

![](img/baa0f6a6e63f608417a14babde91d80a_59.png)

## 第四步：深入分析——散点图与分布
除了折线图，散点图矩阵是观察多变量关系及其各自分布的强大工具。在绘制之前，我们通常计算指标的日收益率（或对数收益率）以进行标准化分析。

以下是计算日对数收益率的代码：
```python
# 计算日对数收益率
returns = np.log(data / data.shift(1))
print(returns.head())
```

![](img/baa0f6a6e63f608417a14babde91d80a_61.png)

得到收益率数据后，我们可以使用Pandas的`scatter_matrix`函数绘制散点图矩阵。

![](img/baa0f6a6e63f608417a14babde91d80a_63.png)

以下是绘制散点图矩阵的步骤：
1.  导入必要的绘图库。
2.  调用`pd.plotting.scatter_matrix`函数。
3.  指定参数，如图形大小、对角线图形类型（直方图或核密度估计图）和散点图的透明度。

![](img/baa0f6a6e63f608417a14babde91d80a_65.png)

```python
# 绘制散点图矩阵
pd.plotting.scatter_matrix(returns,
                           alpha=0.2,          # 散点透明度
                           diagonal=‘kde’,     # 对角线绘制核密度估计图
                           figsize=(10, 10))
```

![](img/baa0f6a6e63f608417a14babde91d80a_67.png)

在这个矩阵图中：
*   **对角线**上的图是每个变量自身的分布情况（核密度估计图），它比直方图更平滑地展示了数据分布形态。
*   **非对角线**上的散点图展示了两两变量之间的关系。从`SPX`和`VIX`的散点图中，可以更清晰地看到数据点沿负相关方向分布的态势，这印证了我们从折线图中得到的观察。

![](img/baa0f6a6e63f608417a14babde91d80a_69.png)

![](img/baa0f6a6e63f608417a14babde91d80a_71.png)

![](img/baa0f6a6e63f608417a14babde91d80a_73.png)

## 第五步：量化关系——回归分析
可视化给出了直观印象，但我们还需要量化这种关系。这就是回归分析的任务——找到一条最佳拟合线（回归方程）来描述变量间的关联。

![](img/baa0f6a6e63f608417a14babde91d80a_75.png)

![](img/baa0f6a6e63f608417a14babde91d80a_77.png)

虽然本段教程未给出完整的回归计算代码，但其核心思想是：通过数学方法（如最小二乘法）拟合出形如 **`y = a * x + b`** 的线性方程，其中`a`是回归系数，量化了`x`变化时`y`的平均变化量。对于`SPX`和`VIX`，我们预期会得到一个负的回归系数`a`。

![](img/baa0f6a6e63f608417a14babde91d80a_79.png)

![](img/baa0f6a6e63f608417a14babde91d80a_81.png)

![](img/baa0f6a6e63f608417a14babde91d80a_83.png)

在后续学习中，你可以使用`statsmodels`或`scikit-learn`库来具体计算这个回归方程和相关系数，从而用精确的数字验证图表中观察到的负相关关系。

![](img/baa0f6a6e63f608417a14babde91d80a_85.png)

![](img/baa0f6a6e63f608417a14babde91d80a_87.png)

## 总结
本节课中我们一起学习了金融指标间的关系分析方法。
1.  **数据准备**：首先提取出需要分析的目标指标列。
2.  **可视化探索**：通过绘制**双Y轴折线图**，将不同量纲的指标放在同一图中对比趋势；通过**散点图矩阵**，同时观察各变量的分布及其两两关系。
3.  **核心洞察**：我们观察到`SPX`与`VIX`之间存在**负相关**关系，即市场下跌时恐慌情绪倾向于上升。
4.  **分析逻辑**：分析过程通常是迭代的：从简单图表开始，发现问题（如尺度不一），不断改进图表（如使用双Y轴、选取特定时间段、绘制散点图），最终通过回归分析进行量化验证。

![](img/baa0f6a6e63f608417a14babde91d80a_89.png)

![](img/baa0f6a6e63f608417a14babde91d80a_91.png)

掌握这些方法，你就能对金融数据中的复杂关系进行初步的探索和描述，为更深入的量化策略研究打下基础。