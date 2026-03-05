# 机器学习与量化交易项目班：P3：金融数据获取、存储与时间序列分析入门

![](img/4cae25e1ea866396bbb3dd216933240c_0.png)

![](img/4cae25e1ea866396bbb3dd216933240c_1.png)

![](img/4cae25e1ea866396bbb3dd216933240c_3.png)

![](img/4cae25e1ea866396bbb3dd216933240c_5.png)

![](img/4cae25e1ea866396bbb3dd216933240c_7.png)

## 概述 📋

在本节课中，我们将学习量化交易中两个至关重要的基础环节：**金融数据的获取与存储**，以及**时间序列分析的入门知识**。掌握这些技能是后续进行模型构建和策略开发的前提。

---

## 第一部分：金融数据的获取与存储 💾

上一节我们介绍了课程的整体安排。本节中，我们来看看如何为量化交易准备“原材料”——数据。

### 建立数据库的目的

建立本地金融数据库并非最终目的，而是为后续工作服务。其主要意义在于：
1.  **用于建模**：高质量的输入（X）是获得有意义输出（Y）的前提，即“垃圾进，垃圾出”。
2.  **用于交易**：模型在实时决策时，需要快速获取历史数据。

是否需要建立本地数据库，取决于你的具体需求（如数据量、计算环境、网络条件），并非越复杂越好。

### 数据获取来源

对于个人开发者，可以从以下平台免费或低成本获取数据：

*   **Tushare**：提供全面的中国金融市场数据，拥有友好的Python API接口。
*   **Yahoo Finance**：经典的全球金融数据源。
*   **Quandl**：提供大量经济和金融数据集，注册后每日调用次数更多。

> **提示**：本课将以Yahoo Finance为例讲解数据爬取，因为其API相对基础，掌握后能更自如地应对其他数据源。作业将要求使用Tushare进行实践。

### 数据存储方式

![](img/4cae25e1ea866396bbb3dd216933240c_9.png)

常见的金融数据存储方式有以下几种：

![](img/4cae25e1ea866396bbb3dd216933240c_11.png)

![](img/4cae25e1ea866396bbb3dd216933240c_13.png)

![](img/4cae25e1ea866396bbb3dd216933240c_15.png)

*   **CSV文件**：最简单，每个金融产品存为一个文件。适合数据量小或需要共享的场景。
*   **关系型数据库（如MySQL）**：结构化存储时间序列数据的成熟方案，便于查询和管理。
*   **NoSQL数据库（如MongoDB）**：适合存储非结构化的文本数据，如财经新闻。

![](img/4cae25e1ea866396bbb3dd216933240c_17.png)

本节课重点讲解如何使用MySQL存储日级（End-of-Day）交易数据。

### 数据库表设计

![](img/4cae25e1ea866396bbb3dd216933240c_19.png)

一个合理的数据库设计能提升效率。以下是核心表结构示例：

1.  **交易所表 (`exchange`)**：存储交易所信息。
    *   `id`：主键
    *   `abbrev`：交易所缩写（如`NYSE`）
    *   `name`：交易所全称
    *   `city`, `country`, `currency`：所在地和货币
    *   `timezone_offset`：时区偏移

![](img/4cae25e1ea866396bbb3dd216933240c_21.png)

![](img/4cae25e1ea866396bbb3dd216933240c_23.png)

2.  **数据供应商表 (`data_vendor`)**：记录数据来源。
    *   `id`：主键
    *   `name`：供应商名称（如`Yahoo Finance`）
    *   `website_url`, `support_email`：联系方式
    *   `created_date`, `last_updated_date`：记录创建和更新时间

![](img/4cae25e1ea866396bbb3dd216933240c_25.png)

![](img/4cae25e1ea866396bbb3dd216933240c_27.png)

3.  **股票代码表 (`symbol`)**：存储可交易的标的。
    *   `id`：主键
    *   `exchange_id`：外键，关联交易所
    *   `ticker`：股票代码（如`AAPL`）
    *   `instrument`：资产类型（如`stock`）
    *   `name`：公司名称
    *   `sector`：所属板块
    *   `currency`：交易货币

![](img/4cae25e1ea866396bbb3dd216933240c_29.png)

![](img/4cae25e1ea866396bbb3dd216933240c_31.png)

4.  **日价格表 (`daily_price`)**：存储每日价格数据。
    *   `id`：主键
    *   `data_vendor_id`：外键，关联数据供应商
    *   `symbol_id`：外键，关联股票代码
    *   `price_date`：交易日
    *   `created_date`, `last_updated_date`：记录创建和更新时间
    *   `open_price`, `high_price`, `low_price`, `close_price`：开盘、最高、最低、收盘价
    *   `adj_close_price`：**调整后收盘价**（已考虑分红、拆股等公司行为）
    *   `volume`：成交量

