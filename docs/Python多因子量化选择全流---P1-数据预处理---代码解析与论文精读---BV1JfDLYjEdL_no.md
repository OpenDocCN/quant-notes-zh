# Python多因子量化选股全流程：P1：数据预处理 📊

![](img/c2315df7b7420729fb303cd355b101d3_1.png)

![](img/c2315df7b7420729fb303cd355b101d3_3.png)

在本节课中，我们将学习量化多因子选股流程的第一步：数据预处理。数据预处理是后续所有分析（包括机器学习预测和投资组合构建）的基础，其质量直接影响最终的投资收益。本节课将详细讲解数据预处理的四个核心步骤，并通过Python代码进行演示。

![](img/c2315df7b7420729fb303cd355b101d3_5.png)

![](img/c2315df7b7420729fb303cd355b101d3_7.png)

## 概述

![](img/c2315df7b7420729fb303cd355b101d3_9.png)

![](img/c2315df7b7420729fb303cd355b101d3_11.png)

数据预处理旨在将原始因子数据转化为干净、可比、可用于建模的格式。它主要包含四个部分：缺失值填充、异常值处理、中性化和标准化。我们将逐一解析每个步骤的原理与实现。

![](img/c2315df7b7420729fb303cd355b101d3_13.png)

![](img/c2315df7b7420729fb303cd355b101d3_15.png)

## 数据预处理的核心步骤

![](img/c2315df7b7420729fb303cd355b101d3_17.png)

![](img/c2315df7b7420729fb303cd355b101d3_19.png)

![](img/c2315df7b7420729fb303cd355b101d3_21.png)

上一节我们介绍了数据预处理的重要性，本节中我们来看看具体的四个步骤。

![](img/c2315df7b7420729fb303cd355b101d3_23.png)

![](img/c2315df7b7420729fb303cd355b101d3_25.png)

![](img/c2315df7b7420729fb303cd355b101d3_27.png)

以下是数据预处理的四个核心部分：

![](img/c2315df7b7420729fb303cd355b101d3_29.png)

1.  **缺失值填充**：针对数据中的空值（NaN）进行处理。常见的填充方式有零填充、中位数填充和均值填充。
2.  **异常值处理**：为数据设定合理的上下限。当数据值超过上限时，将其替换为上限值；当数据值低于下限时，将其替换为下限值。这种方法也称为“中位数去极值”。
3.  **中性化**：使用市值和行业指标对原始因子值进行回归，取回归的残差作为新的因子值。目的是消除因子在市值和行业上的暴露，使其更具可比性。公式可表示为：`残差 = 因子值 - (β1 * 市值 + β2 * 行业哑变量 + 截距)`。
4.  **标准化**：将因子值转化为均值为0、标准差为1的标准正态分布。常用方法是Z-Score标准化，公式为：`标准化值 = (因子值 - 均值) / 标准差`。

![](img/c2315df7b7420729fb303cd355b101d3_31.png)

![](img/c2315df7b7420729fb303cd355b101d3_33.png)

## Python代码实践

![](img/c2315df7b7420729fb303cd355b101d3_35.png)

![](img/c2315df7b7420729fb303cd355b101d3_37.png)

![](img/c2315df7b7420729fb303cd355b101d3_39.png)

接下来，我们将通过Python代码演示如何实现上述预处理步骤。

![](img/c2315df7b7420729fb303cd355b101d3_41.png)

![](img/c2315df7b7420729fb303cd355b101d3_43.png)

### 1. 导入数据与基础设置

![](img/c2315df7b7420729fb303cd355b101d3_45.png)

![](img/c2315df7b7420729fb303cd355b101d3_47.png)

![](img/c2315df7b7420729fb303cd355b101d3_49.png)

首先，导入必要的库并加载因子数据。为了提高读取效率，数据通常保存为`.pkl`格式。

![](img/c2315df7b7420729fb303cd355b101d3_51.png)

![](img/c2315df7b7420729fb303cd355b101d3_53.png)

```python
import pandas as pd
import numpy as np
import statsmodels.api as sm

![](img/c2315df7b7420729fb303cd355b101d3_55.png)

![](img/c2315df7b7420729fb303cd355b101d3_57.png)

# 设置中文字体显示（如需要）
# plt.rcParams[‘font.sans-serif‘] = [‘SimHei‘]

![](img/c2315df7b7420729fb303cd355b101d3_58.png)

![](img/c2315df7b7420729fb303cd355b101d3_60.png)

![](img/c2315df7b7420729fb303cd355b101d3_62.png)

# 读取因子数据（.pkl格式读取速度快）
factor_data = pd.read_pickle(‘factor_data.pkl‘)
print(f“数据形状：{factor_data.shape}“)
print(factor_data.head())
```
本例中使用的数据时间范围为2011年1月至2022年1月，包含约62万条记录和20多个因子（如市净率、市盈率、销售净利率等）。数据来源于Wind金融终端。

![](img/c2315df7b7420729fb303cd355b101d3_64.png)

