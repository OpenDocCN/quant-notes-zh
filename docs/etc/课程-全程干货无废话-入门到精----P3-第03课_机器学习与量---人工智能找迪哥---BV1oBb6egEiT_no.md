# 量化交易实战：03：金融数据获取、存储与时间序列分析入门

![](img/3e382cf05a0bd9d1c8818179baf0d0cf_0.png)

## 概述

在本节课中，我们将学习量化交易中两个至关重要的基础环节：金融数据的获取与存储，以及时间序列分析的入门知识。我们将从零开始，学习如何从网络获取数据、将其存入本地数据库，并利用基础的统计方法对数据进行初步分析，为后续的建模打下坚实基础。

## 金融数据的获取与存储

上一节我们介绍了课程的整体安排，本节中我们来看看量化交易的“原材料”——数据。没有高质量的数据，任何复杂的模型都无从谈起。

### 数据获取的目的与考量

建立数据库并非最终目的，而是为后续的建模、回测、风控和策略执行提供服务。是否需要建立本地数据库，取决于你的具体需求：
*   **简单任务**：对于日间交易且无需实时数据的简单模型，使用在线平台（如优矿、通联数据）的API直接调用可能就足够了。
*   **复杂任务**：对于需要离线计算、深度学习模型或计算服务器无法连接外网的情况，则必须建立本地数据库。

核心原则是：**根据需求选择方案，避免为了建库而建库**。

### 数据获取来源

以下是几个推荐的数据获取平台：
*   **Tushare**：专注于中国金融数据，提供了封装良好、功能强大的Python API，能极大简化数据获取流程。
*   **Yahoo Finance**：经典的全球金融数据源，数据质量较高。
*   **Quandl**：提供大量宏观和微观经济数据，是非常全面的研究数据平台。

### 数据存储方式

金融数据主要有以下几种存储方式：
1.  **CSV文件**：最简单直接，每个金融产品（如一只股票）存为一个CSV文件。适合数据量小或需要共享的场景。
2.  **关系型数据库**：如MySQL，是存储时间序列型金融数据最常用和高效的方式。
3.  **NoSQL数据库**：如MongoDB，适合存储非结构化的文本数据（如财经新闻）。

![](img/3e382cf05a0bd9d1c8818179baf0d0cf_2.png)

### 数据格式与注意事项

存储历史数据时，需要关注以下信息并小心处理常见问题：
*   **必需信息**：交易所、数据来源、股票代码、价格。
*   **企业行为**：如分红、拆股，需要进行复权调整，否则价格会出现跳跃。
*   **国家假日**：需从时间序列中剔除，避免产生空白或零值数据点。
*   **数据噪声**：如价格毛刺，需要使用滤波器进行平滑处理。
*   **缺失数据**：需要进行填充，常用方法有填零、填均值或中位数。

![](img/3e382cf05a0bd9d1c8818179baf0d0cf_4.png)

### 实战：构建本地MySQL金融数据库

![](img/3e382cf05a0bd9d1c8818179baf0d0cf_6.png)

![](img/3e382cf05a0bd9d1c8818179baf0d0cf_8.png)

我们将通过一个完整示例，学习如何从维基百科获取标普500成分股列表，再从雅虎财经爬取每只股票的历史数据，并存储到本地MySQL数据库中。

以下是核心步骤的代码框架：

