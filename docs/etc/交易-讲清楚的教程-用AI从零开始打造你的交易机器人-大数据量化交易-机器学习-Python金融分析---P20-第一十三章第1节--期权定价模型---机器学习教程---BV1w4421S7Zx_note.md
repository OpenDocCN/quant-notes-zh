# 量化交易：P20：第一十三章第1节： 期权定价模型 - 机器学习教程 📈

![](img/eacf87fa1ba436d8f4aef6d7870f83da_0.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_1.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_2.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_3.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_4.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_6.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_7.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_8.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_9.png)

在本节课中，我们将学习如何评估量化策略回测过程中的过度拟合风险。我们将介绍一个名为“组合对称交叉验证”的通用框架，用于计算策略回测发生过拟合的概率。此外，我们还将探讨如何使用Bootstrap和蒙特卡洛置换检验来评估单个策略参数的显著性。这些方法对于构建稳健的交易策略至关重要。

## 课程概述与常见问题

在策略开发过程中，我们常会遇到一些问题，这些问题可能导致回测结果过于乐观，无法在实盘中复现。

上一节我们介绍了策略开发的基本流程，本节中我们来看看具体有哪些常见的偏差和陷阱。

以下是几种常见的偏差类型：

*   **幸存者偏差**：在构建投资组合时，如果只选取目前仍然存活的基金或股票，而忽略了历史上已经退市或关闭的部分，就会高估整体表现。
*   **多空偏差**：在单边上涨的市场中，无论怎样优化，策略总会倾向于做多。这种数据集本身的不平衡会导致策略在优化后出现方向性偏差。
*   **未来函数**：在回测中使用了在实盘当时无法获得的信息。例如，在一个K线尚未走完时，就使用了该K线的最高价。
*   **端点效应**：在使用某些全局优化的信号处理方法时，引入最新的数据会改变历史状态，本质上类似于引入了未来信息。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_11.png)

## 回测过拟合概率框架

为了定量评估策略过拟合的风险，我们将学习一篇论文中提出的“回测过拟合概率”框架。该框架的核心思想是比较策略在样本内和样本外的表现排名。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_13.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_15.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_17.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_19.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_21.png)

### 核心概念与定义

![](img/eacf87fa1ba436d8f4aef6d7870f83da_23.png)

首先，我们需要理解两个核心概念：样本内和样本外。如果过度拟合发生，策略通常在样本内表现出最优性能，但在样本外表现会劣化。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_25.png)

该框架建立了一个概率空间来评估回测过拟合事件的概率。原假设是回测确实发生了过度拟合。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_27.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_29.png)

我们定义策略的表现度量，例如夏普比率。假设我们测试了N个策略，每个策略都能计算出一个夏普比率。我们用两个随机向量来表示这N个策略在样本内和样本外的表现：

![](img/eacf87fa1ba436d8f4aef6d7870f83da_31.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_33.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_35.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_37.png)

*   **R_in**: 样本内表现向量，长度为N。
*   **R_out**: 样本外表现向量，长度为N。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_39.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_41.png)

该框架的关键在于比较策略在样本内和样本外的**排名**，而非具体数值。

### 过拟合概率公式

回测过拟合的概率定义为：样本内最优参数的夏普比率，小于所有N组参数在样本外夏普比率中位数的概率。

用公式表示如下：

`PBO = P( rank(R_out[argmax(R_in)]) <= N/2 )`

![](img/eacf87fa1ba436d8f4aef6d7870f83da_43.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_45.png)

其中，`rank()` 函数返回该策略夏普比率在样本外所有策略中的排名（从最好到最差排序）。`argmax(R_in)` 是样本内表现最好的策略索引。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_46.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_47.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_48.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_49.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_50.png)

**公式解读**：如果我们在样本内选出的最优策略，在样本外的表现排名低于中位数（即比一半的策略都差），我们就认为过拟合可能发生了。PBO就是这个事件发生的概率。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_52.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_54.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_56.png)

## 组合对称交叉验证框架

为了估计上述的PBO，论文提出了“组合对称交叉验证”框架。接下来，我们一步步拆解这个框架的实现过程。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_58.png)

### 第一步：生成回测矩阵

首先，我们需要对多组策略参数进行回测，生成一个收益率矩阵。

