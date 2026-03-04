# Python多因子量化选股与机器学习：P1：综合打分法选股策略构建与评估 📊

![](img/636be9f7f320c9270ebbd1308e9bbcab_1.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_3.png)

在本节课中，我们将学习量化选股策略中最核心的环节之一：**综合打分法**。我们将从因子数据出发，通过打分、选股、构建投资组合，最终完成策略的性能评估。整个过程将涉及数据处理、策略逻辑实现以及关键绩效指标的计算。

![](img/636be9f7f320c9270ebbd1308e9bbcab_5.png)

---

![](img/636be9f7f320c9270ebbd1308e9bbcab_7.png)

## 数据准备与回顾

![](img/636be9f7f320c9270ebbd1308e9bbcab_9.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_11.png)

上一节我们介绍了因子筛选的方法。本节中，我们来看看如何利用筛选出的有效因子进行选股。

![](img/636be9f7f320c9270ebbd1308e9bbcab_13.png)

首先，我们导入经过预处理的因子数据。这些数据包括换手率、波动率、市净率、市现率、市盈率等因子值。

```python
# 示例：导入因子数据
import pandas as pd
factor_data = pd.read_csv('processed_factors.csv')
```

![](img/636be9f7f320c9270ebbd1308e9bbcab_15.png)

回顾上一步的因子筛选过程：我们对每个因子计算了过去11个月的IC值，筛选出IC值大于0.03的因子，并根据相关系数去除了共线性。例如，在2012年1月，我们最终选定了波动率、收益率、涨跌幅、成交量振动指标作为当期选股的依据。

![](img/636be9f7f320c9270ebbd1308e9bbcab_17.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_19.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_20.png)

---

![](img/636be9f7f320c9270ebbd1308e9bbcab_22.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_24.png)

## 综合打分法详解 🧮

![](img/636be9f7f320c9270ebbd1308e9bbcab_26.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_28.png)

综合打分法的核心思想是：对每个有效因子，根据其与未来收益的关系（由IC值的正负指示），为每只股票在该因子上的表现进行评分，最后将所有因子的分数相加，得到股票的总分。

![](img/636be9f7f320c9270ebbd1308e9bbcab_30.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_32.png)

以下是实施综合打分法的关键步骤：

1.  **因子分组与评分**：对于IC值为正的因子（因子值越大，未来收益越高），我们将所有股票按该因子值从小到大分为5组。因子值最小的组得1分，最大的组得5分。对于IC值为负的因子，则反向评分。
2.  **计算总分**：遍历所有选定的有效因子，对每只股票进行上述评分，并将所有因子的得分相加，得到该股票的综合得分。
3.  **代码逻辑**：以下代码片段展示了如何对单个因子进行分组打分。

![](img/636be9f7f320c9270ebbd1308e9bbcab_34.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_36.png)

```python
# 示例：对单个因子进行5分组打分
def score_factor(series, ic_positive=True):
    # 使用qcut进行5分位数分组
    if ic_positive:
        # IC为正，因子值越大得分越高
        groups = pd.qcut(series, q=5, labels=[1, 2, 3, 4, 5])
    else:
        # IC为负，因子值越小得分越高，因此先反向分组再映射分数
        groups = pd.qcut(series, q=5, labels=[5, 4, 3, 2, 1])
    return groups

![](img/636be9f7f320c9270ebbd1308e9bbcab_38.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_40.png)

# 假设 factor_series 是某因子在所有股票上的值，ic_flag 表示该因子IC是否为正
stock_scores = score_factor(factor_series, ic_flag)
```

![](img/636be9f7f320c9270ebbd1308e9bbcab_42.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_44.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_46.png)

运行上述打分流程后，我们会得到一个包含每只股票综合得分的 `DataFrame`。得分高的股票（例如19分）被认为更具投资潜力，得分低的股票（例如4分）则潜力较低。

![](img/636be9f7f320c9270ebbd1308e9bbcab_48.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_50.png)

---

![](img/636be9f7f320c9270ebbd1308e9bbcab_52.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_54.png)

## 构建投资组合与计算收益 💹

![](img/636be9f7f320c9270ebbd1308e9bbcab_56.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_58.png)

得到股票综合得分后，下一步是根据得分构建投资组合并计算其收益。

![](img/636be9f7f320c9270ebbd1308e9bbcab_60.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_62.png)

1.  **股票筛选**：我们选择综合得分排名在前5%、10%或20%的股票作为投资组合的候选池。
2.  **组合构建与收益计算**：采用**等权重**方式投资选中的股票。假设每季度调仓一次，持有期为3个月。投资组合的季度收益率即为所选股票在该期间收益率的算术平均值。
3.  **代码实现**：以下是如何计算前N%股票组合收益率的简化逻辑。

![](img/636be9f7f320c9270ebbd1308e9bbcab_64.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_66.png)

```python
# 示例：计算前 top_pct 股票的等权重组合收益率
def calculate_portfolio_return(score_df, price_df, top_pct=0.1):
    """
    score_df: 包含股票代码和综合得分的DataFrame
    price_df: 包含股票收益率的时间序列DataFrame
    top_pct: 选择前百分之多少的股票，例如0.1代表前10%
    """
    # 1. 按得分排序并选择前 top_pct 的股票
    num_stocks = int(len(score_df) * top_pct)
    top_stocks = score_df.nlargest(num_stocks, '综合得分')['股票代码'].tolist()

    # 2. 获取这些股票未来3个月（一个季度）的收益率
    future_returns = price_df.loc[top_stocks].mean(axis=0)  # 等权重，取平均

    return future_returns

![](img/636be9f7f320c9270ebbd1308e9bbcab_68.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_70.png)

# 假设 quarterly_returns 是存储了每个季度组合收益率的序列
quarterly_returns = calculate_portfolio_return(total_scores, stock_return_data, 0.05)
```

