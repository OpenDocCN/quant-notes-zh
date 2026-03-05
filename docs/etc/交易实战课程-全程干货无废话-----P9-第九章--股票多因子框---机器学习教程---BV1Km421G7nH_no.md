# AI量化交易实战课程：P9：股票多因子框架 - 数据处理与行业中性化实践

![](img/563bd1247ac53d0690a85d1dceb561a8_1.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_3.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_5.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_7.png)

## 概述
在本节课中，我们将学习如何为股票多因子模型准备和处理核心数据。我们将重点关注如何获取并整合指数成分、行业分类、基本面及量价数据，并实践构建一个动态的行业分类系统，为后续的因子检验和行业中性化处理打下基础。

---

## 多因子模型所需的核心数据

上一节我们介绍了多因子模型的基本概念，本节中我们来看看构建一个对标特定指数（如中证500）的模型具体需要哪些数据。

进行多因子模型研究，不能直接使用全市场的原始价格数据。为了构建一个有效且可交易的模型，我们需要系统性地准备以下数据：

1.  **指数价格数据**：用于计算基准收益率，作为模型表现的对比基准。
2.  **指数成分数据**：明确我们的股票池范围。例如，对标中证500指数，就需要知道历史上每个交易日哪些股票属于该指数成分股。
3.  **行业分类数据**：用于进行行业中性化处理。我们需要知道每只股票在历史上每个时间点所属的行业（通常使用一级行业分类），并且这个数据是动态变化的。
4.  **基本面数据**：公司的财务指标，用于构建基本面因子。**关键点**：必须使用“历史时点”的数据，即避免使用未来更新过的财报数据，防止回测中用到未来信息。
5.  **量价数据**：股票的开盘、收盘、最高、最低价、成交量等，用于计算量价因子和收益率。通常需要使用复权后的价格进行计算。
6.  **股票上市日期**：用于剔除新股。例如，可以设定不交易上市未满一年的股票。
7.  **交易成本数据**：在回测中考虑手续费、印花税等交易成本，使回测结果更贴近实际。

![](img/563bd1247ac53d0690a85d1dceb561a8_9.png)

---

![](img/563bd1247ac53d0690a85d1dceb561a8_11.png)

## 数据处理实战：构建动态行业分类系统

理解了所需数据后，我们开始动手处理。一个常见的起点是构建一个动态的行业分类系统，以便在任何交易日都能快速获取各行业的股票列表。

![](img/563bd1247ac53d0690a85d1dceb561a8_13.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_15.png)

以下是构建该系统的关键步骤：

### 1. 读取行业变动记录
我们首先加载记录股票行业变更历史的表格。每条记录包含股票代码、变更日期和变更后的行业代码。

![](img/563bd1247ac53d0690a85d1dceb561a8_17.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_19.png)

```python
import pandas as pd
sector_change_df = pd.read_csv('stock_sector_change.csv')
```

![](img/563bd1247ac53d0690a85d1dceb561a8_21.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_23.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_25.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_27.png)

### 2. 数据预处理
- **统一股票代码格式**：将数字代码转换为带市场后缀（如‘SZ’、‘SH’）的标准格式。
- **聚焦一级行业**：我们只关心一级行业分类。如果原始数据包含二级、三级行业变更记录，需要去重，只保留一级行业发生变化的记录。

![](img/563bd1247ac53d0690a85d1dceb561a8_29.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_31.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_33.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_34.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_35.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_36.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_37.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_38.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_40.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_42.png)

```python
# 示例：添加市场后缀
def add_market_suffix(code):
    code_str = str(code).zfill(6)
    if code_str.startswith(('00', '30')):
        return code_str + '.SZ'
    elif code_str.startswith('60'):
        return code_str + '.SH'
    else:
        return code_str + '.SH' # 其他情况默认上海

![](img/563bd1247ac53d0690a85d1dceb561a8_44.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_45.png)

sector_change_df['stock_id'] = sector_change_df['security_code'].apply(add_market_suffix)
# 提取一级行业代码（假设原行业代码为6位数字，前2位为一级）
sector_change_df['sector_code'] = sector_change_df['industry_code'].astype(str).str[:2]
```

### 3. 创建行业查询类
我们需要一个类（Class）来封装功能，输入日期和行业代码，返回该日期该行业下的所有股票列表。

其核心逻辑是：对于给定的查询日期，找出每只股票在该日期之前最后一次行业变更记录，该记录中的行业即为股票在该日期的所属行业。

