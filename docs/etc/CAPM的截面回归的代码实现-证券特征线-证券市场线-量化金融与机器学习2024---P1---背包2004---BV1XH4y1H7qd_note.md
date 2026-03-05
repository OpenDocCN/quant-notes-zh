# 量化金融与机器学习：1：CAPM截面回归与两条核心线的代码实现

![](img/c62d05baffe7fa19fc78814900682247_0.png)

![](img/c62d05baffe7fa19fc78814900682247_2.png)

![](img/c62d05baffe7fa19fc78814900682247_4.png)

![](img/c62d05baffe7fa19fc78814900682247_6.png)

![](img/c62d05baffe7fa19fc78814900682247_8.png)

![](img/c62d05baffe7fa19fc78814900682247_10.png)

![](img/c62d05baffe7fa19fc78814900682247_12.png)

![](img/c62d05baffe7fa19fc78814900682247_14.png)

![](img/c62d05baffe7fa19fc78814900682247_16.png)

![](img/c62d05baffe7fa19fc78814900682247_18.png)

![](img/c62d05baffe7fa19fc78814900682247_20.png)

![](img/c62d05baffe7fa19fc78814900682247_22.png)

在本节课中，我们将学习资本资产定价模型（CAPM）的横截面回归方法，并理解证券特征线（SCL）与证券市场线（SML）的概念、作用及其代码实现。我们将从数据处理开始，逐步完成时间序列回归和横截面回归，最终绘制出这两条重要的线。

## 概述：横截面回归的原理与步骤

![](img/c62d05baffe7fa19fc78814900682247_24.png)

横截面回归是建立在时间序列回归基础之上的。上一节我们介绍了对单只股票进行时间序列回归，得到了其阿尔法（α）和贝塔（β）系数。本节中，我们将把这个过程扩展到全市场所有股票。

![](img/c62d05baffe7fa19fc78814900682247_26.png)

![](img/c62d05baffe7fa19fc78814900682247_28.png)

![](img/c62d05baffe7fa19fc78814900682247_30.png)

![](img/c62d05baffe7fa19fc78814900682247_32.png)

![](img/c62d05baffe7fa19fc78814900682247_34.png)

![](img/c62d05baffe7fa19fc78814900682247_35.png)

![](img/c62d05baffe7fa19fc78814900682247_37.png)

横截面回归的核心步骤如下：
1.  对每一只股票进行时间序列回归，得到各自的贝塔值。
2.  计算每只股票在选定时间段（如一年）内的平均收益率。
3.  以所有股票的贝塔值为自变量，平均收益率为因变量，进行一次新的回归。
4.  这次回归拟合出的直线就是证券市场线（SML），其截距项（γ0）可用于检验CAPM理论的有效性。

![](img/c62d05baffe7fa19fc78814900682247_39.png)

![](img/c62d05baffe7fa19fc78814900682247_41.png)

![](img/c62d05baffe7fa19fc78814900682247_43.png)

![](img/c62d05baffe7fa19fc78814900682247_45.png)

![](img/c62d05baffe7fa19fc78814900682247_47.png)

所谓的“横截面”，可以形象地理解为在某个时间点上，对所有股票的数据切一刀，观察这个“断面”上所有股票的情况。实际操作中，我们取一个时间段（如一年）内每只股票收益率的均值作为该股票在这个“断面”上的代表值。

![](img/c62d05baffe7fa19fc78814900682247_49.png)

![](img/c62d05baffe7fa19fc78814900682247_51.png)

![](img/c62d05baffe7fa19fc78814900682247_53.png)

![](img/c62d05baffe7fa19fc78814900682247_55.png)

![](img/c62d05baffe7fa19fc78814900682247_57.png)

## 第一步：数据准备与预处理

![](img/c62d05baffe7fa19fc78814900682247_59.png)

![](img/c62d05baffe7fa19fc78814900682247_61.png)

![](img/c62d05baffe7fa19fc78814900682247_63.png)

![](img/c62d05baffe7fa19fc78814900682247_65.png)

![](img/c62d05baffe7fa19fc78814900682247_67.png)

![](img/c62d05baffe7fa19fc78814900682247_69.png)

![](img/c62d05baffe7fa19fc78814900682247_71.png)

首先，我们需要获取全市场股票的历史数据。这里使用一个包含2018年至2023年A股数据的数据库，并选取2020年的数据进行演示。

![](img/c62d05baffe7fa19fc78814900682247_73.png)

![](img/c62d05baffe7fa19fc78814900682247_75.png)

