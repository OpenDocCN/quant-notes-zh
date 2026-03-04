# 多因子量化选股与机器学习：四：因子滚动筛选 📊

![](img/5c106132708e7f5053abf23659aedfa9_1.png)

![](img/5c106132708e7f5053abf23659aedfa9_3.png)

在本节课中，我们将学习多因子量化选股流程中的一个核心步骤：因子滚动筛选。我们将解析如何从众多候选因子中，筛选出对预测未来股票收益率最有效的因子组合。

![](img/5c106132708e7f5053abf23659aedfa9_5.png)

上一节我们介绍了如何计算因子的IC和ICIR值来评估其预测能力。本节中，我们来看看如何基于这些评估指标，动态地、滚动地筛选出每一期用于构建选股模型的因子。

![](img/5c106132708e7f5053abf23659aedfa9_7.png)

![](img/5c106132708e7f5053abf23659aedfa9_9.png)

## 数据准备与回顾

![](img/5c106132708e7f5053abf23659aedfa9_11.png)

首先，我们导入经过预处理的数据。这些数据包含了多个因子（如换手率、市净率、市现率、市值、毛利率等）以及对应的未来收益率。这些因子值已经完成了**中性化处理**（例如，对市值和行业进行中性化）和标准化等预处理步骤，不再是原始数据。

![](img/5c106132708e7f5053abf23659aedfa9_13.png)

![](img/5c106132708e7f5053abf23659aedfa9_15.png)

```python
# 示例：导入预处理后的因子数据
# data 是一个DataFrame，包含多列因子值和一列未来收益率
```

![](img/5c106132708e7f5053abf23659aedfa9_17.png)

![](img/5c106132708e7f5053abf23659aedfa9_19.png)

我们有众多因子，但并非所有因子都适合用于每一期的选股。因此，需要进行因子筛选，以确定哪些因子应作为当期选股的重要依据。

![](img/5c106132708e7f5053abf23659aedfa9_21.png)

## 因子筛选的核心逻辑

![](img/5c106132708e7f5053abf23659aedfa9_23.png)

我们的筛选过程分为两个主要步骤：

1.  **基于IC值的初筛**：计算每个因子在历史窗口期内的平均IC值，筛选出IC值大于设定阈值（例如0.03）的因子。
2.  **基于相关性的精筛**：在初筛得到的因子集合中，计算任意两个因子之间的相关系数。如果两个因子的相关系数过高（例如大于0.8），则剔除其中IC值较低的那个，以降低因子间的共线性。

以下是该逻辑的简要代码框架：

![](img/5c106132708e7f5053abf23659aedfa9_25.png)

```python
# 1. 设定滚动窗口（例如过去12个月）
historical_window = 12

![](img/5c106132708e7f5053abf23659aedfa9_27.png)

# 2. 在窗口内计算每个因子的IC值序列
ic_series = calculate_ic(data, window=historical_window)

![](img/5c106132708e7f5053abf23659aedfa9_29.png)

![](img/5c106132708e7f5053abf23659aedfa9_31.png)

# 3. 计算每个因子的平均IC值，并筛选出大于阈值的因子
threshold_ic = 0.03
selected_by_ic = ic_series.mean() > threshold_ic
initial_factors = selected_by_ic[selected_by_ic].index.tolist()

![](img/5c106132708e7f5053abf23659aedfa9_33.png)

![](img/5c106132708e7f5053abf23659aedfa9_35.png)

# 4. 计算初筛因子间的相关系数矩阵
corr_matrix = data[initial_factors].corr()

![](img/5c106132708e7f5053abf23659aedfa9_37.png)

# 5. 剔除高相关性因子中IC值较低者
final_factors = remove_high_correlation(initial_factors, corr_matrix, ic_series.mean(), threshold_corr=0.8)
```

![](img/5c106132708e7f5053abf23659aedfa9_39.png)

![](img/5c106132708e7f5053abf23659aedfa9_41.png)

![](img/5c106132708e7f5053abf23659aedfa9_43.png)

## 代码逐步解析

![](img/5c106132708e7f5053abf23659aedfa9_45.png)

现在，让我们深入代码的每个部分。

### 步骤一：确定滚动计算窗口

![](img/5c106132708e7f5053abf23659aedfa9_47.png)

![](img/5c106132708e7f5053abf23659aedfa9_49.png)

我们采用滚动窗口的方式进行因子筛选。例如，在决定2021年8月使用哪些因子时，我们会回看过去一段时间（如11个月）的数据进行计算。

![](img/5c106132708e7f5053abf23659aedfa9_51.png)

```python
# 假设当前月份为 current_month
lookback_months = 11
start_date = current_month - pd.DateOffset(months=lookback_months)
window_data = data[(data[‘date’] >= start_date) & (data[‘date’] < current_month)]
```
这段代码截取了从`start_date`到`current_month`之前的数据作为计算窗口。窗口长度可以根据需要调整。

![](img/5c106132708e7f5053abf23659aedfa9_53.png)

![](img/5c106132708e7f5053abf23659aedfa9_55.png)

### 步骤二：计算窗口内各因子的IC值

![](img/5c106132708e7f5053abf23659aedfa9_57.png)

![](img/5c106132708e7f5053abf23659aedfa9_59.png)

