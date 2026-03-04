# 课程名称：第2讲：K均值聚类与朴素贝叶斯概览 📊

![](img/fbb651557ac3d4c700a1b601c2883fd7_1.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_3.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_5.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_7.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_9.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_11.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_13.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_15.png)

---

![](img/fbb651557ac3d4c700a1b601c2883fd7_17.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_19.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_21.png)

在本节课中，我们将学习**K均值聚类**的基本原理、收敛过程以及如何选择聚类数量；并简要了解在金融与文本分析中的一些应用场景。我们还会介绍**朴素贝叶斯**的核心思想、适用场景以及简单的文本分类示例。以下内容力求简单直白，便于初学者理解。

![](img/fbb651557ac3d4c700a1b601c2883fd7_23.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_25.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_27.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_29.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_31.png)

---

![](img/fbb651557ac3d4c700a1b601c2883fd7_33.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_35.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_37.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_39.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_41.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_43.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_45.png)

## 概述

![](img/fbb651557ac3d4c700a1b601c2883fd7_47.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_49.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_51.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_53.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_55.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_57.png)

上一节课我们学习了K均值聚类的基本思路，本节继续讲解其具体算法流程，并通过实例帮助理解。随后我们将引入朴素贝叶斯的基本定理及其在文本分析中的应用。核心概念以公式与代码形式呈现，帮助理解推理过程。

![](img/fbb651557ac3d4c700a1b601c2883fd7_59.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_61.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_63.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_65.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_67.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_69.png)

---

![](img/fbb651557ac3d4c700a1b601c2883fd7_71.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_73.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_75.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_77.png)

## 一、K均值聚类：基本原理与实现

![](img/fbb651557ac3d4c700a1b601c2883fd7_79.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_81.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_83.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_85.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_87.png)

上一节我们介绍了K均值聚类的目标，本节对算法的关键步骤进行梳理。

![](img/fbb651557ac3d4c700a1b601c2883fd7_89.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_91.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_93.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_95.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_97.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_99.png)

**以下是K均值聚类的核心步骤：**  

![](img/fbb651557ac3d4c700a1b601c2883fd7_101.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_103.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_105.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_107.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_109.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_111.png)

- 首先选择聚类数 \(K\)（聚类的数量）。  
- 随机选择 \(K\) 个点作为初始质心 \(\{\mu_1, \mu_2, \dots, \mu_K\}\)。  
- 对于每个数据点 \(x_i\)，计算其到各个质心的距离，归入距离最近的质心所对应的聚类。  
- 计算每个聚类的新的质心：  
  \[
  \mu_j = \frac{1}{|C_j|} \sum_{x_i \in C_j} x_i
  \]
  其中 \(C_j\) 是第 \(j\) 个聚类中的数据点集合，\(|C_j|\) 是该集合的大小。  
- 重复上述两步，直到收敛（聚类分配不再改变或质心变化极小）。

![](img/fbb651557ac3d4c700a1b601c2883fd7_113.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_115.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_117.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_119.png)

> 公式要点：  
> - 距离度量通常使用欧氏距离 \(\|x_i - \mu_j\|^2\)。  
> - 质心更新为聚类内点的均值。

![](img/fbb651557ac3d4c700a1b601c2883fd7_121.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_123.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_125.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_127.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_129.png)

**收敛与多组聚类的扩展**  
- 通过不断迭代，上述过程会收敛，得到最终的聚类划分及质心。  
- 也可以尝试 \(K=3\)、\(K=4\) 等，重复相同步骤，观察收敛结果。

![](img/fbb651557ac3d4c700a1b601c2883fd7_131.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_133.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_135.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_137.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_139.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_141.png)

![](img/fbb651557ac3d4c700a1b601c2883fd7_143.png)

**过渡到下一节的自然衔接