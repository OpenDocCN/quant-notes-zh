# 量化交易入门：03：搭建自己的量化数据库 📊

在本节课中，我们将学习如何获取、存储和管理金融数据，这是构建量化交易系统的基础。我们将从零开始，介绍如何从网络获取数据，并将其存储到本地数据库中，以便后续的分析和建模使用。

---

## 课程概述

上一节我们进行了课程介绍和热身。本节中，我们将开始实际的编程工作，重点学习金融数据的获取与存储。掌握这些技能是进行后续量化分析和策略开发的前提。

---

## 数据获取与存储的目的

建立数据库的主要目的是为后续的建模、回测和交易策略开发服务。数据的质量直接决定了模型的效果，即“垃圾进，垃圾出”。因此，确保有一个可靠、高效的数据存储和读取机制至关重要。

具体来说，建立数据库的意义有两个：
1.  **用于建模**：无论是模型的建立、评估、回测还是风控，都需要可靠的历史数据作为输入。
2.  **用于交易**：在线交易策略需要实时或准实时地获取历史信息来指导交易决策。

是否需要建立本地数据库，取决于你的具体需求。对于简单的日间交易模型，使用在线平台（如优矿、通联数据）的API可能就足够了。但对于复杂的计算任务、无法连接外网的服务器，或者希望完全控制数据的情况，建立本地数据库则是必要的。

---

## 金融数据的获取

假设你个人起步，没有预算购买昂贵的商用数据。那么，从公开网络获取数据是主要途径。这通常涉及使用爬虫程序或平台提供的API。

以下是几个值得推荐的数据源：

*   **Tushare**：一个提供中国金融市场数据的平台，封装了功能强大且易用的Python API接口，可以快速获取各类数据。
*   **Yahoo Finance**：提供全球金融数据，质量较高，是国际市场的常用数据源。
*   **Quandl**：一个非常全面的经济和金融数据平台，包含大量宏观和微观数据集，适合研究使用。

在本课程中，我们将以从 **Yahoo Finance** 获取数据为例进行讲解。掌握了这种方法后，使用Tushare等平台会更加得心应手。

---

## 金融数据的存储方式

获取数据后，需要选择合适的存储方式。常见的有以下几种：

1.  **CSV文件**：最简单的方式，将每只股票的数据存为一个CSV文件。适合数据量小、需要与人共享或快速查看的场景。
2.  **关系型数据库**：如 **MySQL**。这是存储时间序列金融数据最常用、最规范的方式，便于进行复杂的查询和管理。
3.  **NoSQL数据库**：如 **MongoDB**。更适合存储非结构化的文本数据，例如财经新闻、评论等。

本节课我们将重点学习如何使用 **MySQL** 来存储金融数据。

![](img/954070abbf42c1312b9cc130722e6421_1.png)

---

## 建立本地MySQL数据库

![](img/954070abbf42c1312b9cc130722e6421_3.png)

![](img/954070abbf42c1312b9cc130722e6421_5.png)

![](img/954070abbf42c1312b9cc130722e6421_7.png)

首先，你需要在本地安装并配置MySQL服务器。在Linux系统上，安装过程通常很简单。

![](img/954070abbf42c1312b9cc130722e6421_9.png)

![](img/954070abbf42c1312b9cc130722e6421_11.png)

安装完成后，需要创建一个用户并授予权限，以便通过程序与数据库交互。以下是一个基本的设置流程：

![](img/954070abbf42c1312b9cc130722e6421_13.png)

```sql
# 以root身份登录MySQL
mysql -u root -p

![](img/954070abbf42c1312b9cc130722e6421_15.png)

# 创建一个新数据库
CREATE DATABASE quant_db;

![](img/954070abbf42c1312b9cc130722e6421_17.png)

# 创建一个新用户并设置密码
CREATE USER 'quant_user'@'localhost' IDENTIFIED BY 'your_password';

# 授予用户对数据库的所有权限
GRANT ALL PRIVILEGES ON quant_db.* TO 'quant_user'@'localhost';

# 刷新权限
FLUSH PRIVILEGES;
```

这个过程没有太多技术含量，但要求你动手实践一遍，确保环境配置正确。

---

## 设计数据库表结构

数据库表的设计至关重要。一个良好的设计应该能覆盖数据的主要维度。以下是一个常见的设计方案，包含几个核心表：

