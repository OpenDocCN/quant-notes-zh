# 量化金融与机器学习：P1：Fama-French三因子模型原理与实现

在本节课中，我们将要学习Fama-French三因子模型。该模型在经典的资本资产定价模型基础上，引入了两个新的因子，以更准确地解释和预测股票的预期收益率。我们将从模型的理论背景讲起，逐步深入到因子的构建方法，并最终通过Python代码实现因子的计算和时序回归分析。

## 模型背景与核心思想

上一节我们介绍了CAPM模型及其局限性。CAPM模型基于一系列理想化假设，忽略了可能影响市场的其他因素，导致其在实际应用中的效果不尽如人意。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_1.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_3.png)

因此，学者们开始探索是否还有其他因素决定了股票的预期收益。Fama-French三因子模型就是其中最著名的扩展之一。它在CAPM模型的基础上，增加了两个新的因子。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_5.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_7.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_9.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_11.png)

该模型的核心公式如下：
```
E(Ri) - Rf = βi * (E(Rm) - Rf) + si * SMB + hi * HML + εi
```
其中：
*   `E(Ri) - Rf` 是股票i的超额收益率。
*   `E(Rm) - Rf` 是市场风险溢价，即CAPM中的市场因子。
*   `SMB` 是规模因子，代表小市值公司的超额收益。
*   `HML` 是价值因子，代表高账面市值比公司的超额收益。
*   `βi`, `si`, `hi` 分别是股票i对这三个因子的敏感度系数。
*   `εi` 是残差项。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_13.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_15.png)

模型的目标与CAPM一致，用于资产定价，但它通过引入**SMB**和**HML**两个因子，试图捕捉CAPM未能解释的收益部分。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_17.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_19.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_21.png)

## 新增因子的经济含义

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_23.png)

以下是两个新增因子的具体解释：

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_25.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_27.png)

**SMB因子**
SMB因子旨在捕捉“规模效应”，即小市值公司相对于大市值公司存在超额收益的现象。其构建思想是做多一篮子小市值公司，同时做空一篮子大市值公司，这个多空组合的收益率即为SMB因子。历史数据表明，在某些市场阶段，仅依赖小市值因子就能获得显著的投资回报。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_29.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_31.png)

**HML因子**
HML因子旨在捕捉“价值效应”。这里的“价值”用**账面市值比**来衡量。账面市值比是市净率的倒数，计算公式为：
```
账面市值比 = 净资产 / 总市值
```
价值效应认为，高账面市值比的价值型股票，相对于低账面市值比的成长型股票，存在超额收益。HML因子的构建是做多高账面市值比公司，做空低账面市值比公司。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_33.png)

## 因子的构建方法

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_35.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_37.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_39.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_41.png)

理解了因子的经济含义后，本节我们来看看如何具体构建这两个因子。核心思路是将它们构建为“多空投资组合的收益率”，以确保所有因子都具有“收益率”的量纲和经济学意义。

构建过程分为以下几步：

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_43.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_45.png)

**第一步：数据准备与分组**
首先，在每一个时间截面，获取所有股票的总市值和账面市值比数据。

**第二步：规模分组**
将所有股票按总市值从小到大排序，并找到中位数。将市值低于中位数的股票划分为**S组**，代表小市值公司；将市值高于中位数的股票划分为**B组**，代表大市值公司。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_47.png)

**第三步：价值分组**
将所有股票按账面市值比从小到大排序。将排名在前30%的股票划分为**L组**，代表低账面市值比公司；将排名在中间40%的股票划分为**M组**；将排名在后30%的股票划分为**H组**，代表高账面市值比公司。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_49.png)

**第四步：形成六组投资组合**
每个股票现在都有两个标签。通过组合这两个标签，我们可以得到6个投资组合：`S/L`, `S/M`, `S/H`, `B/L`, `B/M`, `B/H`。

**第五步：计算组合收益率**
对于每个投资组合，计算其按市值加权的平均日收益率。这意味着组合内每只股票的投资权重与其总市值成正比。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_51.png)

**第六步：计算因子收益率**
最后，通过多空组合的收益率差来定义因子：
*   **SMB因子** = (S/L组合收益率 + S/M组合收益率 + S/H组合收益率) / 3 - (B/L组合收益率 + B/M组合收益率 + B/H组合收益率) / 3
*   **HML因子** = (S/H组合收益率 + B/H组合收益率) / 2 - (S/L组合收益率 + B/L组合收益率) / 2

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_53.png)

## 代码实现：因子计算

以下是使用Python计算Fama-French三因子的核心代码片段。我们假设已经获取了包含股票代码、日期、收盘价、总市值和市净率的历史数据。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_55.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_57.png)