![](img/636be9f7f320c9270ebbd1308e9bbcab_72.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_74.png)

通过循环每个调仓时点（如每季度初），我们可以得到策略在整个回测周期内的一系列收益率数据。

![](img/636be9f7f320c9270ebbd1308e9bbcab_76.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_78.png)

---

![](img/636be9f7f320c9270ebbd1308e9bbcab_80.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_82.png)

## 策略性能评估与可视化 📈

![](img/636be9f7f320c9270ebbd1308e9bbcab_84.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_86.png)

计算出投资组合的收益率序列后，我们需要对其性能进行全面评估，并与基准（如沪深300指数）进行比较。

![](img/636be9f7f320c9270ebbd1308e9bbcab_88.png)

以下是评估策略时需要计算的关键指标：

![](img/636be9f7f320c9270ebbd1308e9bbcab_90.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_92.png)

*   **累计收益率**：策略在整个回测期间的总收益。
    *   `累计收益率 = (1 + R1) * (1 + R2) * ... * (1 + Rn) - 1`
*   **年化收益率**：将累计收益率折算成年化标准。
    *   `年化收益率 = (1 + 累计收益率)^(252 / 回测天数) - 1`
*   **年化波动率**：收益率的标准差，衡量风险。
*   **夏普比率**：衡量单位风险获得的超额回报。
    *   `夏普比率 = (年化收益率 - 无风险利率) / 年化波动率`
*   **最大回撤**：策略从任一峰值到后续最低点的最大亏损幅度，衡量下行风险。
*   **阿尔法 & 贝塔**：通过CAPM模型计算，阿尔法衡量超额收益，贝塔衡量市场风险暴露。
*   **信息比率**：衡量单位主动风险带来的超额收益。

![](img/636be9f7f320c9270ebbd1308e9bbcab_94.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_96.png)

我们可以绘制策略净值曲线与基准曲线的对比图，以及超额收益曲线图。

![](img/636be9f7f320c9270ebbd1308e9bbcab_98.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_100.png)

```python
import matplotlib.pyplot as plt

![](img/636be9f7f320c9270ebbd1308e9bbcab_102.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_104.png)

# 示例：绘制净值曲线对比
cumulative_strategy = (1 + quarterly_returns).cumprod()
cumulative_benchmark = (1 + benchmark_returns).cumprod()

![](img/636be9f7f320c9270ebbd1308e9bbcab_106.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_108.png)

plt.figure(figsize=(12, 6))
plt.plot(cumulative_strategy, label='策略组合 (前5%)')
plt.plot(cumulative_benchmark, label='基准 (沪深300)')
plt.title('策略净值 vs 基准净值')
plt.legend()
plt.show()

![](img/636be9f7f320c9270ebbd1308e9bbcab_110.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_112.png)

# 示例：绘制超额收益曲线（阴影区域）
excess_return = quarterly_returns - benchmark_returns
cumulative_excess = (1 + excess_return).cumprod()

![](img/636be9f7f320c9270ebbd1308e9bbcab_114.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_116.png)

plt.figure(figsize=(12, 4))
plt.fill_between(cumulative_excess.index, 0, cumulative_excess.values, where=cumulative_excess.values >= 1, color='green', alpha=0.3, label='正超额')
plt.fill_between(cumulative_excess.index, 0, cumulative_excess.values, where=cumulative_excess.values < 1, color='red', alpha=0.3, label='负超额')
plt.plot(cumulative_excess, color='black', linewidth=1)
plt.axhline(y=1, color='grey', linestyle='--')
plt.title('累计超额收益曲线')
plt.legend()
plt.show()
```

![](img/636be9f7f320c9270ebbd1308e9bbcab_118.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_120.png)

从示例结果图中通常可以看到，选择得分排名更靠前（如前5%）的股票，其累计收益率、夏普比率等指标往往优于选择更宽范围（如前20%）的组合，但可能伴随更大的最大回撤。这符合“高收益、高风险”的基本金融规律。

![](img/636be9f7f320c9270ebbd1308e9bbcab_122.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_124.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_126.png)

---

![](img/636be9f7f320c9270ebbd1308e9bbcab_128.png)

## 总结

![](img/636be9f7f320c9270ebbd1308e9bbcab_130.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_132.png)

本节课中我们一起学习了量化选股中**综合打分法**的完整流程。

![](img/636be9f7f320c9270ebbd1308e9bbcab_134.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_136.png)

1.  **核心逻辑**：我们根据因子的IC值方向，对股票在每个有效因子上的表现进行1-5分的评分，加总后得到综合得分。
2.  **策略构建**：依据综合得分排名，选择前列的股票构建等权重投资组合，并计算其定期收益率。
3.  **绩效评估**：我们计算了累计收益率、年化收益率、夏普比率、最大回撤等一系列关键指标，并通过与基准对比的净值曲线和超额收益曲线，直观地评估了策略的有效性和风险特征。

![](img/636be9f7f320c9270ebbd1308e9bbcab_138.png)

![](img/636be9f7f320c9270ebbd1308e9bbcab_140.png)

综合打分法是一种直观且强大的多因子选股方法，它将多个因子的信息浓缩为一个总分，简化了选股决策。理解并掌握这一方法，是构建更复杂量化策略的重要基础。