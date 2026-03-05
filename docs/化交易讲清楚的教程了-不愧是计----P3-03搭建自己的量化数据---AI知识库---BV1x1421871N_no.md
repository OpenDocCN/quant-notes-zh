# 量化交易实战：03：搭建自己的量化数据库 📊

在本节课中，我们将学习如何获取、存储和管理金融数据，这是进行量化交易分析的基础。我们将从零开始，构建一个本地的金融数据库，并学习如何利用这些数据进行初步的时间序列分析。

## 课程概述

在前两节课中，我们进行了课程预热，介绍了量化交易的基本框架。从本节课开始，我们将平衡编程实践与理论知识。每周的课程安排会包含一次偏重编程的实践课和一次偏重理论的讲解课。请大家务必在周中完成作业，我们会在下次课前进行作业点评和答疑。

编程学习的关键在于实践。本课程的设计理念是“教一学一”，即课堂上讲解一个较复杂的案例（如“航母”），课后作业则是一个相对简单的实践任务（如“飞机”），确保大家能够通过模仿和实践掌握核心技能。

---

## 数据的获取与存储

上一节我们介绍了课程的整体安排，本节中我们来看看量化交易的第一步：数据的获取与存储。建立数据库本身不是目的，而是为后续的建模、回测和交易策略服务。因此，选择何种存储方式（云端调用或本地数据库）完全取决于你的具体需求，应避免“为了建库而建库”。

建立数据库主要有两个意义：
1.  **用于建模**：模型的质量依赖于输入数据的质量。所谓“垃圾进，垃圾出”，高质量的数据是有效模型的基础。
2.  **用于交易**：无论是模型在择时、选股时需要调用历史数据，还是策略执行时需要实时获取信息，都需要一个可靠的数据源支持。

如果你是一个人独立开发，需要根据自身需求来决定是否建立本地数据库。对于简单的日间交易模型，使用在线平台（如优矿、通联数据）的API可能就足够了。但对于复杂的建模任务（如深度学习）、无法连接外网的计算服务器，或者需要高频、定制化的数据调用，建立本地数据库则是必要的。

### 数据获取途径

以下是几个值得推荐的数据获取平台：

*   **Tushare**：一个提供国内金融数据的Python接口，封装了友好的API，可以方便地获取A股、指数、宏观经济等数据。
*   **Yahoo Finance**：提供全球金融市场数据，是国际通用的免费数据源之一。
*   **Quandl**：一个非常全面的经济和金融数据库平台，包含大量宏观和微观数据集。

**课程安排**：本节课我们将以从 **Yahoo Finance** 爬取数据为例进行讲解，因为其访问相对Tushare稍复杂。掌握此方法后，使用Tushare将更加得心应手。课后作业则是让大家练习使用 **Tushare** 的API获取数据。

### 数据存储方式

金融数据常见的存储方式有以下几种：

1.  **CSV文件**：为每只股票或每个金融产品单独保存一个CSV文件。这是最简单直接的方式，易于读写和共享，适合数据量较小或初步探索阶段。
2.  **关系型数据库 (如 MySQL)**：这是存储时间序列金融数据最常用和高效的方式之一。我们可以设计合理的表结构来系统化管理数据。
3.  **非关系型数据库 (如 MongoDB)**：更适合存储非结构化的文本数据，例如财经新闻、股吧评论等，便于后续进行自然语言处理分析。

本节课我们将重点讲解如何使用 **MySQL** 来构建本地金融数据库。

---

## 构建MySQL金融数据库

上一节我们了解了数据的来源和存储形式，本节我们将动手搭建一个MySQL数据库，并设计存储金融数据的表结构。

### 数据库与表设计

首先，你需要在本地安装并配置好MySQL服务器。这个过程相对简单，网上有大量教程可供参考。

数据库表的设计方案因人而异，但有一些通用的最佳实践。以下是一个能够覆盖常见在线数据平台接口的表结构设计，你可以根据具体数据源（如Yahoo Finance或Tushare）进行微调。

核心数据表通常包括：

*   `exchange`：交易所信息表。
*   `data_vendor`：数据提供商信息表。
*   `symbol`：投资标的（股票、指数等）信息表。
*   `daily_price`：每日价格数据表。

![](img/069e14fe192aa5145817207f13bc94ee_1.png)

以下是各表需要包含的关键字段：

**`symbol` 表 (标的物表)**
*   `id`: 主键
*   `exchange_id`: 关联交易所ID
*   `ticker`: 股票代码 (如 `AAPL`, `600036`)
*   `instrument`: 资产类型 (如 `stock`, `index`)
*   `name`: 公司或指数名称
*   `sector`: 所属板块
*   `currency`: 交易货币

