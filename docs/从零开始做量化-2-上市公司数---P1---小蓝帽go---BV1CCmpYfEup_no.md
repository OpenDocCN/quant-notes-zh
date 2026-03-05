# 从零开始做量化｜2：上市公司数据 - P1

在本节课中，我们将学习如何获取和构建一个基础的上市公司信息数据库。这个数据库包含了所有上市公司的基本资料，是后续量化分析的重要基础。

---

![](img/c6316204eb1b7a054592df0664312707_1.png)

上一节我们搭建了基础数据库，本节中我们来看看如何准备公司信息数据。`company info` 是一个长期数据库，通常不需要频繁更新，每月或每季度更新一次即可。

首先，我们需要获取所有上市公司的股票代码。

以下是获取所有股票代码的 Python 代码示例，它从新浪财经接口获取数据：

```python
# 示例代码：get_all_symbol.py
# 此代码会从新浪财经获取所有A股股票代码
import requests
import pandas as pd

def get_all_symbols():
    url = "http://api.finance.sina.com.cn/stock/api/jsonp.php/CompanyFinanceService.getCompanyFinancePage?page=1&num=10000"
    # 实际代码中需要处理接口返回的JSONP数据
    # ...
    return symbol_list

symbols = get_all_symbols()
print(symbols[:5])  # 例如输出: ['SH600000', 'SH600556', ...]
```

运行上述代码后，我们会得到一个包含所有上市公司代码的列表，例如 `SH600000` 代表浦发银行，`SH600556` 代表天下秀。

---

拿到所有股票代码后，我们就要准备每家上市公司的详细介绍。

我们来到 `get_company_info.py` 这个脚本。它主要从两个数据源获取信息：
1.  Tushare 平台（需要自己的 API Key）。
2.  AKShare 开源库。

这两个数据源可以提供股票的基础数据，例如公司所属行业、上市日期、主营业务等。

![](img/c6316204eb1b7a054592df0664312707_3.png)

以下是获取公司基础信息的代码框架：

![](img/c6316204eb1b7a054592df0664312707_5.png)

![](img/c6316204eb1b7a054592df0664312707_6.png)

```python
# 示例代码：get_company_info.py
import tushare as ts
import akshare as ak

# 使用Tushare (需要配置token)
pro = ts.pro_api('你的Tushare API Token')
df_basic = pro.stock_basic()

# 使用AKShare
df_company = ak.stock_individual_info_em(symbol="SH600000")

# 将获取的数据进行处理和存储
# ...
```

`df_basic` 包含公司的基本字段，如股票代码、名称、上市日期、所属行业等。`df_company` 则可能包含更详细的业务介绍和公司概况。

![](img/c6316204eb1b7a054592df0664312707_8.png)

---

这些获取到的公司介绍文本，在后续步骤中，我们会使用自然语言处理（NLP）的方法进行处理。例如，对其进行分词和词向量嵌入（Embedding），最终与股价交易数据结合，输入到深度学习模型中进行训练。

这一步数据获取和初步处理可能比较耗时。运行完毕后，你将得到一个结构化的上市公司信息数据库，其内容大致如下：

![](img/c6316204eb1b7a054592df0664312707_10.png)

| 股票代码 | 公司简称 | 所属行业 | 主营业务介绍 | ... |
| :--- | :--- | :--- | :--- | :--- |
| SH600000 | 浦发银行 | 银行 | 提供存贷款、信用卡等金融服务... | ... |
| SH600556 | 天下秀 | 互联网媒体 | 新媒体营销服务、红人经济... | ... |

---

![](img/c6316204eb1b7a054592df0664312707_12.png)

本节课中我们一起学习了如何获取全市场股票代码，以及如何从 Tushare 和 AKShare 两个数据源获取上市公司的基本信息和业务介绍。我们构建的这个 `company info` 数据库是量化研究的基础设施，为后续的数据分析和模型训练做好了准备。