```python
class SectorManager:
    def __init__(self, sector_change_df):
        self.df = sector_change_df
        # 确保日期列为datetime类型
        self.df['change_date'] = pd.to_datetime(self.df['change_date'])

    def get_stock_list(self, sector_code, current_date):
        """
        获取指定日期（current_date）下，指定行业（sector_code）的股票列表。
        """
        # 1. 筛选出变更日期早于查询日期的所有记录
        hist_df = self.df[self.df['change_date'] <= current_date]

        # 2. 对于每只股票，保留其最后一条变更记录（即最新行业）
        latest_df = hist_df.sort_values('change_date').groupby('stock_id').tail(1)

        # 3. 筛选出行业代码匹配的股票
        sector_stocks = latest_df[latest_df['sector_code'] == sector_code]['stock_id'].tolist()
        return sector_stocks

# 初始化并使用
sector_manager = SectorManager(sector_change_df)
bank_stocks = sector_manager.get_stock_list('01', pd.Timestamp('2016-01-04'))
print(f"2016-01-04 银行股数量：{len(bank_stocks)}")
```

---

![](img/563bd1247ac53d0690a85d1dceb561a8_47.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_49.png)

## 整合数据：以中证500为股票池

![](img/563bd1247ac53d0690a85d1dceb561a8_51.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_53.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_55.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_56.png)

有了行业信息，下一步是将其他数据（如基本面因子）与我们的目标股票池（中证500）以及行业信息进行整合。

![](img/563bd1247ac53d0690a85d1dceb561a8_58.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_60.png)

### 1. 加载并筛选数据
假设我们已加载了中证500成分股权重表`index_weights_df`和基本面数据`fundamental_df`。

![](img/563bd1247ac53d0690a85d1dceb561a8_62.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_63.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_64.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_65.png)

```python
# 假设当前研究日期为 2016-01-04
current_date = pd.Timestamp('2016-01-04')
# 获取该日期中证500的成分股列表
index_constituents = index_weights_df[index_weights_df['date'] == current_date]['stock_id'].tolist()

# 筛选出该日期的基本面数据，并只保留中证500成分股
fundamental_current = fundamental_df[
    (fundamental_df['date'] == current_date) &
    (fundamental_df['stock_id'].isin(index_constituents))
].copy()
```

![](img/563bd1247ac53d0690a85d1dceb561a8_67.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_68.png)

### 2. 关联行业信息
为基本面数据中的每只股票添加其所属行业。

```python
# 创建一个股票到行业的映射字典（基于当前日期）
stock_to_sector = {}
for sector in sector_codes: # sector_codes 是所有一级行业代码列表
    stocks = sector_manager.get_stock_list(sector, current_date)
    for stock in stocks:
        if stock in index_constituents: # 只映射股票池内的股票
            stock_to_sector[stock] = sector

# 将行业信息加入基本面DataFrame
fundamental_current['sector'] = fundamental_current['stock_id'].map(stock_to_sector)
```

### 3. 计算行业因子暴露
现在，我们可以分析某个因子（例如市净率倒数 `BPLR`）在不同行业间的分布。计算时需考虑市值加权，而非简单等权平均。

```python
# 合并市值权重（假设权重数据已对齐）
fundamental_current = fundamental_current.merge(
    index_weights_df[['stock_id', 'weight']],
    on='stock_id',
    how='left'
)

# 计算等权重的行业均值
sector_mean_equal = fundamental_current.groupby('sector')['BPLR'].mean()

# 计算市值加权后的行业均值 (因子值 * 权重)
fundamental_current['BPLR_weighted'] = fundamental_current['BPLR'] * fundamental_current['weight']
sector_mean_weighted = fundamental_current.groupby('sector')['BPLR_weighted'].sum()

![](img/563bd1247ac53d0690a85d1dceb561a8_70.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_71.png)

print("等权重与市值加权的行业因子值可能存在显著差异，后者更符合指数结构。")
```

---

## 总结

![](img/563bd1247ac53d0690a85d1dceb561a8_73.png)

![](img/563bd1247ac53d0690a85d1dceb561a8_75.png)

本节课中我们一起学习了构建多因子模型的数据基石。我们明确了模型所需的七类核心数据，并重点实践了如何通过动态行业分类系统来组织数据。关键收获在于：

1.  **数据质量至关重要**：必须使用“历史时点”数据，避免未来函数，并对退市、停牌、新股等特殊情况进行处理。
2.  **模块化思维**：将数据查询功能（如获取某日某行业股票列表）封装成可复用的类或函数，能极大提升后续研究效率。
3.  **对齐股票池**：所有因子计算和组合构建都应在目标指数（如中证500）的成分股池内进行。
4.  **加权方式的影响**：在计算行业因子暴露时，采用市值加权通常比等权重更符合实际指数结构，结果也可能不同。

![](img/563bd1247ac53d0690a85d1dceb561a8_77.png)

这些数据处理步骤为下一步——因子的标准化、中性化处理以及有效性检验——做好了准备。