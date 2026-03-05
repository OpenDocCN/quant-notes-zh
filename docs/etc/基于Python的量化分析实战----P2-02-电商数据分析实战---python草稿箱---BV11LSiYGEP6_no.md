# 基于Python的量化分析实战：P2：02 电商数据分析实战 🛒

![](img/cc1ed03db05694d19b3075b2a45e714d_1.png)

![](img/cc1ed03db05694d19b3075b2a45e714d_3.png)

在本节课中，我们将学习如何使用Python进行电商数据分析。我们将从一个大型连锁超市的销售数据集出发，完整地走一遍数据分析的流程，包括数据加载、清洗、建模与可视化，并从中挖掘出有价值的商业洞察。

![](img/cc1ed03db05694d19b3075b2a45e714d_5.png)

## 概述：数据分析的完整流程

![](img/cc1ed03db05694d19b3075b2a45e714d_7.png)

![](img/cc1ed03db05694d19b3075b2a45e714d_9.png)

在开始具体项目之前，我们先了解数据分析的标准流程。一个完整的数据分析项目通常包含以下步骤：
1.  **明确目标**：确定要解决什么问题。
2.  **获取数据**：从业务方或数据库收集原始数据。
3.  **加载与清洗数据**：将数据导入分析工具，并处理缺失值、异常值等问题。
4.  **建模分析**：通过计算指标、建立数据关系或应用算法来发现规律。
5.  **数据可视化**：将分析结果以图表形式直观展示。
6.  **撰写报告**：解释图表含义，总结发现的问题。
7.  **提出建议**：针对问题，给出可行的解决方案。
8.  **验证与迭代**：在业务调整后，用新数据验证结论，并优化分析模型。

![](img/cc1ed03db05694d19b3075b2a45e714d_10.png)

本节课，我们将遵循这个流程，对一个超市销售数据集进行实战分析。

## 第一部分：环境准备与数据加载

![](img/cc1ed03db05694d19b3075b2a45e714d_12.png)

上一节我们介绍了数据分析的宏观流程，本节中我们来看看如何准备分析环境并加载数据。

### 1.1 导入必要的工具库
进行数据分析，我们主要依赖Python的几个核心库，它们被称为“数据分析三剑客”。

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
# 其他辅助库
from datetime import datetime
import warnings
warnings.filterwarnings('ignore')
# 设置中文显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False
```

*   **`pandas`**：用于数据加载、清洗、转换和分析的核心库。
*   **`numpy`**：提供高效的数组运算功能。
*   **`matplotlib` 与 `seaborn`**：用于创建各种静态、交互式图表。
*   其他库用于处理日期、忽略警告和兼容中文显示。

### 1.2 加载与初步查看数据
我们使用 `pandas` 的 `read_csv` 函数来加载CSV格式的数据文件。

```python
# 加载数据
df = pd.read_csv('superstore_sales.csv')
# 查看数据前2行，了解数据结构
print(df.head(2))
# 查看数据后2行
print(df.tail(2))
# 查看数据整体信息，包括行数、列数、数据类型和缺失值
print(df.info())
# 查看数据形状
print(df.shape)
```

执行以上代码后，我们可以看到数据包含超过5万行记录，每行代表一笔订单。字段包括订单ID、日期、客户信息、商品信息、销售金额、利润、地区等。

## 第二部分：数据清洗与预处理

加载数据后，我们发现数据中存在缺失值和日期格式不一致的问题。数据清洗是保证分析质量的关键步骤。

### 2.1 处理日期字段
订单日期是时间序列分析的基础，需要统一格式。

```python
# 将‘Order Date’列转换为标准的日期时间格式
df['Order Date'] = pd.to_datetime(df['Order Date'], format='%d/%m/%Y')
# 随机抽样5条数据，检查转换结果
print(df['Order Date'].sample(5))
```

### 2.2 处理缺失值
通过 `df.info()` 我们发现 `Postal Code`（邮编）字段存在大量缺失。对于本分析，邮编并非核心维度，我们可以选择删除该列。

```python
# 删除‘Postal Code’列
df_clean = df.drop(columns=['Postal Code'])
# 再次确认数据信息
print(df_clean.info())
```

### 2.3 提取分析所需字段
为了后续按年、月进行分析，我们需要从日期中提取出相应的成分。

```python
# 从‘Order Date’中提取‘Year’和‘Month’
df_clean['Year'] = df_clean['Order Date'].dt.year
df_clean['Month'] = df_clean['Order Date'].dt.month
# 查看新增字段
print(df_clean[['Order Date', 'Year', 'Month']].head())
```

### 2.4 计算衍生指标
我们还可以计算一些基础衍生指标，例如商品单价。

```python
# 计算平均单价（注意：此为订单层面的近似值）
df_clean['Unit Price'] = df_clean['Sales'] / df_clean['Quantity']
print(df_clean[['Sales', 'Quantity', 'Unit Price']].head())
```

## 第三部分：整体运营状况分析

数据准备就绪后，我们开始构建分析模型。首先从宏观层面了解企业的整体运营状况。

### 3.1 年度核心指标分析
以下是计算年度销售额、利润额、销量和利润率的代码。

```python
# 按年分组聚合，计算核心指标
yearly_summary = df_clean.groupby('Year').agg({
    'Sales': 'sum',        # 总销售额
    'Profit': 'sum',       # 总利润额
    'Quantity': 'sum'      # 总销量
}).reset_index()

# 计算利润率
yearly_summary['Profit Ratio'] = yearly_summary['Profit'] / yearly_summary['Sales']