![](img/c2315df7b7420729fb303cd355b101d3_66.png)

### 2. 异常值处理与缺失值填充

![](img/c2315df7b7420729fb303cd355b101d3_68.png)

![](img/c2315df7b7420729fb303cd355b101d3_70.png)

![](img/c2315df7b7420729fb303cd355b101d3_72.png)

以下是异常值处理（中位数去极值）和缺失值填充（中位数填充）的代码实现。

![](img/c2315df7b7420729fb303cd355b101d3_74.png)

![](img/c2315df7b7420729fb303cd355b101d3_76.png)

```python
def winsorize_and_fill(data):
    “““对数据进行去极值和填充处理“““
    processed_data = data.copy()
    # 对每个因子列进行操作（假设从第2列开始是因子值）
    for col in processed_data.columns[2:]:
        # 1. 中位数去极值：设定上下限为5%和95%分位数
        median = processed_data[col].median()
        std = processed_data[col].std()
        upper_limit = median + 3 * std
        lower_limit = median - 3 * std
        processed_data[col] = processed_data[col].clip(lower=lower_limit, upper=upper_limit)

        # 2. 缺失值填充：使用中位数填充
        processed_data[col].fillna(processed_data[col].median(), inplace=True)
    return processed_data

![](img/c2315df7b7420729fb303cd355b101d3_78.png)

![](img/c2315df7b7420729fb303cd355b101d3_80.png)

![](img/c2315df7b7420729fb303cd355b101d3_82.png)

# 应用处理函数
factor_data_processed = winsorize_and_fill(factor_data)
```
**代码解析**：
*   `clip()` 函数用于将超出`[lower_limit, upper_limit]`范围的值替换为边界值。
*   `fillna()` 函数用指定值（此处为中位数）填充该列的缺失值。

![](img/c2315df7b7420729fb303cd355b101d3_84.png)

![](img/c2315df7b7420729fb303cd355b101d3_86.png)

### 3. 数据筛选（可选）

![](img/c2315df7b7420729fb303cd355b101d3_88.png)

![](img/c2315df7b7420729fb303cd355b101d3_90.png)

![](img/c2315df7b7420729fb303cd355b101d3_92.png)

在实际操作中，有时需要剔除特殊股票（如ST股）。以下是示例代码：

![](img/c2315df7b7420729fb303cd355b101d3_94.png)

![](img/c2315df7b7420729fb303cd355b101d3_96.png)

![](img/c2315df7b7420729fb303cd355b101d3_98.png)

```python
# 可选步骤：剔除ST股票
def filter_st_stocks(data, stock_name_col=‘股票名称‘):
    “““筛选出名称中包含‘ST‘的股票“““
    st_mask = data[stock_name_col].str.contains(‘ST‘)
    st_stocks = data[st_mask]
    data_cleaned = data[~st_mask].copy() # 删除ST股
    print(f“剔除了 {len(st_stocks)} 只ST股票“)
    return data_cleaned

![](img/c2315df7b7420729fb303cd355b101d3_100.png)

# 如果需要进行筛选，取消下一行的注释
# factor_data_processed = filter_st_stocks(factor_data_processed)
```

![](img/c2315df7b7420729fb303cd355b101d3_102.png)

![](img/c2315df7b7420729fb303cd355b101d3_104.png)

### 4. 行业数据合并与中性化

![](img/c2315df7b7420729fb303cd355b101d3_106.png)

中性化是预处理的核心，需要行业和市值数据。首先合并行业信息。

![](img/c2315df7b7420729fb303cd355b101d3_108.png)

![](img/c2315df7b7420729fb303cd355b101d3_110.png)

```python
# 导入行业数据
industry_data = pd.read_pickle(‘industry_data.pkl‘)
# 假设通过‘股票代码‘和‘日期‘列进行合并
merged_data = pd.merge(factor_data_processed, industry_data, on=[‘股票代码‘, ‘日期‘], how=‘left‘)
```
接下来，实现中性化函数。其核心是用市值和行业哑变量对每个因子做回归，并取残差。

![](img/c2315df7b7420729fb303cd355b101d3_112.png)

```python
def neutralize_factors(data, factor_cols, mkt_cap_col=‘流通市值‘, industry_col=‘一级行业‘):
    “““对指定因子列进行市值和行业中性化“““
    neutralized_data = data.copy()

    # 1. 准备自变量X：对数市值 + 行业哑变量 + 截距项
    # 对数化处理市值
    log_mkt_cap = np.log(neutralized_data[mkt_cap_col])
    # 生成行业哑变量矩阵
    industry_dummies = pd.get_dummies(neutralized_data[industry_col], prefix=‘ind‘)
    # 添加常数项（截距）
    X = sm.add_constant(pd.concat([log_mkt_cap, industry_dummies], axis=1))

    # 2. 对每个因子进行回归，取残差
    for factor in factor_cols:
        y = neutralized_data[factor]
        # 使用OLS回归
        model = sm.OLS(y, X, missing=‘drop‘).fit()
        # 用残差替代原始因子值
        neutralized_data[factor] = model.resid

    return neutralized_data

![](img/c2315df7b7420729fb303cd355b101d3_114.png)

![](img/c2315df7b7420729fb303cd355b101d3_116.png)

# 假设我们的因子列名为一个列表
factor_columns = [‘市净率‘, ‘市盈率‘, ‘销售净利率‘, ...] # 替换为实际的因子列名
data_neutralized = neutralize_factors(merged_data, factor_columns)
```
**代码解析**：
*   `pd.get_dummies()` 用于生成行业哑变量（One-hot Encoding）。
*   `sm.add_constant()` 为自变量矩阵添加常数列（截距项）。
*   `sm.OLS().fit()` 执行普通最小二乘回归。
*   `model.resid` 获取回归模型的残差，即中性化后的因子值。