**`daily_price` 表 (日价格表)**
*   `id`: 主键
*   `data_vendor_id`: 关联数据提供商ID
*   `symbol_id`: 关联标的物ID
*   `price_date`: 价格日期
*   `created_date` / `last_updated_date`: 记录创建/更新时间
*   `open_price`: 开盘价
*   `high_price`: 最高价
*   `low_price`: 最低价
*   `close_price`: 收盘价
*   `adj_close_price`: **调整后收盘价** (此字段非常重要，已考虑分红、拆股等公司行为)
*   `volume`: 成交量

![](img/069e14fe192aa5145817207f13bc94ee_3.png)

![](img/069e14fe192aa5145817207f13bc94ee_5.png)

![](img/069e14fe192aa5145817207f13bc94ee_7.png)

**关键注意事项**：
*   **调整后价格**：高质量的数据源会提供已调整的价格。如果原始数据未调整，你需要自行根据公式计算，否则价格会出现不合理的跳空，影响模型。
*   **节假日处理**：需要将市场休市日从时间序列中剔除，否则连续几天的价格可能为空值或零值，导致程序错误。
*   **数据噪声**：数据中可能存在异常值（Spikes），需要使用数学滤波器进行平滑处理。
*   **缺失值处理**：可以使用Pandas等工具方便地处理缺失值，常见方法有用中位数、均值或0填充，具体取决于模型需求。

![](img/069e14fe192aa5145817207f13bc94ee_9.png)

![](img/069e14fe192aa5145817207f13bc94ee_11.png)

---

![](img/069e14fe192aa5145817207f13bc94ee_13.png)

## 实战演练：爬取并存储S&P 500数据

![](img/069e14fe192aa5145817207f13bc94ee_15.png)

现在，我们将通过一个完整的例子，学习如何将数据从网络获取、存入数据库，再读取并使用的全过程。我们的任务是：**获取标普500指数成分股列表，并爬取每只股票的历史价格数据存入本地数据库**。

### 步骤一：从维基百科获取成分股列表

![](img/069e14fe192aa5145817207f13bc94ee_17.png)

我们首先使用 `BeautifulSoup` 库从维基百科页面爬取S&P 500的成分股代码（Symbol）。

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
**代码解释**：这个函数向维基百科页面发送请求，使用BeautifulSoup解析HTML，找到存储股票代码的表格，并提取第一列的所有代码，最后返回一个代码列表。

### 步骤二：将股票代码插入数据库

获得代码列表后，我们需要将其插入到之前设计好的 `symbol` 表中。

```python
import pymysql.cursors

def insert_symbols_into_db(tickers):
    # 连接数据库
    connection = pymysql.connect(host='localhost',
                                 user='your_username',
                                 password='your_password',
                                 db='quant_trading',
                                 charset='utf8mb4',
                                 cursorclass=pymysql.cursors.DictCursor)
    try:
        with connection.cursor() as cursor:
            for ticker in tickers:
                # 构造SQL插入语句
                sql = "INSERT INTO `symbol` (`ticker`, `instrument`) VALUES (%s, %s)"
                cursor.execute(sql, (ticker, 'stock'))
        # 提交事务
        connection.commit()
    finally:
        connection.close()
```
**代码解释**：这段代码建立了与MySQL数据库的连接，遍历股票代码列表，为每个代码执行SQL插入操作，将代码和资产类型存入数据库。

### 步骤三：从Yahoo Finance爬取历史价格

对于每个股票代码，我们从Yahoo Finance获取其历史价格数据。这里我们展示一种通用方法（使用 `requests`），但实际上有更简便的方式。

```python
import pandas as pd
import datetime

# 简便方法：使用pandas_datareader (Yahoo接口可能已变更，此为例)
from pandas_datareader import data as pdr
import yfinance as yf # 替代方案
yf.pdr_override()

def get_stock_data_from_yahoo(ticker, start_date, end_date):
    # 使用pandas_datareader直接获取数据框
    df = pdr.get_data_yahoo(ticker, start=start_date, end=end_date)
    return df
```
**代码解释**：`pandas_datareader` 库提供了直接对接Yahoo Finance的API，只需一行代码就能获取指定时间段内的股票历史数据，返回一个Pandas DataFrame，包含开盘价、最高价、最低价、收盘价、调整后收盘价和成交量。

### 步骤四：将价格数据插入数据库

获取到单只股票的DataFrame后，我们需要将其逐行插入到 `daily_price` 表中。

