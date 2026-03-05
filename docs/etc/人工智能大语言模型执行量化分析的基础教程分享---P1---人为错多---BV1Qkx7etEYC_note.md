# 量化分析入门：P1：股票财务数据获取与本地存储 📊

在本节课中，我们将学习如何获取单只股票的财务报表数据，并将其存储到本地数据库中，为后续的数据分析打下基础。整个过程将分为数据获取与数据存储两个核心部分。

## 数据获取：使用JQData查询财务数据

上一节我们介绍了课程目标，本节中我们来看看如何从量化平台获取数据。我们将使用JQData量化平台来查询股票的财务数据。

JQData平台提供了详细的数据字典，其中包含了各类财务数据表的名称和字段说明。例如，资产负债表对应的表名为 `balance`，现金流量表为 `cash_flow`。我们可以根据这些信息查询具体的数据。

以下是获取股票“600782”在特定日期（2020年9月15日）的“负债和所有者权益合计”数据的示例代码：

```python
# 导入JQData
from jqdatasdk import *
# 认证（需替换为你的账号信息）
auth('你的用户名', '你的密码')

# 查询资产负债表数据
df = get_fundamentals(query(
        valuation
    ).filter(
        valuation.code == '600782.XSHG', # 股票代码
        valuation.day == '2020-09-15'    # 查询日期
    ), statDate='2020-09-15')

# 打印“负债和所有者权益合计”字段的值
print(df['total_liability_and_owners_equity'])
```

执行上述代码后，会输出一个数值，例如 `52370.51`。这表示我们成功查询到了该股票在指定日期的权益合计数据。

![](img/c9ee80fc840e1bc94a020fbe46147576_1.png)

通过这种方式，我们可以获取任意股票在任意报告期（如季度、年度）的各类财务数据，包括利润、资产、营业收入等。

![](img/c9ee80fc840e1bc94a020fbe46147576_3.png)

## 数据存储：将数据保存至本地数据库

获取到数据后，我们需要将其存储起来以便后续分析。一种常见的方式是将数据存入本地的MySQL数据库。

![](img/c9ee80fc840e1bc94a020fbe46147576_5.png)

首先，你需要在电脑上安装MySQL数据库并创建一个数据库（例如 `stock_db`）和对应的数据表（例如 `stock_finance`）。表的字段设计应与你要存储的财务数据字段相匹配。

以下是使用Python连接MySQL数据库并插入数据的简化步骤：

1.  **连接数据库**：使用 `pymysql` 或 `sqlalchemy` 库建立与MySQL数据库的连接。
2.  **准备数据**：将JQData查询到的 `DataFrame` 数据转换为适合插入的格式。
3.  **执行插入**：编写SQL `INSERT` 语句或使用ORM框架将数据批量写入数据库表。

一个简单的数据插入示例如下：

```python
import pymysql
import pandas as pd

# 假设 df 是从JQData获取的DataFrame
# 1. 连接数据库
connection = pymysql.connect(host='localhost',
                             user='你的用户名',
                             password='你的密码',
                             database='stock_db')
try:
    with connection.cursor() as cursor:
        # 2. 遍历DataFrame的每一行数据
        for index, row in df.iterrows():
            # 3. 构造SQL插入语句
            sql = "INSERT INTO `stock_finance` (`code`, `date`, `total_equity`) VALUES (%s, %s, %s)"
            # 4. 执行插入
            cursor.execute(sql, (row['code'], row['statDate'], row['total_liability_and_owners_equity']))
    # 提交事务
    connection.commit()
finally:
    connection.close()
```

为了方便，你也可以先将数据导出为CSV文件，然后使用数据库管理工具或编写脚本实现CSV文件的批量导入功能。

将数据存入本地数据库后，你就拥有了一个专属的数据集。接下来，便可以自由地使用各种数据分析工具（如Pandas、Matplotlib）或BI软件进行深入的财务分析和可视化。

## 课程总结 🎯

本节课中我们一起学习了量化分析的基础第一步：**数据获取与存储**。

![](img/c9ee80fc840e1bc94a020fbe46147576_7.png)

我们首先介绍了如何利用 **JQData平台** 查询指定股票的财务报表数据，并通过示例代码演示了查询过程。接着，我们讲解了如何将这些获取到的数据，通过 **Python连接MySQL数据库** 的方式，持久化存储到本地，构建自己的分析数据库。

掌握数据获取与存储是进行任何量化分析的前提。有了稳定、可靠的数据源和存储方案，后续的数据处理、策略研究和回测工作才能顺利展开。在接下来的课程中，我们将基于本地存储的数据，进一步学习如何进行财务指标计算和初步分析。