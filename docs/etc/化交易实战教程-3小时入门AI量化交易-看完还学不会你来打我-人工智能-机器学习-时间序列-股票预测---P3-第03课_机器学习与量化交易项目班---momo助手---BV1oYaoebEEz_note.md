# Python金融分析与量化交易实战教程：P3：第03课_机器学习与量化交易项目班

![](img/71029a577a07946fcdfddf31ad5bbddb_0.png)

## 概述

在本节课中，我们将学习两个核心主题：金融数据的获取与存储，以及时间序列分析的基础知识。掌握这些内容是进行量化交易建模的第一步，它们将为我们后续的机器学习模型应用打下坚实基础。

## 金融数据的获取与存储

上一节我们介绍了课程的整体安排，本节中我们来看看如何为量化交易准备数据。数据的获取与存储本身不是目的，而是为后续的建模、回测和交易策略执行服务的。我们需要根据实际需求来决定数据存储的方式，避免为了建库而建库。

### 建立数据库的意义

建立本地金融数据库主要有两个目的：
1.  **用于建模**：高质量的模型输入（X）是获得有意义输出（Y）的前提。无论是模型建立、评估、回测还是风控，都需要可靠的数据支持。
2.  **用于交易**：在实盘或模拟交易中，模型需要实时或定期获取历史数据来指导交易决策，这也需要一个高效的数据存取环境。

对于个人开发者，需要根据自身需求（如是否需要离线计算、模型复杂度、计算资源等）来决定是否搭建本地数据库。对于团队，数据管理、清洗、建模和风控可能由不同角色负责。

### 数据获取来源

以下是几个推荐的数据获取平台：

*   **Tushare**：一个提供国内金融数据（股票、基金、期货等）的Python接口，功能强大且易于使用，能大大减少数据爬取的工作量。
*   **Yahoo Finance**：提供全球金融市场数据，是国际通用的免费数据源之一。
*   **Quandl**：一个汇集了大量经济和金融数据的平台，数据质量高，涵盖宏观和微观数据。

### 数据存储方式

常见的金融数据存储方式有以下几种：

1.  **CSV文件**：最简单的方式，每个金融产品（如一只股票）的历史数据存为一个CSV文件。适合数据量小或需要与人共享的场景。
2.  **关系型数据库（如MySQL）**：适合存储结构化的时间序列数据（如每日股价），便于进行复杂的查询和管理。
3.  **NoSQL数据库（如MongoDB）**：适合存储非结构化的数据，如财经新闻、社交媒体评论等文本信息。

本节课我们将重点学习如何使用MySQL存储时间序列数据。

### 数据库表设计

![](img/71029a577a07946fcdfddf31ad5bbddb_2.png)

一个合理的数据库表设计能有效组织数据。以下是一个常见的、用于存储日级（End-of-Day）交易数据的表结构示例：

*   **exchange（交易所表）**：存储交易所信息。
    *   `id`：主键
    *   `abbrev`：交易所缩写（如`SSE`）
    *   `name`：交易所全称
    *   `city`, `country`, `currency`：所在城市、国家、货币
    *   `timezone_offset`：时区偏移
*   **data_vendor（数据供应商表）**：存储数据来源信息。
    *   `id`：主键
    *   `name`：供应商名称（如`Yahoo Finance`）
*   **symbol（代码表）**：存储金融产品代码。
    *   `id`：主键
    *   `exchange_id`：外键，关联交易所
    *   `ticker`：股票代码（如`AAPL`）
    *   `instrument`：产品类型（如`stock`）
*   **daily_price（日价格表）**：存储每日价格数据。
    *   `id`：主键
    *   `data_vendor_id`：外键，关联数据供应商
    *   `symbol_id`：外键，关联股票代码
    *   `price_date`：日期
    *   `open_price`, `high_price`, `low_price`, `close_price`：开盘、最高、最低、收盘价
    *   `adj_close_price`：调整后收盘价（已考虑分红、拆股等）
    *   `volume`：成交量

### 实战演练：从维基百科和雅虎财经获取S&P 500数据

![](img/71029a577a07946fcdfddf31ad5bbddb_4.png)

![](img/71029a577a07946fcdfddf31ad5bbddb_6.png)

我们将通过一个综合任务来练习数据获取与存储的全流程：首先从维基百科页面爬取S&P 500成分股的股票代码列表，然后针对每个代码，从雅虎财经获取其历史价格数据，最后将所有数据存入本地MySQL数据库。

![](img/71029a577a07946fcdfddf31ad5bbddb_8.png)

以下是关键步骤的代码示例：