1.  **从维基百科爬取股票代码列表**：
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
            tickers.append(ticker)
        return tickers
    ```

2.  **将股票代码插入数据库**：
    ```python
    import mysql.connector

    def insert_tickers_into_db(ticker_list):
        # 连接数据库
        db = mysql.connector.connect(host='localhost', user='youruser', passwd='yourpass', database='finance')
        cursor = db.cursor()
        # 执行插入操作
        for ticker in ticker_list:
            sql = "INSERT INTO symbol (ticker) VALUES (%s)"
            val = (ticker,)
            cursor.execute(sql, val)
        db.commit()
        print(cursor.rowcount, "records inserted.")
    ```

3.  **从雅虎财经获取历史价格数据**（使用pandas-datareader简化操作）：
    ```python
    import pandas_datareader.data as web
    import datetime

    def get_stock_data_from_yahoo(ticker, start_date, end_date):
        # 获取数据
        data = web.DataReader(ticker, 'yahoo', start_date, end_date)
        # 通常我们关注调整后的收盘价
        prices = data['Adj Close']
        return prices
    ```

4.  **将价格数据插入数据库**（流程与插入股票代码类似）。

**更简便的方法**：对于雅虎财经数据，可以直接使用`pandas-datareader`库，一行代码获取数据：
```python
data = web.DataReader('AAPL', 'yahoo', start, end)
```

## 时间序列分析入门

在掌握了数据获取与存储的基本能力后，我们立即可以上手对数据进行分析。时间序列分析是量化交易中历史最悠久、也最基础的分析方法之一。

### 核心概念：均值回归

时间序列分析的一大类策略基于**均值回归**思想。其核心假设是，资产价格会围绕一个长期均值上下波动。当价格偏离均值时，它有很高的概率会向均值回归。
*   **平稳过程**：如果一个时间序列是平稳的，它通常就表现出均值回归的特性。
*   **随机游走**：如果价格变化是随机的，下一刻的价格与历史无关，那么任何基于历史的预测都将失效。

我们的目标就是**寻找或构建出平稳的时间序列（投资组合）**，从而利用其均值回归特性进行套利。

### 平稳性检验

如何判断一个时间序列是否平稳？以下是两种常用方法：

1.  **ADF检验**：这是一种统计检验方法，用于判断时间序列是否存在单位根。若存在单位根，则为非平稳序列（如随机游走）。
    ```python
    from statsmodels.tsa.stattools import adfuller

    def adf_test(timeseries):
        # 执行ADF检验
        result = adfuller(timeseries, autolag='AIC')
        # 输出结果
        print('ADF Statistic: %f' % result[0])
        print('p-value: %f' % result[1])
        print('Critical Values:')
        for key, value in result[4].items():
            print('\t%s: %.3f' % (key, value))
        # 判断：若p-value小于显著性水平（如0.05），则拒绝原假设（非平稳），认为序列是平稳的。
    ```

2.  **赫斯特指数**：用于判断时间序列的趋势性。
    *   `H = 0.5`：表示序列是随机游走。
    *   `H < 0.5`：表示序列是均值回归的。
    *   `H > 0.5`：表示序列是趋势增强的（持久性）。
    ```python
    def hurst(ts):
        lags = range(2, 100)
        tau = [np.sqrt(np.std(np.subtract(ts[lag:], ts[:-lag]))) for lag in lags]
        poly = np.polyfit(np.log(lags), np.log(tau), 1)
        return poly[0]*2.0
    ```

### 从单只股票到投资组合：配对交易基础

单只股票的价格序列往往是非平稳的。但我们可以尝试寻找两只或多只股票，它们的线性组合能形成一个平稳序列。这就是**配对交易**的统计基础。

其模型可以表示为：
`Y(t) = α + β * X(t) + ε(t)`
其中，`ε(t)` 是残差序列。如果 `ε(t)` 是平稳的，那么我们就找到了一个协整关系，可以利用 `Y` 和 `X` 的价格偏离进行套利。

检验步骤：
1.  对两只股票的价格序列 `X` 和 `Y` 进行线性回归，得到残差 `ε`。
2.  对残差序列 `ε` 进行ADF检验，判断其是否平稳。

## 作业安排

以下是本节课的实践作业，旨在巩固所学知识：

1.  **数据获取实践**：使用Tushare的API，获取指定时间区间（如2013年至今）的A股交易数据，并存入本地CSV文件或MySQL数据库。目标是能够随时调取任意股票在任意时间段的数据。
2.  **股票相关性可视化**：获取沪深300成分股数据，计算股票间的相关性（或自定义相似度），使用网络图进行可视化。节点代表股票，边的颜色深度代表相关性强弱。
3.  **平稳性检验**：对沪深300成分股逐一进行ADF检验和/或赫斯特指数计算，找出最可能具备均值回归特性的几只股票。
4.  **协整关系搜寻**：在沪深300成分股中，暴力搜索所有两两配对，通过协整检验找出最具协整关系的Top 10股票对。

## 总结与展望

本节课中我们一起学习了量化交易的数据基石。我们掌握了从网络获取金融数据、设计并构建本地数据库的方法，并学习了时间序列分析的基础——平稳性检验与均值回归思想，为简单的统计套利策略（如配对交易）打下了基础。

数据是模型的输入，质量决定输出的上限。而时间序列分析为我们提供了一套经典的工具，来初步理解数据的内在特性。

![](img/3e382cf05a0bd9d1c8818179baf0d0cf_10.png)

在接下来的课程中，我们将进入机器学习的领域。我们将看到，当数据量足够大时，如何利用更强大的模型（如逻辑回归、支持向量机、随机森林、LSTM等）来捕捉市场中的复杂模式。但请记住，**理解模型背后的原理，根据任务特点选择合适的模型并进行调整，远比暴力尝试所有模型更重要**。我们将秉承这一原则，逐步搭建起从数据到策略的完整量化交易体系。