# 量化交易：P16：第九章第1节： 股票多因子框架2 - 数据处理实践 📊

![](img/db22b10b03a8ff3975a7fbbf0b525332_1.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_3.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_5.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_7.png)

在本节课中，我们将深入学习如何为多因子模型准备和处理核心数据。我们将从理解所需的数据类型开始，逐步构建一个能够动态处理行业分类和股票池的模块，并最终对一个基本面因子进行初步的行业分析。这是将理论模型付诸实践的关键一步。

## 课程概述

上一节我们介绍了多因子模型的基本概念。本节中，我们将重点关注模型的实践部分，特别是数据处理流程。我们将回答一个核心问题：构建一个对标中证500指数的多因子模型，具体需要哪些数据，以及如何处理这些数据以避免常见陷阱。

## 核心数据需求分析

首先，让我们明确构建多因子模型所需的数据类型。对标一个基准指数（如中证500）进行选股，并非简单地使用全市场数据。以下是必需的核心数据清单：

*   **基准指数价格数据**：用于计算模型的基准收益率。
*   **指数成分股数据**：动态的股票池列表，确保我们的选股范围始终在指数成分内。
*   **成分股行业分类数据**：一个动态记录每只股票行业归属变化的数据表，用于进行行业中性化处理。
*   **成分股财务基本面数据**：包含大量财务指标的面板数据，需注意使用“历史时点”数据以避免未来函数。
*   **成分股量价数据**：用于计算收益率以及衍生量价因子。
*   **股票上市日期**：用于剔除新股（例如上市未满一年的股票）。
*   **交易成本数据**：在回测中考虑交易费用，使结果更贴近实际。

**关键概念**：使用“历史时点”数据。这意味着在回测中，我们使用的数据必须是当时市场上可获取的数据快照，而不是事后更新的最新数据。例如，2019年的财报可能在2020年修正，回测时必须使用2019年当时公布的版本。代码上，这通常通过每日更新和存储数据快照来实现。

![](img/db22b10b03a8ff3975a7fbbf0b525332_9.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_11.png)

## 数据处理实战：构建动态行业分类模块

理解了所需数据后，我们开始处理其中最复杂的部分之一：动态行业分类。我们的目标是：输入一个日期和一个行业代码，能够返回在该日期属于该行业的所有股票列表。

### 数据准备与清洗

![](img/db22b10b03a8ff3975a7fbbf0b525332_13.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_15.png)

我们从中信一级行业分类变动记录表开始。这份数据记录了每只股票历史上每次行业归属的变化。

```python
import pandas as pd

![](img/db22b10b03a8ff3975a7fbbf0b525332_17.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_19.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_21.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_23.png)

# 读取行业变动数据
sector_change_df = pd.read_csv('sector_change.csv')
# 确保股票代码为字符串，并补齐至6位（如‘000001’）
sector_change_df['security_code'] = sector_change_df['security_code'].apply(lambda x: str(x).zfill(6))
# 将变动日期列转换为日期时间类型
sector_change_df['change_date'] = pd.to_datetime(sector_change_df['change_date'])
```

![](img/db22b10b03a8ff3975a7fbbf0b525332_25.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_27.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_29.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_31.png)

**注意**：原始数据可能包含二级、三级行业的变动记录。由于我们只关心中信一级行业，需要剔除那些仅在一级行业代码上重复的记录。

![](img/db22b10b03a8ff3975a7fbbf0b525332_33.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_34.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_35.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_36.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_37.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_38.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_40.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_42.png)

```python
# 提取一级行业代码（假设‘industry_code’是字符串，前两位为一级行业）
sector_change_df['sector'] = sector_change_df['industry_code'].str[:2]
# 去除股票代码和一级行业都完全重复的行（即仅二级/三级变动）
sector_change_df = sector_change_df.drop_duplicates(subset=['security_code', 'sector'], keep='last')
```

![](img/db22b10b03a8ff3975a7fbbf0b525332_44.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_45.png)

### 构建行业查询类

接下来，我们创建一个类来封装根据日期获取行业股票列表的逻辑。其核心思想是：对于给定的日期，找到每只股票在该日期之前最后一次行业变动记录，该记录中的行业即为股票在该日期的行业归属。

```python
class SectorManager:
    def __init__(self, sector_change_df):
        self.df = sector_change_df

    def get_stock_list_by_sector(self, sector_code, current_date):
        """
        根据行业代码和日期，获取该行业下的股票列表。
        :param sector_code: 行业代码 (str)
        :param current_date: 指定日期
        :return: 股票代码列表 (list)
        """
        # 1. 筛选出变更日期早于或等于当前日期的所有记录
        df_before_date = self.df[self.df['change_date'] <= current_date]

        # 2. 对于每只股票，取最后一条记录（即最新行业）
        latest_records = df_before_date.groupby('security_code').last().reset_index()

        # 3. 筛选出属于目标行业的股票
        stocks_in_sector = latest_records[latest_records['sector'] == sector_code]['security_code'].tolist()

        return stocks_in_sector

# 初始化管理器
sector_manager = SectorManager(sector_change_df)
# 示例：获取2016-01-04银行业（假设代码为‘01’）的股票列表
bank_stocks = sector_manager.get_stock_list_by_sector('01', pd.Timestamp('2016-01-04'))
```