![](img/4cae25e1ea866396bbb3dd216933240c_33.png)

### 数据质量注意事项

处理金融数据时，需特别注意以下几点：

*   **公司行为调整**：如股票拆分、分红等会导致价格突变，需使用调整后价格（`adj_close_price`）。
*   **节假日处理**：需从时间序列中剔除交易日，避免产生空白或错误数据。
*   **噪声与异常值**：数据中可能存在瞬时暴涨暴跌的“毛刺”，可使用滤波器平滑处理。
*   **缺失值处理**：可使用中位数、均值或前向/后向填充等方法处理缺失数据。

### 实践流程示例

我们将通过一个完整示例，学习从维基百科获取S&P 500成分股列表，再从Yahoo Finance爬取各股历史数据并存入数据库的流程。

1.  **从维基百科爬取股票列表**：使用`BeautifulSoup`库解析网页，提取股票代码（`ticker`）。
    ```python
    import bs4 as bs
    import requests

    def save_sp500_tickers():
        resp = requests.get('http://en.wikipedia.org/wiki/List_of_S%26P_500_companies')
        soup = bs.BeautifulSoup(resp.text, 'lxml')
        table = soup.find('table', {'class': 'wikitable sortable'})
        tickers = []
        for row in table.findAll('tr')[1:]:
            ticker = row.findAll('td')[0].text
            tickers.append(ticker.strip())
        return tickers
    ```

2.  **将股票代码插入数据库**：使用数据库连接库（如`pymysql`或`sqlalchemy`）执行插入操作。

3.  **从Yahoo Finance获取历史价格**：编写函数，根据股票代码和日期范围获取数据。也可使用`pandas-datareader`库简化。
    ```python
    import pandas as pd
    import pandas_datareader.data as web

    def get_data_from_yahoo(ticker, start_date, end_date):
        # 使用pandas-datareader直接获取数据
        df = web.DataReader(ticker, 'yahoo', start_date, end_date)
        return df
    ```

4.  **将价格数据插入数据库**：循环处理每个股票代码，获取数据并插入`daily_price`表。

5.  **从数据库读取并验证数据**：编写查询，读取数据并与源网站比对，确保正确性。

---

## 第二部分：时间序列分析入门 📈

在掌握了数据获取和存储的基本方法后，我们就可以开始对数据进行分析了。本节将介绍量化交易中一个经典的分析框架：时间序列分析。

### 核心概念：平稳性与均值回归

![](img/4cae25e1ea866396bbb3dd216933240c_35.png)

许多量化策略，特别是**统计套利**策略，基于一个关键假设：资产价格或投资组合的价值序列是**平稳的**。

*   **平稳时间序列**：其统计特性（如均值、方差）不随时间变化。这意味着价格倾向于在长期均值附近波动，即存在**均值回归**现象。
*   **非平稳时间序列/随机游走**：下一时刻的价格与当前时刻无关，不可预测。对于这样的序列，任何基于历史价格的预测都等同于随机猜测。

![](img/4cae25e1ea866396bbb3dd216933240c_37.png)

我们的目标是：**找到或构建出平稳的时间序列**，从而利用其均值回归特性进行交易。

![](img/4cae25e1ea866396bbb3dd216933240c_39.png)

![](img/4cae25e1ea866396bbb3dd216933240c_41.png)

### 如何检验平稳性？

有两种常用的统计检验方法：

![](img/4cae25e1ea866396bbb3dd216933240c_43.png)

1.  **Augmented Dickey-Fuller (ADF) 检验**
    *   **原假设 (H0)**：时间序列具有单位根，即是非平稳的（如随机游走）。
    *   **方法**：计算检验统计量 `ADF Statistic`，并与不同置信水平（如1%， 5%， 10%）下的临界值比较。如果`ADF Statistic`小于临界值，则拒绝原假设，认为序列是平稳的。
    *   **Python实现**：使用`statsmodels`库可以轻松完成。
        ```python
        from statsmodels.tsa.stattools import adfuller

        def check_stationarity_adf(timeseries):
            result = adfuller(timeseries, autolag='AIC')
            print('ADF Statistic: %f' % result[0])
            print('p-value: %f' % result[1])
            print('Critical Values:')
            for key, value in result[4].items():
                print('\t%s: %.3f' % (key, value))
            # 判断：如果p-value很小（如<0.05），且ADF统计量小于5%临界值，则拒绝非平稳假设
            if result[1] <= 0.05:
                print("序列很可能是平稳的。")
            else:
                print("序列很可能有单位根（非平稳）。")
        ```