```python
def insert_daily_price_into_db(symbol_id, price_df):
    connection = pymysql.connect(...) # 同上，连接数据库
    try:
        with connection.cursor() as cursor:
            for index, row in price_df.iterrows():
                sql = """INSERT INTO `daily_price`
                         (`symbol_id`, `price_date`, `open_price`, `high_price`, `low_price`, `close_price`, `adj_close_price`, `volume`)
                         VALUES (%s, %s, %s, %s, %s, %s, %s, %s)"""
                cursor.execute(sql, (symbol_id, index.date(), row['Open'], row['High'], row['Low'], row['Close'], row['Adj Close'], row['Volume']))
        connection.commit()
    finally:
        connection.close()
```
**代码解释**：此函数遍历DataFrame的每一行（即每个交易日），将对应的价格数据作为一条记录插入到 `daily_price` 表中。

### 步骤五：主程序流程

最后，我们将上述步骤串联起来，形成一个完整的脚本。

```python
def main():
    # 1. 获取股票代码列表
    tickers = save_sp500_tickers()
    # 2. 将代码插入数据库，并获取它们在数据库中的ID (假设已实现)
    # 3. 定义时间范围
    start = datetime.datetime(2010, 1, 1)
    end = datetime.datetime(2020, 1, 1)
    # 4. 遍历每个股票代码
    for ticker in tickers[:10]: # 示例：只处理前10只
        print(f"Processing {ticker}")
        # 获取该股票的价格数据
        price_df = get_stock_data_from_yahoo(ticker, start, end)
        # 假设通过ticker从数据库查到对应的symbol_id
        symbol_id = get_symbol_id_from_db(ticker)
        # 将价格数据插入数据库
        insert_daily_price_into_db(symbol_id, price_df)

if __name__ == '__main__':
    main()
```

---

## 时间序列分析入门

在成功获取并存储数据后，我们便可以立即开始最简单的分析：时间序列分析。这是量化交易中一个经典且重要的流派，尤其在数据量不大的时代是主流方法。理解它有助于我们后续更好地运用机器学习模型。

![](img/069e14fe192aa5145817207f13bc94ee_19.png)

### 核心概念：平稳性

![](img/069e14fe192aa5145817207f13bc94ee_21.png)

许多统计套利策略（如均值回归策略）的核心假设是：**投资组合的价格序列是平稳的**。

*   **平稳时间序列**：其统计特性（如均值、方差）不随时间变化。这意味着价格会围绕一个长期均值波动，存在“均值回归”现象，从而可能被预测。
*   **非平稳时间序列**：如随机游走。下一时刻的价格与历史价格无关，类似于抛硬币，从长远看无法预测。

**我们的目标**：单只股票的价格往往是非平稳的（随机游走）。但通过构建一个股票组合，我们有可能得到一个平稳的价格序列，从而基于此开发策略。

### 平稳性检验

如何检验一个时间序列是否平稳？这里介绍两种常见方法。

**1. ADF检验**
ADF检验是一种统计检验方法，用于判断时间序列是否存在单位根（单位根存在意味着非平稳）。在Python中，我们可以使用 `statsmodels` 库轻松完成。

```python
from statsmodels.tsa.stattools import adfuller

def check_stationarity_adf(price_series):
    """
    输入: price_series (Pandas Series), 例如一只股票的收盘价序列。
    输出: 打印ADF检验结果。
    """
    result = adfuller(price_series)
    print('ADF Statistic: %f' % result[0])
    print('p-value: %f' % result[1])
    print('Critical Values:')
    for key, value in result[4].items():
        print('\t%s: %.3f' % (key, value))
    # 判断：如果ADF统计值小于临界值（或p-value小于0.05），则拒绝原假设（非平稳），认为序列是平稳的。
    if result[1] <= 0.05:
        print("序列很可能是平稳的。")
    else:
        print("序列很可能不是平稳的（具有单位根）。")

# 示例：检验亚马逊股票价格
import yfinance as yf
amzn = yf.download('AMZN', '2000-01-01', '2015-01-01')
check_stationarity_adf(amzn['Adj Close'])
```
运行后你可能会发现，单只股票（如AMZN）的价格序列是非平稳的。

**2. Hurst指数**
Hurst指数是另一个衡量时间序列趋势和均值回归特性的指标。
*   `H = 0.5`：表示随机游走。
*   `H < 0.5`：表示均值回归序列。
*   `H > 0.5`：表示趋势序列。

计算Hurst指数需要自己实现或使用第三方库。

### 从单股票到配对：协整关系

既然单只股票很难平稳，我们可以尝试寻找两只或多只股票，它们的线性组合是平稳的。这就是**配对交易**的统计基础。