![](img/c2315df7b7420729fb303cd355b101d3_118.png)

![](img/c2315df7b7420729fb303cd355b101d3_120.png)

![](img/c2315df7b7420729fb303cd355b101d3_122.png)

### 5. 标准化

![](img/c2315df7b7420729fb303cd355b101d3_124.png)

![](img/c2315df7b7420729fb303cd355b101d3_126.png)

![](img/c2315df7b7420729fb303cd355b101d3_128.png)

在中性化之后，需要对因子进行标准化，使其处于同一量纲。

![](img/c2315df7b7420729fb303cd355b101d3_130.png)

![](img/c2315df7b7420729fb303cd355b101d3_132.png)

![](img/c2315df7b7420729fb303cd355b101d3_134.png)

```python
def standardize_factors(data, factor_cols):
    “““对指定因子列进行Z-Score标准化“““
    standardized_data = data.copy()
    for factor in factor_cols:
        mean = standardized_data[factor].mean()
        std = standardized_data[factor].std()
        standardized_data[factor] = (standardized_data[factor] - mean) / std
    return standardized_data

![](img/c2315df7b7420729fb303cd355b101d3_136.png)

![](img/c2315df7b7420729fb303cd355b101d3_138.png)

data_final = standardize_factors(data_neutralized, factor_columns)
print(“预处理完成后的数据样例：“)
print(data_final[[‘股票代码‘, ‘日期‘] + factor_columns[:3]].head())
```

![](img/c2315df7b7420729fb303cd355b101d3_140.png)

![](img/c2315df7b7420729fb303cd355b101d3_142.png)

![](img/c2315df7b7420729fb303cd355b101d3_144.png)

### 6. 合并收益率数据（为后续分析准备）

![](img/c2315df7b7420729fb303cd355b101d3_146.png)

![](img/c2315df7b7420729fb303cd355b101d3_148.png)

![](img/c2315df7b7420729fb303cd355b101d3_150.png)

最终，我们需要将处理好的因子数据与未来的收益率数据合并，以便后续计算因子与收益的相关性。

![](img/c2315df7b7420729fb303cd355b101d3_152.png)

```python
# 导入收益率数据（如下一期收益率）
return_data = pd.read_pickle(‘return_data.pkl‘)
# 合并，假设‘下一期收益率‘列名为‘next_return‘
final_data_for_analysis = pd.merge(data_final, return_data[[‘股票代码‘, ‘日期‘, ‘next_return‘]], on=[‘股票代码‘, ‘日期‘], how=‘inner‘)
print(“可用于建模的最终数据形状：“, final_data_for_analysis.shape)
```

![](img/c2315df7b7420729fb303cd355b101d3_154.png)

![](img/c2315df7b7420729fb303cd355b101d3_156.png)

![](img/c2315df7b7420729fb303cd355b101d3_158.png)

## 总结

![](img/c2315df7b7420729fb303cd355b101d3_160.png)

本节课中，我们一起学习了量化多因子选股中数据预处理的完整流程：

![](img/c2315df7b7420729fb303cd355b101d3_162.png)

![](img/c2315df7b7420729fb303cd355b101d3_164.png)

![](img/c2315df7b7420729fb303cd355b101d3_166.png)

1.  **理解核心步骤**：包括缺失值填充、异常值处理、中性化和标准化。中性化是为了消除市值和行业偏见，标准化是为了统一因子量纲。
2.  **实践Python代码**：我们使用Pandas和Statsmodels库，逐步实现了：
    *   数据的读取与查看。
    *   使用`clip`和`fillna`进行异常值处理和缺失值填充。
    *   构建行业哑变量和对数市值，并通过线性回归取残差实现因子中性化。
    *   使用Z-Score公式进行因子标准化。
    *   将处理后的因子与收益率数据合并，为下一步的因子检验和模型训练做好准备。

![](img/c2315df7b7420729fb303cd355b101d3_168.png)

![](img/c2315df7b7420729fb303cd355b101d3_170.png)

预处理后的数据变得干净、可比，是后续进行因子有效性分析、构建预测模型以及形成投资策略的可靠基础。在下一节中，我们将学习如何使用这些处理好的数据来检验因子的有效性。