我们以沪深300指数的双均线策略为例。策略规则是：当短期均线上穿长期均线时做多，下穿时平仓。我们随机生成了1000组不同的长短周期参数组合，对每组参数进行回测，得到每日收益率序列。

最终，我们得到一个维度为 `T × N` 的矩阵 `M`：
*   `T`: 回测周期的天数。
*   `N`: 测试的策略参数组数量（例如1000）。
*   矩阵的每一列代表一组参数完整的日收益率序列。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_60.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_62.png)

### 第二步：分割数据矩阵

我们将矩阵 `M` 按行（即时间维度）分割成 `S` 个互不相交的子矩阵。`S` 必须是一个偶数。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_64.png)

例如，将2990天的数据分成 `S=10` 组，则每组大约有299天。我们去掉不能被 `S` 整除的余数数据，保证每组大小相同。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_66.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_68.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_70.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_72.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_74.png)

### 第三步：生成组合并划分训练集/测试集

![](img/eacf87fa1ba436d8f4aef6d7870f83da_76.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_78.png)

从这 `S` 组中，我们随机选取 `S/2` 组作为训练集，剩下的 `S/2` 组作为测试集。这种选取方式总共有 `C(S, S/2)` 种组合。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_80.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_82.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_84.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_86.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_88.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_90.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_92.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_94.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_96.png)

对于每一种组合：
1.  将选中的 `S/2` 组数据**按原始时间顺序拼接**，作为训练集 `J`。
2.  将剩下的 `S/2` 组数据拼接，作为测试集 `J_bar`。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_98.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_100.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_102.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_104.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_106.png)

### 第四步：计算表现与排名

![](img/eacf87fa1ba436d8f4aef6d7870f83da_108.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_110.png)

对于当前划分的训练集 `J` 和测试集 `J_bar`：
1.  计算训练集 `J` 上所有 `N` 组策略的夏普比率，得到向量 `R_in`。
2.  计算测试集 `J_bar` 上所有 `N` 组策略的夏普比率，得到向量 `R_out`。
3.  将 `R_in` 和 `R_out` 分别转化为排名向量 `r_in` 和 `r_out`（排名从1到N，1代表最好）。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_112.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_114.png)

### 第五步：计算相对排名与统计量

1.  找出训练集中表现最好的策略索引：`n* = argmax(R_in)`。
2.  查看该策略在测试集中的排名：`rank_out = r_out[n*]`。
3.  计算相对排名 `ω`：`ω = (rank_out) / (N + 1)`。这个值在0到1之间，表示最优策略在样本外打败了多少比例的策略。
4.  计算对数优势比 `λ`：`λ = log( ω / (1 - ω) )`。
    *   如果 `ω > 0.5`（样本外排名在前50%），则 `λ > 0`。
    *   如果 `ω < 0.5`（样本外排名在后50%），则 `λ < 0`。
    *   `λ` 越大，说明样本内与样本外表现越一致。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_116.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_118.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_119.png)

### 第六步：计算过拟合概率

![](img/eacf87fa1ba436d8f4aef6d7870f83da_121.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_123.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_125.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_127.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_129.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_131.png)

遍历所有可能的组合（共 `C(S, S/2)` 个），我们会得到一系列 `λ` 值。这些 `λ` 值构成了一个经验分布。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_133.png)

**回测过拟合概率PBO** 就是 `λ` 的分布中，`λ < 0` 的部分所占的比例。即：

![](img/eacf87fa1ba436d8f4aef6d7870f83da_135.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_137.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_138.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_140.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_142.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_144.png)

`PBO = (Number of λ < 0) / (Total number of λ)`

![](img/eacf87fa1ba436d8f4aef6d7870f83da_146.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_148.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_150.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_152.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_154.png)

这个比例越高，说明我们测试的策略家族过度拟合历史数据的风险越大。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_156.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_158.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_160.png)

## Bootstrap方法评估策略显著性

![](img/eacf87fa1ba436d8f4aef6d7870f83da_162.png)

CSCV框架评估的是**策略家族**的过拟合概率。如果我们想评估**某一组特定参数**的回测结果是否显著，可以使用Bootstrap方法。

Bootstrap的核心思想是通过对原始数据（如沪深300收益率）进行有放回的重采样，来模拟统计量的抽样分布，进而计算p值。

