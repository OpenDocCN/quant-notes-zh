# Python多因子量化选股全流程：P4：因子滚动筛选 📊

![](img/febc0cb302eb8246082c73f64e4ad87a_1.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_3.png)

在本节课中，我们将学习多因子选股流程中的关键一步：因子滚动筛选。我们将了解如何从众多候选因子中，挑选出对预测未来股票收益最有效且彼此独立的因子，为后续的模型构建和回测打下坚实基础。

![](img/febc0cb302eb8246082c73f64e4ad87a_5.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_7.png)

上一节我们介绍了如何计算因子的IC值和ICIR值来评估其预测能力。本节中，我们来看看如何基于这些评估指标，结合相关性分析，进行系统的因子筛选。

![](img/febc0cb302eb8246082c73f64e4ad87a_9.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_11.png)

## 数据准备 📂

![](img/febc0cb302eb8246082c73f64e4ad87a_13.png)

首先，我们需要导入经过预处理的因子数据。这些数据已经完成了中性化（如市值、行业中性化）和标准化处理，不再是原始值。

![](img/febc0cb302eb8246082c73f64e4ad87a_15.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_17.png)

```python
# 示例：导入预处理后的因子数据
import pandas as pd
factor_data = pd.read_csv('processed_factor_data.csv')
```

![](img/febc0cb302eb8246082c73f64e4ad87a_19.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_21.png)

数据中包含多个因子，例如换手率、市净率、市现率、市值、毛利率等。每个因子都对应着未来一期的股票收益率。

![](img/febc0cb302eb8246082c73f64e4ad87a_23.png)

## 核心筛选逻辑 ⚙️

因子筛选的核心目标是：选出**预测能力强**（IC值高）且**彼此独立性高**（相关性低）的因子。我们采用两轮筛选法。

### 第一轮筛选：基于IC值

![](img/febc0cb302eb8246082c73f64e4ad87a_25.png)

我们使用**信息系数（IC）** 作为首要筛选标准。IC值是当期因子值与下期收益率之间的秩相关系数，衡量因子的预测能力。

![](img/febc0cb302eb8246082c73f64e4ad87a_27.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_29.png)

**计算公式**：
对于每个因子 `f` 和未来收益率 `r`，在时间窗口 `t` 内的IC值计算为：
`IC_t = corr( factor_rank_t, return_rank_{t+1} )`

![](img/febc0cb302eb8246082c73f64e4ad87a_31.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_33.png)

筛选步骤如下：

![](img/febc0cb302eb8246082c73f64e4ad87a_35.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_37.png)

1.  **确定回望期**：在每个月末进行选股时，我们回望过去一段时间（例如11个月）的数据。
2.  **计算滚动IC值**：对回望期内的每一个月，计算每个因子与该月未来收益率的IC值。
3.  **计算平均IC值**：对每个因子，计算其在回望期内所有月份IC值的平均值。
4.  **设定阈值筛选**：保留平均IC值大于设定阈值（例如 **IC_mean > 0.03**）的因子。这确保了所选因子具有稳定且显著的正向预测能力。

![](img/febc0cb302eb8246082c73f64e4ad87a_39.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_41.png)

以下是关键代码逻辑示意：

![](img/febc0cb302eb8246082c73f64e4ad87a_43.png)

```python
# 假设 factor_data 包含因子列和名为‘next_return’的未来收益率列
lookback_period = 11  # 回望11个月
threshold_ic = 0.03

![](img/febc0cb302eb8246082c73f64e4ad87a_45.png)

# 计算每个因子在回望期内的平均IC值
def calculate_mean_ic(data, lookback):
    ic_series = {}
    for factor in factor_columns: # factor_columns 是所有因子列名的列表
        # 分组计算每个时间点（月）该因子与未来收益率的相关系数
        ic = data.groupby('date').apply(lambda x: x[factor].corr(x['next_return']))
        # 取最近 lookback 个月的平均值
        mean_ic = ic.tail(lookback).mean()
        ic_series[factor] = mean_ic
    return pd.Series(ic_series)

![](img/febc0cb302eb8246082c73f64e4ad87a_47.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_49.png)

mean_ic_series = calculate_mean_ic(factor_data, lookback_period)
# 第一轮筛选：选择平均IC大于阈值的因子
selected_factors_round1 = mean_ic_series[mean_ic_series > threshold_ic].index.tolist()
```

![](img/febc0cb302eb8246082c73f64e4ad87a_51.png)

### 第二轮筛选：基于因子间相关性

![](img/febc0cb302eb8246082c73f64e4ad87a_53.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_55.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_57.png)

经过第一轮筛选后，因子池中可能仍存在高度相关的因子。使用高度相关的多个因子等同于重复使用同一信息，可能导致模型过拟合，且无法增加信息量。因此，我们需要剔除相关性过高的因子。