```python
import pandas as pd
import numpy as np

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_59.png)

# 假设 df 是包含所需字段的DataFrame
# 列包括：`stock_code`, `trade_date`, `close`, `total_mv`(总市值), `pb`(市净率)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_61.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_62.png)

# 计算账面市值比 (Book-to-Market Ratio)
df[‘bm’] = 1 / df[‘pb’]

# 初始化列表存储每日的因子值
smb_list = []
hml_list = []
date_list = []

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_64.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_66.png)

# 获取所有交易日并循环
unique_dates = df[‘trade_date’].unique()
for current_date in unique_dates:
    day_data = df[df[‘trade_date’] == current_date].copy()
    
    if len(day_data) < 10: # 股票数量太少则跳过
        continue
        
    # 1. 规模分组 (S/B)
    mv_median = day_data[‘total_mv’].median()
    s_group = day_data[day_data[‘total_mv’] <= mv_median].index.tolist()
    b_group = day_data[day_data[‘total_mv’] > mv_median].index.tolist()
    
    # 2. 价值分组 (L/M/H)
    bm_30 = day_data[‘bm’].quantile(0.3)
    bm_70 = day_data[‘bm’].quantile(0.7)
    l_group = day_data[day_data[‘bm’] <= bm_30].index.tolist()
    m_group = day_data[(day_data[‘bm’] > bm_30) & (day_data[‘bm’] <= bm_70)].index.tolist()
    h_group = day_data[day_data[‘bm’] > bm_70].index.tolist()
    
    # 3. 形成6个组合
    def intersect(lst1, lst2):
        return list(set(lst1) & set(lst2))
    
    sl = intersect(s_group, l_group)
    sm = intersect(s_group, m_group)
    sh = intersect(s_group, h_group)
    bl = intersect(b_group, l_group)
    bm = intersect(b_group, m_group)
    bh = intersect(b_group, h_group)
    portfolios = {‘SL’: sl, ‘SM’: sm, ‘SH’: sh, ‘BL’: bl, ‘BM’: bm, ‘BH’: bh}
    
    # 4. 计算各组合市值加权收益率 (需要下一期收益率数据，此处为简化示意)
    # 假设 day_data 中已有下一期收益率列 ‘next_ret’
    portfolio_returns = {}
    for name, stocks in portfolios.items():
        if len(stocks) == 0:
            portfolio_returns[name] = np.nan
            continue
        sub_data = day_data.loc[stocks]
        # 市值加权平均收益率
        weighted_ret = np.average(sub_data[‘next_ret’], weights=sub_data[‘total_mv’])
        portfolio_returns[name] = weighted_ret
    
    # 5. 计算SMB和HML因子
    smb = (portfolio_returns[‘SL’] + portfolio_returns[‘SM’] + portfolio_returns[‘SH’]) / 3 - \
          (portfolio_returns[‘BL’] + portfolio_returns[‘BM’] + portfolio_returns[‘BH’]) / 3
    hml = (portfolio_returns[‘SH’] + portfolio_returns[‘BH’]) / 2 - \
          (portfolio_returns[‘SL’] + portfolio_returns[‘BL’]) / 2
    
    # 存储结果
    if not np.isnan(smb) and not np.isnan(hml):
        smb_list.append(smb)
        hml_list.append(hml)
        date_list.append(current_date)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_68.png)

# 构建因子序列DataFrame
factor_df = pd.DataFrame({
    ‘trade_date’: date_list,
    ‘SMB’: smb_list,
    ‘HML’: hml_list
}).set_index(‘trade_date’)
```

## 代码实现：时序回归分析

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_70.png)

计算出市场因子、SMB因子和HML因子后，我们就可以对单只股票进行时序回归，估计其对各因子的暴露系数。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_72.png)

```python
import statsmodels.api as sm

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_74.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_76.png)

# 1. 准备数据：以股票‘000001.SZ’为例，计算其超额收益率
stock_code = ‘000001.SZ’
stock_data = get_stock_data(stock_code) # 自定义函数，获取股票价格序列
market_data = get_market_data() # 自定义函数，获取市场指数收益率

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_78.png)

# 计算收益率
stock_data[‘ret’] = stock_data[‘close’].pct_change()
market_data[‘mkt_ret’] = market_data[‘close’].pct_change()

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_80.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_81.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_83.png)

# 合并数据，对齐日期
merged_data = pd.merge(stock_data[[‘trade_date’, ‘ret’]], market_data[[‘trade_date’, ‘mkt_ret’]], on=‘trade_date’)
merged_data = pd.merge(merged_data, factor_df, left_on=‘trade_date’, right_index=True)

# 设定无风险收益率 (例如年化3%)
rf_daily = 0.03 / 252
# 计算超额收益率
merged_data[‘excess_ret’] = merged_data[‘ret’] - rf_daily
merged_data[‘mkt_excess’] = merged_data[‘mkt_ret’] - rf_daily

# 2. 准备回归变量
X = merged_data[[‘mkt_excess’, ‘SMB’, ‘HML’]].dropna()
X = sm.add_constant(X) # 添加常数项
y = merged_data.loc[X.index, ‘excess_ret’]

# 3. 执行OLS回归
model = sm.OLS(y, X).fit()

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_85.png)

# 4. 查看回归结果
print(model.summary())

# 输出核心系数
print(f“市场因子Beta: {model.params[‘mkt_excess’]:.4f}“)
print(f“规模因子系数s: {model.params[‘SMB’]:.4f}“)
print(f“价值因子系数h: {model.params[‘HML’]:.4f}“)
print(f“模型R-squared: {model.rsquared:.4f}“)
```
回归结果中的系数 `s` 和 `h` 分别代表了该股票收益率对规模因子和价值因子变化的敏感程度。较高的R-squared值表明三因子模型比CAPM能更好地解释该股票的历史收益率波动。

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_87.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_89.png)

## 总结

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_91.png)

![](img/f2f5d3bb00cf04ccc2f3451e6eec34c8_93.png)

本节课中我们一起学习了Fama-French三因子模型。我们从CAPM的局限性出发，理解了引入规模因子和价值因子的动机。我们详细探讨了SMB和HML因子的具体经济含义及其严谨的构建方法，即通过双重排序形成多空投资组合来计算因子收益率。最后，我们通过完整的Python代码演示了如何从原始数据计算这两个因子，并如何利用它们对单只股票进行时序回归分析，从而得到股票对各因子的风险暴露。三因子模型为资产定价和风险分析提供了一个比CAPM更强大的框架。