**1. 交易所表**
存储交易所的基本信息。
```sql
CREATE TABLE exchange (
    id INT AUTO_INCREMENT PRIMARY KEY,
    abbrev VARCHAR(32) NOT NULL,
    name VARCHAR(255) NOT NULL,
    city VARCHAR(255),
    country VARCHAR(255),
    currency VARCHAR(64),
    timezone_offset TIME,
    created_date DATETIME,
    last_updated_date DATETIME
);
```

**2. 数据提供商表**
记录数据来源。
```sql
CREATE TABLE data_vendor (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(64) NOT NULL,
    website_url VARCHAR(255),
    support_email VARCHAR(255),
    created_date DATETIME,
    last_updated_date DATETIME
);
```

**3. 股票代码表**
存储所有需要关注的股票符号。
```sql
CREATE TABLE symbol (
    id INT AUTO_INCREMENT PRIMARY KEY,
    exchange_id INT,
    ticker VARCHAR(32) NOT NULL,
    instrument VARCHAR(64) NOT NULL,
    name VARCHAR(255),
    sector VARCHAR(255),
    currency VARCHAR(32),
    created_date DATETIME,
    last_updated_date DATETIME,
    FOREIGN KEY (exchange_id) REFERENCES exchange(id)
);
```

**4. 每日价格表**
存储股票每日的交易数据，这是最核心的表。
```sql
CREATE TABLE daily_price (
    id INT AUTO_INCREMENT PRIMARY KEY,
    data_vendor_id INT,
    symbol_id INT,
    price_date DATE NOT NULL,
    created_date DATETIME,
    last_updated_date DATETIME,
    open_price DECIMAL(19,4),
    high_price DECIMAL(19,4),
    low_price DECIMAL(19,4),
    close_price DECIMAL(19,4),
    adj_close_price DECIMAL(19,4),
    volume BIGINT,
    FOREIGN KEY (data_vendor_id) REFERENCES data_vendor(id),
    FOREIGN KEY (symbol_id) REFERENCES symbol(id)
);
```

建议你根据选定的数据源（如Yahoo Finance或Tushare）来微调这些表结构，使其更贴合实际数据格式。

---

## 实战：爬取并存储S&P 500数据

接下来，我们通过一个具体任务来串联所有步骤：获取标准普尔500指数（S&P 500）的成分股列表及其历史价格，并存入数据库。

这个任务分为几步：
1.  从维基百科页面爬取S&P 500的成分股代码列表。
2.  将这些股票代码存入数据库的 `symbol` 表。
3.  遍历股票代码列表，从Yahoo Finance获取每只股票的历史价格数据。
4.  将价格数据存入数据库的 `daily_price` 表。

### 第一步：从维基百科爬取股票代码

我们使用 `BeautifulSoup` 库来解析网页并提取数据。

![](img/954070abbf42c1312b9cc130722e6421_19.png)

```python
import requests
from bs4 import BeautifulSoup

![](img/954070abbf42c1312b9cc130722e6421_21.png)

def obtain_parse_wiki_snp500():
    """从维基百科获取S&P 500股票代码列表"""
    url = "https://en.wikipedia.org/wiki/List_of_S%26P_500_companies"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')

    # 在页面中找到包含股票代码的表格
    symbols_list = []
    table = soup.find('table', {'class': 'wikitable sortable'})
    # 跳过表头，遍历每一行
    for row in table.findAll('tr')[1:]:
        symbol = row.findAll('td')[0].text.strip()
        symbols_list.append(symbol)
    return symbols_list
```

### 第二步：将股票代码插入数据库

使用 `MySQL Connector` 库连接数据库并执行插入操作。

```python
import mysql.connector

def insert_snp500_symbols(symbols):
    """将股票代码列表插入数据库"""
    db_config = {
        'user': 'quant_user',
        'password': 'your_password',
        'host': 'localhost',
        'database': 'quant_db'
    }
    conn = mysql.connector.connect(**db_config)
    cursor = conn.cursor()

    # 假设我们已经有了一个data_vendor记录，其id为1（例如Yahoo Finance）
    vendor_id = 1
    exchange_id = 1  # 假设NYSE的id为1

    for symbol in symbols:
        cursor.execute("""
            INSERT INTO symbol (exchange_id, ticker, instrument, name, created_date)
            VALUES (%s, %s, %s, %s, NOW())
        """, (exchange_id, symbol, 'stock', symbol))
    conn.commit()
    cursor.close()
    conn.close()
```

### 第三步：从Yahoo Finance获取历史价格