![](img/febc0cb302eb8246082c73f64e4ad87a_59.png)

筛选步骤如下：

![](img/febc0cb302eb8246082c73f64e4ad87a_61.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_63.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_65.png)

1.  **计算因子间相关系数矩阵**：针对第一轮筛选出的因子，计算它们在回望期内的平均截面相关系数矩阵。
2.  **设定相关性阈值**：设定一个阈值（例如 **corr > 0.8**）来判断因子是否高度相关。
3.  **迭代剔除**：在高度相关的因子对中，保留IC值较高的那个，剔除IC值较低的那个。

![](img/febc0cb302eb8246082c73f64e4ad87a_67.png)

以下是关键代码逻辑示意：

![](img/febc0cb302eb8246082c73f64e4ad87a_69.png)

```python
def filter_high_correlation(factors_list, data, lookback, threshold_corr=0.8):
    selected_factors = factors_list.copy()
    # 计算选定因子在回望期内的平均相关系数矩阵
    corr_matrix = data[selected_factors].tail(lookback * 30).corr().abs() # 假设日度数据，取最近 lookback*30 天
    # 遍历上三角矩阵，寻找高相关性因子对
    for i in range(len(selected_factors)):
        for j in range(i+1, len(selected_factors)):
            factor_i, factor_j = selected_factors[i], selected_factors[j]
            if corr_matrix.loc[factor_i, factor_j] > threshold_corr:
                # 如果两个因子高度相关，则剔除IC值较低的那个
                ic_i = mean_ic_series[factor_i]
                ic_j = mean_ic_series[factor_j]
                factor_to_remove = factor_i if ic_i < ic_j else factor_j
                if factor_to_remove in selected_factors:
                    selected_factors.remove(factor_to_remove)
    return selected_factors

selected_factors_final = filter_high_correlation(selected_factors_round1, factor_data, lookback_period)
```

![](img/febc0cb302eb8246082c73f64e4ad87a_71.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_73.png)

## 滚动筛选实践 🔄

![](img/febc0cb302eb8246082c73f64e4ad87a_75.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_77.png)

在实际应用中，因子筛选不是一劳永逸的。因子的有效性可能会随着市场环境变化而改变（即因子衰减）。因此，我们需要定期（例如每季度）重复上述滚动筛选过程，动态更新用于选股的因子列表。

![](img/febc0cb302eb8246082c73f64e4ad87a_79.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_81.png)

流程如下：
1.  在每个季度末，回望过去一段时间（如一年）的数据。
2.  执行上述两轮筛选，得到该时间点最新的有效因子列表。
3.  将该因子列表存储下来，用于下一个季度的选股模型构建。
4.  时间滚动到下一个季度，重复步骤1-3。

![](img/febc0cb302eb8246082c73f64e4ad87a_83.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_85.png)

最终，我们会得到一个按时间索引的因子列表字典或DataFrame，记录了历史上每个筛选时点所使用的因子集合。

![](img/febc0cb302eb8246082c73f64e4ad87a_87.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_89.png)

```python
# 示例输出结构
selected_factors_history = {
    ‘2022-01-31’: [‘factor_A‘， ’factor_B‘， ’factor_D‘],
    ‘2022-04-30’: [‘factor_B‘， ’factor_C‘， ’factor_E‘， ’factor_F‘],
    ‘2022-07-31’: [‘factor_A‘， ’factor_C‘， ’factor_F‘],
    # ...
}
```

![](img/febc0cb302eb8246082c73f64e4ad87a_91.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_93.png)

## 总结 📝

![](img/febc0cb302eb8246082c73f64e4ad87a_95.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_97.png)

本节课中我们一起学习了多因子量化选股中的因子滚动筛选方法。

![](img/febc0cb302eb8246082c73f64e4ad87a_99.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_101.png)

*   **核心目标**：从大量因子中筛选出**有效**（高IC）且**独立**（低相关性）的因子子集。
*   **两轮筛选法**：
    1.  **基于IC值**：保留历史平均IC值超过阈值（如0.03）的因子，确保预测能力。
    2.  **基于相关性**：在剩余因子中，剔除两两间相关系数过高（如大于0.8）的因子，避免信息冗余和多重共线性，优先保留IC值更高的因子。
*   **滚动实施**：因子有效性会随时间变化，因此需要定期（如每季度）重复筛选过程，实现因子的动态更新与轮动。

![](img/febc0cb302eb8246082c73f64e4ad87a_103.png)

![](img/febc0cb302eb8246082c73f64e4ad87a_105.png)

通过本节的筛选，我们得到了一个纯净、有效的因子池。在接下来的课程中，我们将利用这些筛选后的因子，构建具体的选股模型并进行回测验证。