![](img/c62d05baffe7fa19fc78814900682247_77.png)

![](img/c62d05baffe7fa19fc78814900682247_79.png)

![](img/c62d05baffe7fa19fc78814900682247_81.png)

![](img/c62d05baffe7fa19fc78814900682247_83.png)

以下是连接数据库并获取2020年股票数据的代码：
```python
# 假设已建立数据库连接
query = “SELECT 股票代码， 交易日期， 收盘价 FROM 股票数据表 WHERE 交易日期 BETWEEN ‘2020-01-01’ AND ‘2020-12-31’”
df_raw = pd.read_sql(query， connection)
```

![](img/c62d05baffe7fa19fc78814900682247_85.png)

![](img/c62d05baffe7fa19fc78814900682247_87.png)

![](img/c62d05baffe7fa19fc78814900682247_88.png)

![](img/c62d05baffe7fa19fc78814900682247_90.png)

![](img/c62d05baffe7fa19fc78814900682247_92.png)

![](img/c62d05baffe7fa19fc78814900682247_94.png)

![](img/c62d05baffe7fa19fc78814900682247_96.png)

![](img/c62d05baffe7fa19fc78814900682247_98.png)

获取原始数据后，我们需要将其转换为更适合分析的结构：行索引为交易日期，列索引为股票代码，值为收盘价。
```python
# 转换数据格式
df_pivot = df_raw.pivot(index=‘交易日期’， columns=‘股票代码’， values=‘收盘价’)
```
数据中可能存在缺失值（例如新股上市较晚或股票停牌），我们将删除包含缺失值的列。
```python
# 删除含有缺失值的列
df_pivot_clean = df_pivot.dropna(axis=1)
print(f“清理后剩余股票数量： {df_pivot_clean.shape[1]}”)
```

![](img/c62d05baffe7fa19fc78814900682247_100.png)

![](img/c62d05baffe7fa19fc78814900682247_102.png)

![](img/c62d05baffe7fa19fc78814900682247_104.png)

![](img/c62d05baffe7fa19fc78814900682247_106.png)

![](img/c62d05baffe7fa19fc78814900682247_108.png)

![](img/c62d05baffe7fa19fc78814900682247_110.png)

![](img/c62d05baffe7fa19fc78814900682247_112.png)

## 第二步：计算收益率与市场组合

![](img/c62d05baffe7fa19fc78814900682247_114.png)

![](img/c62d05baffe7fa19fc78814900682247_116.png)

![](img/c62d05baffe7fa19fc78814900682247_118.png)

![](img/c62d05baffe7fa19fc78814900682247_120.png)

![](img/c62d05baffe7fa19fc78814900682247_122.png)

![](img/c62d05baffe7fa19fc78814900682247_124.png)

有了清洗后的股价数据，我们需要计算股票的收益率。这里我们计算10日滚动收益率以平滑短期波动。
```python
# 计算10日收益率
returns_10d = df_pivot_clean.pct_change(periods=10)
```
接下来，我们需要市场组合的收益率。在CAPM中，通常使用市场指数（如沪深300）的收益率。在简化模型中，我们可以用所有股票收益率的平均值来近似代表市场组合收益率。
```python
# 计算市场组合收益率（每行所有股票的平均值）
market_returns = returns_10d.mean(axis=1)
```
同时，我们还需要无风险收益率。假设年化无风险收益率为3%，我们需要将其折算为10日收益率。
```python
# 计算10日无风险收益率（简化处理）
annual_rf = 0.03
trading_days_per_year = 243 # 假设年交易日数
ten_day_rf = annual_rf / trading_days_per_year * 10
```

![](img/c62d05baffe7fa19fc78814900682247_126.png)

![](img/c62d05baffe7fa19fc78814900682247_128.png)

![](img/c62d05baffe7fa19fc78814900682247_130.png)

![](img/c62d05baffe7fa19fc78814900682247_132.png)

![](img/c62d05baffe7fa19fc78814900682247_134.png)

![](img/c62d05baffe7fa19fc78814900682247_136.png)

![](img/c62d05baffe7fa19fc78814900682247_138.png)

![](img/c62d05baffe7fa19fc78814900682247_140.png)

![](img/c62d05baffe7fa19fc78814900682247_142.png)

## 第三步：时间序列回归与证券特征线（SCL）

![](img/c62d05baffe7fa19fc78814900682247_144.png)

![](img/c62d05baffe7fa19fc78814900682247_146.png)