![](img/4cae25e1ea866396bbb3dd216933240c_45.png)

2.  **计算Hurst指数**
    *   **含义**：Hurst指数 `H` 用于衡量时间序列的长期记忆性。
        *   `H = 0.5`：序列是随机游走（布朗运动）。
        *   `H < 0.5`：序列是均值回归的。
        *   `H > 0.5`：序列是趋势增强的（有持续性）。
    *   **方法**：通过重标极差分析（R/S分析）计算。

### 从单资产到投资组合：协整关系

单只股票的价格序列往往是非平稳的。但是，**两只或多只股票的线性组合却可能是平稳的**。这种关系称为**协整**。

*   **核心思想**：如果股票A和股票B的价格存在长期均衡关系，即 `Price_A ≈ β * Price_B + constant`，那么它们的价差（或残差）序列可能是平稳的。
*   **策略应用**：这就是**配对交易**的基础。当价差偏离历史均值时，做空高价股、做多低价股，期待价差回归时平仓获利。
*   **检验方法**：对两只股票价格进行线性回归，然后对回归的**残差序列**进行ADF检验。如果残差平稳，则说明这两只股票存在协整关系。

```python
import numpy as np
import statsmodels.api as sm
from statsmodels.tsa.stattools import adfuller

def find_cointegrated_pairs(dataframe):
    """在数据框（列是股票代码，行是时间）中寻找协整的股票对"""
    n = dataframe.shape[1]
    pvalue_matrix = np.ones((n, n))
    keys = dataframe.columns
    pairs = []
    for i in range(n):
        for j in range(i+1, n):
            # 1. 对两只股票价格进行线性回归
            stock1 = dataframe[keys[i]]
            stock2 = dataframe[keys[j]]
            result = sm.OLS(stock1, sm.add_constant(stock2)).fit()
            # 2. 获取残差
            residuals = result.resid
            # 3. 对残差进行ADF检验
            pvalue = adfuller(residuals)[1]
            pvalue_matrix[i, j] = pvalue
            if pvalue < 0.05: # 如果p值小于0.05，认为残差平稳，存在协整关系
                pairs.append((keys[i], keys[j], pvalue))
    return pvalue_matrix, pairs
```

---

## 作业安排 📝

以下是本节课的实践作业，请按要求完成：

### 作业一：数据获取与存储实践
*   **任务**：阅读Tushare文档，使用其API获取A股市场数据（例如，2013年至今的日线数据）。
*   **要求**：
    *   **基础**：能够熟练调用API，将所需数据载入内存进行分析。
    *   **进阶**：将获取的数据存入本地MySQL数据库的相应表中。
*   **目的**：确保你拥有随时获取所需金融数据的能力。

![](img/4cae25e1ea866396bbb3dd216933240c_47.png)

### 作业二：股票关系网络可视化
*   **任务**：模仿示例，对沪深300成分股进行关系网络可视化。
*   **步骤**：
    1.  从Tushare获取300只股票的历史价格。
    2.  计算股票两两之间的相关性（或其他自定义的相似度度量）。
    3.  使用聚类算法（如`sklearn`的`SpectralClustering`）和可视化库（如`networkx`, `matplotlib`），生成类似下图的可视化结果：每个节点代表一只股票，连线的颜色深浅代表相关性大小。
*   **提交**：将生成的图片作为作业提交。

### 作业三：平稳性检验
*   **任务**：对沪深300成分股进行ADF检验和/或Hurst指数计算。
*   **要求**：找出其中最可能满足均值回归特性（即平稳或H值较小）的10只股票。
*   **目的**：实践单资产平稳性检验方法。

### 作业四：协整关系搜寻
*   **任务**：在沪深300成分股中，暴力搜索所有两两配对。
*   **要求**：使用协整检验方法，找出最具协整关系（即最可能适用于配对交易）的10对股票。
*   **提示**：注意时间区间的选择，并思考如何避免“过拟合”历史数据。

---

## 总结 🎯

本节课我们一起学习了量化交易的两大基石：

1.  **数据的获取与存储**：我们探讨了建立本地数据库的意义、常见数据源、存储方案设计以及数据清洗的注意事项，并通过一个完整示例演练了从爬取到存储的流程。
2.  **时间序列分析入门**：我们引入了平稳性、随机游走、均值回归和协整等核心概念，并学习了如何使用ADF检验和Hurst指数来识别可交易的统计规律。

![](img/4cae25e1ea866396bbb3dd216933240c_49.png)

这些内容是连接数据与策略的桥梁。从下周开始，我们将把重心转向如何利用这些数据，通过机器学习模型来开发交易策略。请务必完成本周作业，为后续学习打下坚实基础。