虽然可以使用 `pandas-datareader` 库更简单地完成，但为了理解原理，我们先展示如何通过 `requests` 直接获取。

```python
import pandas as pd
from io import StringIO

def get_daily_data_from_yahoo(symbol, start_date, end_date):
    """从Yahoo Finance获取单只股票的历史数据（示例方法）"""
    # 注意：Yahoo Finance的旧版API已不稳定，此代码仅为示例逻辑。
    # 实际应用中建议使用 `yfinance` 库或 `pandas-datareader`。
    base_url = "https://query1.finance.yahoo.com/v7/finance/download/"
    url = f"{base_url}{symbol}?period1={start_ts}&period2={end_ts}&interval=1d&events=history"
    response = requests.get(url)
    data = pd.read_csv(StringIO(response.text))
    return data
```

**更简单的方法（推荐）**：
使用 `pandas-datareader` 库，它封装了与多个数据源的交互。

```python
import pandas_datareader.data as web
import datetime

def get_data_pandas(symbol, start, end):
    """使用pandas-datareader获取数据"""
    try:
        df = web.DataReader(symbol, 'yahoo', start, end)
        return df
    except Exception as e:
        print(f"Error fetching data for {symbol}: {e}")
        return None

# 示例：获取苹果公司(APPL)从2020年到2022年的数据
start = datetime.datetime(2020, 1, 1)
end = datetime.datetime(2022, 12, 31)
df_aapl = get_data_pandas('AAPL', start, end)
print(df_aapl.head())
```

### 第四步：将价格数据插入数据库并验证

获取数据后，编写函数将其插入 `daily_price` 表。最后，编写一个主函数连接数据库，查询并打印出部分数据，以验证整个流程的正确性。

```python
def main():
    # 1. 获取股票代码列表
    symbols = obtain_parse_wiki_snp500()[:10]  # 先取前10只测试
    # 2. 插入股票代码
    insert_snp500_symbols(symbols)
    print("Symbols inserted.")

    # 3. & 4. 获取并插入历史价格（此处省略循环插入的详细代码）
    # ...
    # 5. 验证：从数据库读取并打印一只股票的数据
    conn = mysql.connector.connect(user='quant_user', password='your_password',
                                   host='localhost', database='quant_db')
    query = "SELECT price_date, close_price FROM daily_price WHERE symbol_id = 1 LIMIT 5;"
    df_from_db = pd.read_sql(query, conn)
    print(df_from_db)
    conn.close()

![](img/954070abbf42c1312b9cc130722e6421_23.png)

if __name__ == "__main__":
    main()
```

---

## 数据质量注意事项

在处理金融数据时，需要特别注意以下几点，它们容易导致错误：

1.  **企业行为调整**：如分红、拆股等。高质量的数据源会提供调整后的价格（`adj_close_price`）。如果使用未调整的数据，需要自己应用调整公式。
2.  **节假日处理**：市场休市日没有数据。在构建连续时间序列时，需要剔除这些日期，避免出现异常的空值或零值。
3.  **噪声数据**：数据中可能包含突然的暴涨或暴跌（毛刺）。在建模前，可能需要使用滤波器平滑数据。
4.  **缺失值处理**：对于缺失的数据点，常见的处理方法有：向前/向后填充、使用均值/中位数填充、或直接删除。具体方法取决于你的模型和策略。

Pandas库提供了丰富的功能来处理上述问题，例如 `fillna()`, `dropna()`, `resample()` 等。

---

## 本节课总结

在本节课中，我们一起学习了量化交易中数据层的基础建设：

1.  **明确了目的**：理解了建立量化数据库是为了服务于建模、回测和交易执行。
2.  **学会了获取**：介绍了Tushare、Yahoo Finance等公开数据源及其获取方式。
3.  **掌握了存储**：学习了CSV、MySQL等不同的数据存储方式，并详细讲解了如何设计MySQL表结构来存储金融时间序列数据。
4.  **完成了实战**：通过一个完整的例子，演练了从网页爬取股票列表、从API获取历史价格、存储到数据库以及最终读取验证的全过程。
5.  **注意到了细节**：了解了金融数据清洗中需要特别注意的环节，如调整、节假日和缺失值处理。

数据是量化交易的基石。搭建一个稳定、高效的数据管道，是后续所有复杂分析和策略开发的先决条件。请务必动手实践课程中的代码，并尝试完成作业，将知识转化为实际技能。