## 因子数据处理：以市净率倒数为例

有了动态行业信息，我们就可以开始处理因子数据。本节以基本面因子——市净率的倒数（BPLR）为例，展示如何按行业进行统计分析。

![](img/db22b10b03a8ff3975a7fbbf0b525332_47.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_49.png)

### 数据加载与对齐

![](img/db22b10b03a8ff3975a7fbbf0b525332_51.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_53.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_55.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_56.png)

首先加载因子数据和对应日期的指数成分股权重。

![](img/db22b10b03a8ff3975a7fbbf0b525332_58.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_60.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_62.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_63.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_64.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_65.png)

```python
# 加载某日的因子数据（例如BPLR）
factor_df = pd.read_csv('20160104_factor.csv')
# 加载同日中证500成分股权重
weight_df = pd.read_csv('20160104_index_weight.csv')

# 数据预处理：统一股票代码格式
factor_df['stock_id'] = factor_df['stock_id'].apply(lambda x: str(x).zfill(6))
weight_df['wind_code'] = weight_df['wind_code'].apply(lambda x: str(x).zfill(6))

# 仅保留在指数成分股内的因子数据
factor_df = factor_df[factor_df['stock_id'].isin(weight_df['wind_code'])]
# 将权重信息合并到因子DataFrame中，按股票代码对齐
factor_df = factor_df.merge(weight_df[['wind_code', 'weight']], left_on='stock_id', right_on='wind_code')
```

![](img/db22b10b03a8ff3975a7fbbf0b525332_67.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_69.png)

### 计算行业因子值

现在，我们计算该因子在不同行业内的均值。这里对比两种方法：等权重平均和按指数权重加权平均。

```python
# 方法1：等权重行业平均
equal_weight_sector_mean = factor_df.groupby('sector')['BPLR'].mean()

# 方法2：按指数权重加权平均
# 先计算加权因子值
factor_df['weighted_BPLR'] = factor_df['BPLR'] * factor_df['weight']
# 再按行业求和（权重和因子加权值）
sector_weighted_sum = factor_df.groupby('sector')[['weight', 'weighted_BPLR']].sum()
# 计算加权平均值
weighted_sector_mean = sector_weighted_sum['weighted_BPLR'] / sector_weighted_sum['weight']
```

**对比与思考**：对于像中证500这样的指数，其成分股权重差异显著。等权重平均会人为降低大市值股票的影响，可能无法真实反映因子在指数内的暴露情况。通常，采用指数权重进行加权更为合理。

## 扩展到时间序列分析

上述步骤是针对单日数据的处理。在实际研究中，我们需要分析因子在历史时序上的表现。这意味着需要循环或向量化处理每一天的数据。

![](img/db22b10b03a8ff3975a7fbbf0b525332_71.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_72.png)

1.  **获取交易日列表**：首先需要一份完整的交易日历。
2.  **循环处理每日数据**：对于每一个交易日：
    *   获取当日的指数成分股列表。
    *   获取当日各股票的行业分类。
    *   读取当日的因子数据，并与成分股、行业、权重数据对齐。
    *   计算当日各行业的因子值（等权重或加权）。
3.  **整合结果**：将每日结果组合成一个按时间和行业索引的面板数据，便于后续分析和可视化。

**性能优化提示**：当处理大量因子和长时间序列时，循环可能成为瓶颈。可以考虑以下优化：
*   **使用向量化操作**：尽量使用Pandas/Numpy的向量化函数代替Python循环。
*   **使用更高效的数据结构**：例如将行业映射关系从字典转换为与股票池顺序对齐的列表。
*   **并行计算**：对于独立的计算任务，使用多进程库进行并行处理。
*   **使用数据库**：将数据存入数据库（如MongoDB），便于快速查询和增量更新，避免反复读取CSV文件。

## 总结与下节预告

![](img/db22b10b03a8ff3975a7fbbf0b525332_74.png)

![](img/db22b10b03a8ff3975a7fbbf0b525332_76.png)

本节课中我们一起学习了多因子模型数据处理的核心流程。我们从明确数据需求开始，重点构建了动态处理行业分类的工具，并对单个因子进行了初步的行业层面分析。我们强调了使用“历史时点”数据的重要性，并对比了因子行业值的不同计算方法。

数据处理是量化策略的基石，其质量直接决定了模型的可靠性。实践中会遇到很多细节问题，如股票退市、代码格式统一、数据缺失等，需要仔细处理。

![](img/db22b10b03a8ff3975a7fbbf0b525332_78.png)

下一节，我们将在此基础上，深入探讨如何对因子进行标准化、去极值、中性化（剔除行业、市值暴露）等预处理，并开始介绍因子IC值分析等评价方法，为最终的因子合成与模型构建做准备。