### 步骤简介

1.  **计算策略表现**：计算待评估策略的日平均收益率 `μ_strategy`。
2.  **重采样**：从沪深300的日收益率序列中，有放回地随机抽取 `T` 个样本（`T`为回测天数），构成一个新的收益率序列。
3.  **计算基准表现**：计算这个新序列的平均收益率 `μ_bootstrap`。这模拟了在“策略无预测能力”（即收益源于市场本身）的假设下，可能得到的平均收益。
4.  **重复模拟**：将步骤2和3重复大量次数（如10000次），得到一系列 `μ_bootstrap`，形成抽样分布。
5.  **计算p值**：计算 `μ_strategy` 在这个Bootstrap分布中的位置。如果 `μ_strategy` 远大于分布的大部分值，则p值很小，说明策略收益显著优于随机情况；反之，则可能不显著。

**注意**：由于A股市场长期存在上涨偏差，在Bootstrap前需要对市场收益率序列进行“去偏”处理，例如构建一个对称的序列，以确保零假设是“策略无超额收益”。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_164.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_166.png)

## 蒙特卡洛置换检验

蒙特卡洛置换检验是另一种评估策略显著性的方法，它通过打乱数据间的关联来构建零假设下的分布。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_168.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_170.png)

### 与Bootstrap的区别

*   **Bootstrap**：对**市场收益率**进行重采样，检验策略收益是否显著高于随机市场收益。
*   **置换检验**：保持**策略信号序列**不变，随机打乱**市场收益率序列**与信号序列的对应关系，检验策略收益是否显著高于随机配对下的收益。

### 步骤简介

![](img/eacf87fa1ba436d8f4aef6d7870f83da_172.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_174.png)

1.  **准备数据**：对于待评估的N组策略，收集每组的每日仓位信号和对应的市场收益率。
2.  **随机配对**：**对所有策略采用相同的随机排列**，将市场收益率序列整体打乱顺序，然后与固定的策略仓位信号序列重新配对。
3.  **计算新收益**：根据新的配对关系，计算每一组策略在新的市场收益率下的日收益序列，并计算其平均收益 `μ_perm`。
4.  **选择最优**：从这N组新的策略收益中，选出平均收益最高的那个值 `max(μ_perm)`。
5.  **重复模拟**：重复步骤2-4大量次数，得到一系列 `max(μ_perm)`，这构成了在“策略信号与市场收益无关”的零假设下，“最佳策略”的平均收益分布。
6.  **计算p值**：将原始回测中最佳策略的 `μ_strategy` 与这个置换检验得到的分布进行比较。如果 `μ_strategy` 远高于置换分布中的大部分值，则p值很小，说明原始策略的优异表现很难用随机性解释，可能包含了过度优化。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_176.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_178.png)

## 总结与最佳实践

本节课中我们一起学习了三种评估回测过拟合风险的方法：

1.  **CSCV框架**：用于评估一个**策略家族**（由多组参数构成）的过拟合概率。
2.  **Bootstrap检验**：用于评估**单一策略**的收益是否显著优于随机情况。
3.  **蒙特卡洛置换检验**：用于评估**单一策略**的优异表现是否可能源于随机性。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_180.png)

在实际策略开发中，我们建议遵循以下流程以降低过拟合风险：

![](img/eacf87fa1ba436d8f4aef6d7870f83da_182.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_184.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_186.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_188.png)

*   **先有逻辑，再做验证**：从市场观察和经济逻辑出发构建策略，而非纯粹的数据挖掘。
*   **严格区分样本**：始终保留一部分数据作为样本外测试集，绝不用于参数优化。
*   **进行推进分析**：采用滚动时间窗口进行参数优化和测试，模拟策略在实盘中的逐步更新过程。
*   **关注参数稳定性**：选择参数敏感度低的区域，确保策略在参数小幅变动时表现稳健。
*   **实盘验证**：上线前进行模拟盘交易，对比实盘结果与回测结果的一致性。

![](img/eacf87fa1ba436d8f4aef6d7870f83da_190.png)

![](img/eacf87fa1ba436d8f4aef6d7870f83da_191.png)

通过结合使用本节课介绍的定量评估方法和严谨的开发流程，我们可以更有信心地开发出能够在实盘中持续稳健运行的量化交易策略。