1.  **从维基百科爬取股票代码列表**：
    ```python
    import requests
    from bs4 import BeautifulSoup

    def obtain_parse_wiki_snp500():
        # 发送HTTP请求获取页面内容
        response = requests.get("https://en.wikipedia.org/wiki/List_of_S%26P_500_companies")
        soup = BeautifulSoup(response.text, 'html.parser')
        # 找到包含股票代码的表格
        symbols_list = []
        # ... 解析表格，提取代码列数据 ...
        return symbols_list
    ```

2.  **将股票代码插入数据库**：
    ```python
    import MySQLdb

    def insert_snp500_symbols(symbols):
        # 连接数据库
        db = MySQLdb.connect(host='localhost', user='your_user', passwd='your_pwd', db='your_db')
        cursor = db.cursor()
        # 遍历代码列表，执行插入操作
        for symbol in symbols:
            cursor.execute("INSERT INTO symbol (ticker) VALUES (%s)", [symbol])
        db.commit()
        db.close()
    ```

3.  **从雅虎财经获取单只股票历史数据**：
    ```python
    import pandas as pd

    # 方法一：使用pandas_datareader（更简单）
    import pandas_datareader.data as web
    def get_data_from_yahoo(ticker):
        data = web.DataReader(ticker, 'yahoo', start='2000-01-01', end='2015-01-01')
        return data['Adj Close']  # 获取调整后收盘价序列

    # 方法二：使用requests直接调用API（更灵活）
    import requests
    import json
    def get_data_from_yahoo_direct(ticker):
        # 构造API请求URL
        url = f"https://query1.finance.yahoo.com/v8/finance/chart/{ticker}"
        params = {...} # 时间范围等参数
        response = requests.get(url, params=params)
        data = json.loads(response.text)
        # 解析返回的JSON数据，提取价格序列
        # ...
    ```

4.  **将价格数据插入数据库并读取验证**：
    ```python
    # 将获取的价格数据（DataFrame格式）插入daily_price表
    # ... (使用MySQLdb或SQLAlchemy执行批量插入)

    # 从数据库读取数据并打印验证
    def read_daily_price_from_db(symbol_id):
        db = MySQLdb.connect(...)
        query = "SELECT * FROM daily_price WHERE symbol_id = %s ORDER BY price_date"
        df = pd.read_sql_query(query, db, params=[symbol_id])
        print(df.head())
        return df
    ```

### 数据质量注意事项

在处理金融数据时，需要特别注意以下几点，它们容易导致模型出错：

*   **企业行为**：如分红、拆股会导致股价出现跳跃。高质量的数据源会提供调整后价格（`adj_close_price`），否则需要自行计算调整。
*   **节假日**：交易日历中需要剔除国家假日，否则时间序列会出现间断，可能引发程序错误。
*   **噪声与异常值**：数据中可能存在瞬间暴涨暴跌的“毛刺”，需要使用滤波器等方法进行平滑处理。
*   **缺失值处理**：对于缺失的数据点，常见的处理方法包括向前/向后填充、使用中位数或均值填充等，具体方法取决于模型需求。

## 时间序列分析基础

在掌握了数据获取与存储的方法后，我们来看看如何对金融时间序列数据进行最基础的分析。时间序列分析是量化交易，特别是统计套利策略的基石。

### 核心概念：平稳性

时间序列分析的一个核心目标是判断序列是否**平稳**。一个平稳的时间序列其统计特性（如均值、方差）不随时间变化。这对预测至关重要，因为非平稳序列（如随机游走）的下一刻价格与历史无关，理论上无法预测。

**随机游走公式**：
`Price_t = Price_{t-1} + ε_t`，其中 `ε_t` 是白噪声。

我们的目标是找到或构建**非随机游走**的平稳序列，从而进行预测和套利。

### 均值回归策略

一类重要的策略基于**均值回归**假设：资产价格倾向于围绕其长期均值波动。当价格偏离均值时，它最终会回归。

一个经典的均值回归模型是**Ornstein-Uhlenbeck过程**，其微分方程形式为：
`dX_t = θ (μ - X_t) dt + σ dW_t`
其中：
*   `X_t`：在时间t的价格。
*   `θ`：回归速率。
*   `μ`：长期均值。
*   `σ`：波动率。
*   `W_t`：维纳过程（布朗运动）。

该公式表明，价格的变化`dX_t`与当前价格和均值的差`(μ - X_t)`成正比，并加上一个随机噪声。

### 平稳性检验

如何用程序检验一个时间序列是否平稳？以下是两种常用方法：