![](img/c62d05baffe7fa19fc78814900682247_148.png)

![](img/c62d05baffe7fa19fc78814900682247_150.png)

![](img/c62d05baffe7fa19fc78814900682247_152.png)

![](img/c62d05baffe7fa19fc78814900682247_154.png)

现在，我们对每一只股票进行时间序列回归。回归模型为：
**个股超额收益率 = α + β * 市场超额收益率 + ε**
其中，超额收益率 = 收益率 - 无风险收益率。

![](img/c62d05baffe7fa19fc78814900682247_156.png)

![](img/c62d05baffe7fa19fc78814900682247_158.png)

![](img/c62d05baffe7fa19fc78814900682247_160.png)

![](img/c62d05baffe7fa19fc78814900682247_162.png)

![](img/c62d05baffe7fa19fc78814900682247_164.png)

![](img/c62d05baffe7fa19fc78814900682247_166.png)

![](img/c62d05baffe7fa19fc78814900682247_168.png)

![](img/c62d05baffe7fa19fc78814900682247_170.png)

我们将循环处理所有股票，保存它们的阿尔法和贝塔值。
```python
# 初始化列表存储系数
alpha_list = []
beta_list = []

![](img/c62d05baffe7fa19fc78814900682247_172.png)

![](img/c62d05baffe7fa19fc78814900682247_174.png)

![](img/c62d05baffe7fa19fc78814900682247_176.png)

![](img/c62d05baffe7fa19fc78814900682247_178.png)

![](img/c62d05baffe7fa19fc78814900682247_179.png)

![](img/c62d05baffe7fa19fc78814900682247_181.png)

# 对每一只股票进行时间序列回归
for stock_code in returns_10d.columns:
    # 获取单只股票的收益率
    stock_ret = returns_10d[stock_code].dropna()
    # 计算超额收益率
    excess_stock = stock_ret - ten_day_rf
    excess_market = market_returns.loc[stock_ret.index] - ten_day_rf
    
    # 添加常数项，进行OLS回归
    X = sm.add_constant(excess_market)
    model = sm.OLS(excess_stock， X).fit()
    
    # 保存系数
    alpha_list.append(model.params[0]) # 截距项 α
    beta_list.append(model.params[1])  # 斜率项 β

![](img/c62d05baffe7fa19fc78814900682247_183.png)

![](img/c62d05baffe7fa19fc78814900682247_185.png)

![](img/c62d05baffe7fa19fc78814900682247_187.png)

![](img/c62d05baffe7fa19fc78814900682247_189.png)

![](img/c62d05baffe7fa19fc78814900682247_191.png)

![](img/c62d05baffe7fa19fc78814900682247_193.png)

![](img/c62d05baffe7fa19fc78814900682247_195.png)

# 转换为Series方便后续使用
alpha_series = pd.Series(alpha_list， index=returns_10d.columns)
beta_series = pd.Series(beta_list， index=returns_10d.columns)
```
完成回归后，我们可以为任意一只股票绘制其证券特征线（SCL）。SCL就是上述时间序列回归拟合出的直线，它描述了该股票超额收益率与市场超额收益率之间的关系。
```python
# 以第一只股票为例，绘制散点图和SCL
sample_stock = returns_10d.columns[0]
sample_excess_stock = returns_10d[sample_stock].dropna() - ten_day_rf
sample_excess_market = market_returns.loc[sample_excess_stock.index] - ten_day_rf

![](img/c62d05baffe7fa19fc78814900682247_197.png)

![](img/c62d05baffe7fa19fc78814900682247_199.png)

![](img/c62d05baffe7fa19fc78814900682247_201.png)

![](img/c62d05baffe7fa19fc78814900682247_203.png)

![](img/c62d05baffe7fa19fc78814900682247_205.png)

![](img/c62d05baffe7fa19fc78814900682247_206.png)

![](img/c62d05baffe7fa19fc78814900682247_208.png)

![](img/c62d05baffe7fa19fc78814900682247_210.png)

![](img/c62d05baffe7fa19fc78814900682247_212.png)

# 散点图
plt.scatter(sample_excess_market， sample_excess_stock， alpha=0.5)
plt.xlabel(‘市场超额收益率’)
plt.ylabel(‘个股超额收益率’)
plt.title(f‘证券特征线（SCL）- {sample_stock}’)

![](img/c62d05baffe7fa19fc78814900682247_214.png)

![](img/c62d05baffe7fa19fc78814900682247_216.png)

![](img/c62d05baffe7fa19fc78814900682247_218.png)

![](img/c62d05baffe7fa19fc78814900682247_219.png)

![](img/c62d05baffe7fa19fc78814900682247_221.png)

![](img/c62d05baffe7fa19fc78814900682247_223.png)

![](img/c62d05baffe7fa19fc78814900682247_225.png)

![](img/c62d05baffe7fa19fc78814900682247_227.png)

# 绘制拟合线
sample_alpha = alpha_series[sample_stock]
sample_beta = beta_series[sample_stock]
x_line = np.linspace(sample_excess_market.min()， sample_excess_market.max()， 100)
y_line = sample_alpha + sample_beta * x_line
plt.plot(x_line， y_line， color=‘red’， linewidth=2)
plt.show()
```
**证券特征线（SCL）的应用**：如果某只股票的实际收益点位于其SCL的上方，说明其实际收益高于模型预测的预期收益，可能被低估（考虑买入）；反之，若位于下方，则可能被高估（考虑卖出）。