**核心思想**：如果股票A和股票B的价格存在长期均衡关系，即 `Price_A ≈ β * Price_B + ε`，并且残差项 `ε` 是一个平稳序列（均值回归），那么当 `ε` 偏离均值时，我们就可以进行套利操作：做多被低估的，做空被高估的，等待价差回归。

**检验方法**：协整检验。我们可以先对两只股票的价格进行线性回归，得到残差序列，然后对这个残差序列进行ADF检验。如果残差序列是平稳的，则说明这两只股票存在协整关系。

```python
import statsmodels.api as sm

def find_cointegrated_pairs(price_df):
    """
    输入: price_df (DataFrame), 列是各股票的价格序列。
    输出: 协整配对列表。
    """
    n = price_df.shape[1]
    pvalue_matrix = np.ones((n, n))
    keys = price_df.keys()
    pairs = []
    for i in range(n):
        for j in range(i+1, n):
            stock1 = price_df[keys[i]]
            stock2 = price_df[keys[j]]
            # 第一步：线性回归
            result = sm.OLS(stock1, sm.add_constant(stock2)).fit()
            # 第二步：对残差进行ADF检验
            _, pvalue, _ = adfuller(result.resid)
            pvalue_matrix[i, j] = pvalue
            if pvalue < 0.05: # 认为在5%显著性水平下协整
                pairs.append((keys[i], keys[j], pvalue))
    return pvalue_matrix, pairs

# 示例：在一个板块内寻找协整配对
# energy_stocks = price_df[['XOM', 'CVX', 'COP', ...]] # 能源股价格DataFrame
# pvalues, cointegrated_pairs = find_cointegrated_pairs(energy_stocks)
```

---

## 作业安排

以下是本节课的实践作业，请根据自身编程基础选择完成：

![](img/069e14fe192aa5145817207f13bc94ee_23.png)

1.  **基础作业（数据获取）**：学习使用 **Tushare** 的API，尝试获取A股市场（例如沪深300成分股）从2013年至今的日线交易数据。你可以选择：
    *   **进阶**：将数据存入你设计的本地MySQL数据库的相应表中。
    *   **基础**：将每只股票的数据保存为单独的CSV文件。
    *   **底线**：确保你能熟练调用API，随时获取任意股票在任意时间段的数据。

2.  **进阶作业一（数据可视化）**：在获取沪深300成分股历史数据的基础上，模仿网络上的案例，绘制股票相关性网络图。其中：
    *   **节点**：代表一只股票。
    *   **边**：代表两只股票之间的相关性（颜色越深，相关性越高）。
    *   **目标**：将复杂的相关性关系以降维的方式直观展示在二维平面上。你需要读懂并修改示例代码，使用Tushare的数据重现类似图像。

3.  **进阶作业二（平稳性检验）**：对沪深300成分股逐一进行ADF检验或计算Hurst指数，判断各股票价格序列的平稳性。将结果进行排序，找出最接近平稳（均值回归）的若干只股票。

4.  **挑战作业（协整配对发现）**：编写一个脚本，对沪深300成分股进行两两协整关系检验。找出最具协整关系的股票对（Top 10），并输出结果。思考：在不同的时间区间内，协整关系是否稳定？

---

## 下节预告与机器学习展望

本节课我们一起学习了金融数据的获取、存储和传统的时间序列分析方法。下一节课，我们将正式进入机器学习的领域。

机器学习为量化交易提供了另一种强大的范式。其核心框架可以概括为：
`Y_t = F(X_t) + ε`
其中，`X_t` 是在t时刻构造的特征向量（可包含历史价格、技术指标、宏观数据等），`F` 是我们选择的模型（如线性回归、随机森林、神经网络），`Y_t` 是预测目标（如未来涨跌、收益率），`ε` 是误差。

我们将重点学习如何针对量化交易问题构造有效的特征（特征工程），如何根据问题特性选择合适的模型（而不仅仅是暴力尝试所有模型），以及如何避免机器学习在量化中的常见陷阱。我们将使用 `scikit-learn` 和 `Keras` 等库来实践。

---

## 总结

在本节课中，我们深入探讨了量化交易的数据基石：
1.  **明确了需求**：根据个人或团队需求决定数据存储方案。
2.  **掌握了方法**：学习了从网络（如Yahoo Finance, Tushare）获取数据的多种途径（爬虫、API）。
3.  **实践了存储**：一步步搭建了本地MySQL数据库，并设计了存储金融数据的表结构。
4.  **入门了分析**：介绍了时间序列平稳性的概念，学习了ADF检验和协整关系，为统计套利策略打下了基础。

记住，数据处理是量化交易中最耗时但也是最关键的一环。扎实地完成本节课的实践内容，将为后续所有的模型构建和策略开发铺平道路。