在窗口数据`window_data`中，我们需要计算每一个因子与未来收益率之间的**秩相关系数**，即IC值。

![](img/5c106132708e7f5053abf23659aedfa9_61.png)

![](img/5c106132708e7f5053abf23659aedfa9_62.png)

```python
def calculate_ic_for_window(window_data):
    ic_results = {}
    # 假设最后一列是未来收益率 ‘return’
    future_return = window_data.iloc[:, -1]
    for factor in window_data.columns[:-1]: # 遍历所有因子列
        factor_values = window_data[factor]
        # 计算斯皮尔曼秩相关系数
        ic = factor_values.corr(future_return, method=‘spearman’)
        ic_results[factor] = ic
    return pd.Series(ic_results)
```
此函数会输出一个序列，索引为因子名，值为该因子在**这个窗口期**内计算出的单一IC值。在实际滚动筛选中，我们通常计算的是**多期IC值的均值**。

![](img/5c106132708e7f5053abf23659aedfa9_64.png)

### 步骤三：基于平均IC值进行初筛

![](img/5c106132708e7f5053abf23659aedfa9_66.png)

在真实的滚动筛选中，我们会在每个时间点`t`，都使用`t`之前的一段历史窗口数据，计算每个因子在该窗口内每一期的IC值，然后求其平均值。

![](img/5c106132708e7f5053abf23659aedfa9_68.png)

![](img/5c106132708e7f5053abf23659aedfa9_70.png)

```python
# 假设我们有一个函数能计算每个因子在历史各期的IC值序列
# ic_series_by_factor 是一个DataFrame，索引为日期，列为因子，值为当期的IC值
average_ic = ic_series_by_factor.mean() # 计算每个因子在所有历史期内的平均IC值
selected_factors = average_ic[average_ic > 0.03].index.tolist()
```
这样，我们就得到了**平均IC值**超过0.03的因子初选列表。

### 步骤四：基于因子间相关性进行精筛

![](img/5c106132708e7f5053abf23659aedfa9_72.png)

![](img/5c106132708e7f5053abf23659aedfa9_74.png)

得到初选因子列表后，我们需要检查它们之间的相关性。以下是精筛过程的描述：

![](img/5c106132708e7f5053abf23659aedfa9_76.png)

首先，计算初选因子在窗口期内的**相关系数矩阵**。
```python
factor_data = window_data[selected_factors]
corr_matrix = factor_data.corr()
```
接着，遍历相关系数矩阵的上三角部分（避免重复比较），找出相关系数超过阈值（如0.8）的因子对。
对于每一对高相关因子，比较它们的平均IC值，并剔除平均IC值较低的那个因子。

![](img/5c106132708e7f5053abf23659aedfa9_78.png)

![](img/5c106132708e7f5053abf23659aedfa9_80.png)

这个过程确保了最终入选的因子集合，不仅单因子预测能力较强（IC值高），而且因子之间提供的信息重叠度较低。

![](img/5c106132708e7f5053abf23659aedfa9_82.png)

![](img/5c106132708e7f5053abf23659aedfa9_84.png)

### 步骤五：存储与滚动应用

![](img/5c106132708e7f5053abf23659aedfa9_86.png)

![](img/5c106132708e7f5053abf23659aedfa9_88.png)

上述筛选过程会在每个预定的时间点（例如每季度初）重复执行。每次筛选后，将得到的因子列表与该时间点对应存储。

![](img/5c106132708e7f5053abf23659aedfa9_90.png)

![](img/5c106132708e7f5053abf23659aedfa9_92.png)

```python
# 示例：存储筛选结果
selection_date = current_month # 例如 ‘2021-08-01’
selected_factors_dict[selection_date] = final_factors
```
最终，我们得到一个时间序列字典，记录了历史上每个调仓时点所使用的最优因子组合。这个动态的因子列表将用于后续的**因子加权**和**股票打分**回测阶段。

![](img/5c106132708e7f5053abf23659aedfa9_94.png)

## 总结

![](img/5c106132708e7f5053abf23659aedfa9_96.png)

![](img/5c106132708e7f5053abf23659aedfa9_98.png)

本节课中我们一起学习了因子滚动筛选的完整流程。

![](img/5c106132708e7f5053abf23659aedfa9_100.png)

![](img/5c106132708e7f5053abf23659aedfa9_102.png)

1.  **核心目标**：从大量因子中，动态筛选出当期最有效的、互补的因子子集。
2.  **筛选双步骤**：
    *   **初筛看预测力**：使用历史窗口数据计算因子的平均**IC值**，筛选出预测能力稳定且较强的因子（`IC均值 > 阈值`）。
    *   **精筛去冗余**：计算初筛因子间的**相关系数**，剔除高相关因子对中预测能力较弱的一方，确保因子组合的信息多样性。
3.  **滚动机制**：上述过程在每一个预定的调仓时点（如季度初）重复进行，使用截至该时点的最新历史数据，确保因子选择能适应市场变化。

![](img/5c106132708e7f5053abf23659aedfa9_104.png)

![](img/5c106132708e7f5053abf23659aedfa9_106.png)

通过本节的筛选，我们为下一阶段——**使用筛选后的因子构建综合评分模型并进行回测**——打下了坚实的基础。