![](img/c62d05baffe7fa19fc78814900682247_229.png)

![](img/c62d05baffe7fa19fc78814900682247_231.png)

![](img/c62d05baffe7fa19fc78814900682247_233.png)

## 第四步：横截面回归与证券市场线（SML）

![](img/c62d05baffe7fa19fc78814900682247_235.png)

![](img/c62d05baffe7fa19fc78814900682247_237.png)

![](img/c62d05baffe7fa19fc78814900682247_239.png)

![](img/c62d05baffe7fa19fc78814900682247_241.png)

![](img/c62d05baffe7fa19fc78814900682247_242.png)

![](img/c62d05baffe7fa19fc78814900682247_244.png)

![](img/c62d05baffe7fa19fc78814900682247_245.png)

![](img/c62d05baffe7fa19fc78814900682247_247.png)

![](img/c62d05baffe7fa19fc78814900682247_249.png)

![](img/c62d05baffe7fa19fc78814900682247_251.png)

在获得所有股票的贝塔值后，我们进行横截面回归。首先，计算每只股票在2020年这个“横截面”上的平均收益率。
```python
# 计算每只股票在样本期内的平均收益率（未减无风险利率）
stock_mean_returns = returns_10d.mean()
```
横截面回归的模型为：
**股票平均收益率 = γ0 + γ1 * 贝塔值 + ε**
```python
# 进行横截面回归
X_cross = sm.add_constant(beta_series) # 自变量：贝塔值，添加常数项
y_cross = stock_mean_returns # 因变量：股票平均收益率

![](img/c62d05baffe7fa19fc78814900682247_253.png)

![](img/c62d05baffe7fa19fc78814900682247_255.png)

![](img/c62d05baffe7fa19fc78814900682247_257.png)

![](img/c62d05baffe7fa19fc78814900682247_259.png)

![](img/c62d05baffe7fa19fc78814900682247_261.png)

![](img/c62d05baffe7fa19fc78814900682247_262.png)

![](img/c62d05baffe7fa19fc78814900682247_264.png)

model_cross = sm.OLS(y_cross， X_cross).fit()
print(model_cross.summary())

![](img/c62d05baffe7fa19fc78814900682247_266.png)

![](img/c62d05baffe7fa19fc78814900682247_268.png)

![](img/c62d05baffe7fa19fc78814900682247_270.png)

![](img/c62d05baffe7fa19fc78814900682247_272.png)

![](img/c62d05baffe7fa19fc78814900682247_274.png)

# 获取回归系数
gamma0 = model_cross.params[0] # 截距项
gamma1 = model_cross.params[1] # 斜率项
```
这次回归拟合出的直线就是**证券市场线（SML）**。SML反映了在市场均衡条件下，所有证券的预期收益率与其系统风险（贝塔值）之间的线性关系。
```python
# 绘制横截面散点图与证券市场线（SML）
plt.scatter(beta_series， stock_mean_returns， alpha=0.3)
plt.xlabel(‘贝塔值（系统风险）’)
plt.ylabel(‘股票平均收益率’)
plt.title(‘证券市场线（SML）’)

![](img/c62d05baffe7fa19fc78814900682247_276.png)

![](img/c62d05baffe7fa19fc78814900682247_278.png)

![](img/c62d05baffe7fa19fc78814900682247_280.png)

![](img/c62d05baffe7fa19fc78814900682247_282.png)

![](img/c62d05baffe7fa19fc78814900682247_283.png)

![](img/c62d05baffe7fa19fc78814900682247_285.png)

![](img/c62d05baffe7fa19fc78814900682247_287.png)

![](img/c62d05baffe7fa19fc78814900682247_289.png)

![](img/c62d05baffe7fa19fc78814900682247_291.png)

![](img/c62d05baffe7fa19fc78814900682247_293.png)

# 绘制SML拟合线
beta_range = np.linspace(beta_series.min()， beta_series.max()， 100)
sml_line = gamma0 + gamma1 * beta_range
plt.plot(beta_range， sml_line， color=‘red’， linewidth=3， label=‘拟合SML’)
plt.legend()
plt.show()
```
**证券市场线（SML）的意义与检验**：
*   **定价功能**：SML给出了不同系统风险水平下投资者要求的必要回报率。
*   **理论检验**：根据CAPM理论，在均衡市场中，截距项 γ0 应接近于无风险收益率，或者说，回归的截距项应与理论值无显著差异。若我们得到的 γ0 显著不为零（或显著偏离无风险利率），则表明现实市场可能不满足CAPM的严格假设（如市场均衡、同质预期等），即CAPM模型在该市场环境下解释力有限。从我们的示例结果来看，截距项并不为零，这提示了CAPM模型在A股市场（2020年）的适用性可能存在问题。