print(yearly_summary)
```

**分析结论**：从2011年到2014年，超市的销售额、利润额和销量均呈现稳步上升趋势。

### 3.2 计算同比增长率
同比增长率能反映业绩的增长速度。

```python
# 计算销售额的同比增长率
yearly_summary['Sales Growth Rate'] = yearly_summary['Sales'].pct_change() * 100
# 同理可计算利润、销量的增长率
print(yearly_summary[['Year', 'Sales', 'Sales Growth Rate']])
```

**分析结论**：2013年销售额增长率达到峰值（约27%），但2014年增长率有所回落。需要结合市场环境和公司策略进一步分析原因。

![](img/cc1ed03db05694d19b3075b2a45e714d_14.png)

![](img/cc1ed03db05694d19b3075b2a45e714d_16.png)

## 第四部分：多维度深入分析

了解了整体情况后，我们需要从多个维度拆解数据，以发现更深层次的问题和机会。

![](img/cc1ed03db05694d19b3075b2a45e714d_18.png)

### 4.1 月度销售趋势与季节性分析
使用数据透视表可以方便地查看每年每月的销售情况。

```python
# 创建月度销售额透视表
monthly_sales_pivot = pd.pivot_table(df_clean,
                                      values='Sales',
                                      index='Month',
                                      columns='Year',
                                      aggfunc='sum')
print(monthly_sales_pivot)
```

**分析结论**：通过绘制折线图（代码略），可以清晰看到该超市销售具有明显的季节性。下半年（尤其是年末）为销售旺季，上半年则为淡季。建议在旺季加大营销投入，在淡季尝试新品推广或促销活动。

### 4.2 各门店/区域贡献度分析
分析不同门店或区域的销售与利润贡献。

```python
# 按‘Region’分析销售额和利润贡献
region_performance = df_clean.groupby('Region').agg({'Sales':'sum', 'Profit':'sum'}).sort_values('Sales', ascending=False)
region_performance['Sales Contribution'] = region_performance['Sales'] / region_performance['Sales'].sum() * 100
print(region_performance)
```

**分析结论**：APAC（亚太）和EU（欧洲）区域是销售额的主要贡献者。但需结合利润贡献度一起看，评估各区域的实际盈利能力。

### 4.3 商品结构分析
分析哪些品类或子品类是销售主力，以及它们的盈利情况。

```python
# 按商品类别和子类别分组分析
category_performance = df_clean.groupby(['Category', 'Sub-Category']).agg({'Sales':'sum', 'Profit':'sum'}).reset_index()
# 可进一步排序或绘制堆叠柱状图
print(category_performance.sort_values('Sales', ascending=False).head(10))
```

**分析结论**：例如，可能发现“技术”类产品销售额最高，但“办公用品”类的利润率更好。这为库存管理和营销重点提供了依据。

### 4.4 客户价值分析（RFM模型）
RFM模型是衡量客户价值的经典方法。
*   **R（Recency）**：最近一次消费时间。
*   **F（Frequency）**：消费频率。
*   **M（Monetary）**：消费总金额。

以下是计算RFM指标的简化代码：

```python
# 计算RFM指标
import datetime as dt
snapshot_date = df_clean['Order Date'].max() + dt.timedelta(days=1) # 假设分析快照日期为最后一天后一天

rfm = df_clean.groupby('Customer ID').agg({
    'Order Date': lambda x: (snapshot_date - x.max()).days, # Recency
    'Order ID': 'nunique', # Frequency (基于订单数)
    'Sales': 'sum' # Monetary
})
rfm.columns = ['Recency', 'Frequency', 'Monetary']

# 对RFM值进行分档评分（例如1-4分）
rfm['R_Score'] = pd.qcut(rfm['Recency'], 4, labels=[4,3,2,1]) # 最近消费的给高分
rfm['F_Score'] = pd.qcut(rfm['Frequency'], 4, labels=[1,2,3,4])
rfm['M_Score'] = pd.qcut(rfm['Monetary'], 4, labels=[1,2,3,4])

rfm['RFM_Score'] = rfm['R_Score'].astype(str) + rfm['F_Score'].astype(str) + rfm['M_Score'].astype(str)
print(rfm.head())
```

**分析结论**：根据RFM得分可以将客户分为“重要价值客户”、“重要发展客户”、“重要保持客户”、“一般挽留客户”等群体，从而实施精准营销。

## 第五部分：分析总结与建议

本节课中，我们一起学习了电商数据分析的完整流程。我们从加载和清洗一个超市销售数据集开始，逐步进行了整体运营分析、多维度拆解以及客户价值分析。

**核心发现总结**：
1.  **整体增长**：公司2011-2014年业绩持续增长，但2014年增速放缓。
2.  **季节性强**：销售呈现下半年旺季、上半年淡季的特征。
3.  **区域差异**：不同区域对销售额和利润的贡献度不同，需差异化经营。
4.  **商品结构**：各品类销售与利润表现不一，需优化商品组合。
5.  **客户分层**：客户价值差异显著，可通过RFM模型实现精细化运营。

**业务建议**：
*   **针对增长放缓**：深入分析2014年市场环境、竞争态势及内部促销策略效果。
*   **利用季节性**：旺季前备足库存并加大推广，淡季策划主题促销活动或推送新品。
*   **优化区域策略**：对高销售额但低利润率的区域，分析成本构成，尝试提升运营效率。
*   **调整商品策略**：加大高利润品类的推广力度，评估低利润品类存在的必要性。
*   **实施精准营销**：对RFM模型识别出的“重要价值客户”提供VIP服务，对“重要保持客户”进行唤醒和关怀。

![](img/cc1ed03db05694d19b3075b2a45e714d_20.png)

通过本次实战，我们不仅掌握了`pandas`、`matplotlib`等工具的基本用法，更重要的是理解了如何将数据分析方法应用于真实的商业场景，从数据中提炼出 actionable insights（可执行的见解）。这就是数据驱动的决策过程。