1.  **ADF检验**：检验序列是否存在单位根。若存在单位根，则为非平稳序列（如随机游走）。
    ```python
    from statsmodels.tsa.stattools import adfuller

    # 假设`price_series`是一个pandas Series，包含股票价格
    result = adfuller(price_series)
    print('ADF Statistic: %f' % result[0])
    print('p-value: %f' % result[1])
    print('Critical Values:')
    for key, value in result[4].items():
        print('\t%s: %.3f' % (key, value))
    # 如果ADF统计量小于临界值（或p-value小于0.05），则拒绝原假设，认为序列平稳。
    ```

2.  **赫斯特指数**：用于判断时间序列的趋势性。
    *   `H = 0.5`：随机游走。
    *   `H < 0.5`：均值回归序列。
    *   `H > 0.5`：趋势序列。
    ```python
    # 计算赫斯特指数的简化示例
    def hurst(ts):
        lags = range(2, 100)
        tau = [np.sqrt(np.std(np.subtract(ts[lag:], ts[:-lag]))) for lag in lags]
        poly = np.polyfit(np.log(lags), np.log(tau), 1)
        return poly[0]*2.0
    ```

### 从单资产到投资组合：协整关系

单只股票的价格序列往往是非平稳的。但我们可以将多只股票组合成一个投资组合，使得这个组合的价值序列是平稳的。这就是**配对交易**的基础。

**核心思想**：找到两只或多只价格序列存在长期均衡关系的股票。即使各自非平稳，它们的线性组合（价差）却是平稳的。

**数学模型**：
`Y_t = α + β * X_t + ε_t`
其中`Y_t`和`X_t`是两只股票的价格，`ε_t`是残差序列。如果`ε_t`是平稳的，则`X`和`Y`存在协整关系。

**检验步骤**：
1.  对两只股票的价格序列`X`和`Y`进行线性回归，得到残差`ε`。
2.  对残差序列`ε`进行ADF检验，判断其是否平稳。若平稳，则`X`和`Y`协整。

```python
import statsmodels.api as sm
# 假设df是一个DataFrame，包含两只股票‘stock_A’和‘stock_B’的价格
X = sm.add_constant(df['stock_A']) # 添加常数项
model = sm.OLS(df['stock_B'], X).fit()
residuals = model.resid # 获取残差
# 对残差进行ADF检验
adf_result = adfuller(residuals)
```

### 机器学习方法展望

传统时间序列分析适用于数据量较小的场景。当拥有海量数据时，**机器学习**提供了更强大的工具。机器学习模型可以表示为：
`Y_t = F(X_t) + ε_t`
其中`X_t`可以是一个高维特征向量（包含滞后价格、技术指标、基本面数据、新闻情感等），`F`是待学习的模型（如线性回归、支持向量机、随机森林、LSTM神经网络等），`Y_t`是预测目标（如未来涨跌或价格）。

与盲目尝试所有模型不同，成功的机器学习量化需要：
1.  **理解业务与模型**：根据预测目标（分类、回归、异常检测）和特征性质选择合适的模型。
2.  **严谨的特征工程**：从原始数据中构建有预测力的特征。
3.  **合理的回测与验证**：避免过拟合和前瞻性偏差，确保策略在样本外有效。

## 本节课作业

1.  **数据获取实践**：使用Tushare的API，获取A股市场（如沪深300成分股）指定时间区间（例如2013年至今）的历史日线数据，并将其存入本地CSV文件或MySQL数据库。
2.  **股票关系可视化**：获取沪深300成分股的历史数据，计算任意两只股票收益率序列的相关系数。使用网络图进行可视化：节点代表股票，边的颜色深度代表股票间的相关性强度。尝试使用聚类算法（如谱聚类）对股票进行分组。
3.  **平稳性检验**：对沪深300成分股的价格序列（或收益率序列）逐一进行ADF检验和/或赫斯特指数计算，找出最可能具备均值回归特性的若干只股票。
4.  **协整关系挖掘**：在沪深300成分股中，暴力搜索所有两两配对，进行协整检验（线性回归后对残差做ADF检验），找出协整关系最强的10对股票。

## 总结

![](img/71029a577a07946fcdfddf31ad5bbddb_10.png)

本节课我们一起学习了量化交易的数据基石。我们首先探讨了如何根据需求获取和存储金融数据，并完成了从网络爬取到数据库存储的完整流程实践。接着，我们深入时间序列分析领域，学习了平稳性检验、均值回归概念以及寻找资产间协整关系的方法，这些都是统计套利策略的核心。最后，我们对机器学习在量化中的应用做了前瞻性介绍。通过本课的学习和作业实践，你将具备为量化模型准备数据并进行基础时间序列分析的能力。