![](img/c62d05baffe7fa19fc78814900682247_294.png)

![](img/c62d05baffe7fa19fc78814900682247_296.png)

![](img/c62d05baffe7fa19fc78814900682247_298.png)

![](img/c62d05baffe7fa19fc78814900682247_299.png)

![](img/c62d05baffe7fa19fc78814900682247_301.png)

![](img/c62d05baffe7fa19fc78814900682247_303.png)

![](img/c62d05baffe7fa19fc78814900682247_305.png)

![](img/c62d05baffe7fa19fc78814900682247_307.png)

![](img/c62d05baffe7fa19fc78814900682247_309.png)

## 总结

![](img/c62d05baffe7fa19fc78814900682247_311.png)

![](img/c62d05baffe7fa19fc78814900682247_312.png)

![](img/c62d05baffe7fa19fc78814900682247_314.png)

![](img/c62d05baffe7fa19fc78814900682247_316.png)

![](img/c62d05baffe7fa19fc78814900682247_318.png)

![](img/c62d05baffe7fa19fc78814900682247_320.png)

![](img/c62d05baffe7fa19fc78814900682247_322.png)

![](img/c62d05baffe7fa19fc78814900682247_324.png)

本节课中我们一起学习了CAPM模型的横截面回归实现。我们首先回顾了对单只股票进行时间序列回归得到证券特征线（SCL）的过程，然后将其扩展到全市场所有股票。通过计算每只股票的贝塔值和期间平均收益率，我们进行了横截面回归，得到了证券市场线（SML）。

![](img/c62d05baffe7fa19fc78814900682247_326.png)

![](img/c62d05baffe7fa19fc78814900682247_328.png)

![](img/c62d05baffe7fa19fc78814900682247_329.png)

![](img/c62d05baffe7fa19fc78814900682247_331.png)

![](img/c62d05baffe7fa19fc78814900682247_333.png)

![](img/c62d05baffe7fa19fc78814900682247_335.png)

![](img/c62d05baffe7fa19fc78814900682247_337.png)

![](img/c62d05baffe7fa19fc78814900682247_339.png)

核心要点总结如下：
1.  **证券特征线（SCL）** 是单只股票超额收益与市场超额收益的关系线，可用于个股估值判断。
2.  **证券市场线（SML）** 是所有股票平均收益与其系统风险（贝塔）的关系线，它由横截面回归得到。
3.  **横截面回归** 的关键步骤是先做时间序列回归求贝塔，再做一次回归得到SML。
4.  **SML的截距项（γ0）** 是检验CAPM理论有效性的重要指标，理想状态下应接近零（或与无风险利率一致）。

![](img/c62d05baffe7fa19fc78814900682247_341.png)

![](img/c62d05baffe7fa19fc78814900682247_343.png)

![](img/c62d05baffe7fa19fc78814900682247_345.png)

![](img/c62d05baffe7fa19fc78814900682247_347.png)

![](img/c62d05baffe7fa19fc78814900682247_349.png)

![](img/c62d05baffe7fa19fc78814900682247_351.png)

![](img/c62d05baffe7fa19fc78814900682247_353.png)

通过本教程的代码实践，你不仅理解了CAPM中两条核心线的概念，也掌握了从数据处理到模型回归、再到结果可视化的完整分析流程。