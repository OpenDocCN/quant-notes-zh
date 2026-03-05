# 松哥量化正式课程：1-2：解密近30年美股总是上涨的部分原因 - P1

![](img/98fc40211e7745c2f15b7c00771805e4_0.png)

![](img/98fc40211e7745c2f15b7c00771805e4_2.png)

![](img/98fc40211e7745c2f15b7c00771805e4_4.png)

![](img/98fc40211e7745c2f15b7c00771805e4_0.png)

在本节课中，我们将通过一个量化项目，探究美股市场长期上涨背后的一个关键制度性原因——严格的退市机制。我们将学习如何利用Python和雅虎财经数据，统计美股历史上的退市比例，并理解其对市场健康发展的意义。

## 项目背景与思路

上一节我们讨论了A股市场的退市问题。近期监管加强，出现了许多公司主动撤回IPO或受到处罚的情况，这是一个积极的信号。然而，要短期改变市场生态仍比较困难。

有粉丝咨询，为何美股市场似乎总能长期上涨，即使在回调时买入，事后看也常常是正确的决定。这背后，制度保障是一个重要原因。本节课，我们就来量化验证这一观点：美股市场拥有高比例的退市公司。

网上数据显示，美股退市比例可能在40%到50%之间，远高于A股市场（估计不超过10%）。我们想通过自己的方法验证这个数据。

![](img/98fc40211e7745c2f15b7c00771805e4_6.png)

## 数据获取与处理思路

![](img/98fc40211e7745c2f15b7c00771805e4_8.png)

我们的核心思路是：通过对比历史股票代码列表与当前能否获取到数据，来估算退市比例。

具体方法是：获取一份历史某时刻（如2017年9月）在雅虎财经上所有可查询的股票代码列表。然后，尝试用这些代码获取最新的股价数据。如果能成功获取，则认为该股票仍在上市交易；如果获取失败（返回错误或没有数据），则将其标记为“可能已退市”。

**需要注意**：此方法存在误差。有些公司可能只是更改了股票代码（Ticker），对我们而言就像“换了马甲”，也会被误判为退市。但考虑到这种情形比例不会太高，不影响我们对整体趋势的判断。

以下是实现此思路的核心步骤概述。

## 核心步骤详解

### 1. 加载历史股票代码列表

首先，我们从一份外部文件（`yahoo_ticker_symbols_september2017.csv`）中加载截至2017年9月雅虎财经上所有的股票代码数据。

```python
import pandas as pd

# 读取历史股票代码数据
data1 = pd.read_csv('yahoo_ticker_symbols_september2017.csv')
print(data1.info())  # 查看数据概览，约有数万至十万条记录
```

这份数据包含全球多个市场的股票，字段包括股票代码（Ticker）、名称、交易所、类别和国家等。

### 2. 筛选美国市场股票

我们只关注美国市场的股票。通过检查数据，我们发现“Country”列标识了国家信息。我们筛选出该列为“USA”的数据。

```python
# 假设‘Country’列在DataFrame中，我们筛选美国股票
usa_stocks = data1[data1['Country'] == 'USA']
# 确认筛选结果
print(usa_stocks['Country'].unique())  # 应只输出['USA']
```

这样，我们就得到了一个只包含美国股票代码的DataFrame，记为`pre_two`或`psi`。

### 3. 定义数据获取与退市判断函数

接下来，我们定义一个函数，尝试获取指定股票代码的历史数据，并根据结果判断其状态。

```python
import yfinance as yf
from datetime import datetime

def check_stock_status(ticker, start_date='1990-01-01', end_date='2024-02-12'):
    """
    检查股票状态。
    参数:
        ticker: 股票代码
        start_date, end_date: 查询日期范围
    返回:
        status: 'active' 或 'delisted'
        data: 成功获取的数据或None
    """
    try:
        # 尝试下载数据
        stock_data = yf.download(ticker, start=start_date, end=end_date, progress=False)
        if not stock_data.empty:
            return 'active', stock_data
        else:
            # 数据为空，可能已退市
            return 'delisted', None
    except Exception as e:
        # 出现异常，如代码不存在等，视为退市
        return 'delisted', None

# 初始化列表，用于存放可能已退市的代码和仍活跃的代码数据
delisted_tickers = []
active_data = {}

# 示例：检查苹果公司(AAPL)和某个不存在的代码
ticker_to_check = 'AAPL'
status, data = check_stock_status(ticker_to_check)
if status == 'active':
    active_data[ticker_to_check] = data
    print(f"{ticker_to_check}: 活跃")
else:
    delisted_tickers.append(ticker_to_check)
    print(f"{ticker_to_check}: 可能已退市")
```

在实际批量处理时，我们会遍历所有筛选出的美国股票代码，调用此函数，并将结果分别存入`delisted_tickers`列表和`active_data`字典。

### 4. 批量处理与统计

遍历所有股票代码进行查询。这是一个耗时的过程，可能需要一个小时或更久。

```python
# 获取所有美国股票代码列表
all_usa_tickers = usa_stocks['Ticker'].tolist()
total_count = len(all_usa_tickers)
print(f"待处理的美国股票代码总数: {total_count}")

# 批量检查（此处为逻辑示意，实际运行需考虑网络请求频率和耗时）
for ticker in all_usa_tickers:
    status, data = check_stock_status(ticker)
    if status == 'delisted':
        delisted_tickers.append(ticker)
    else:
        active_data[ticker] = data
    # 可添加进度提示

# 统计结果
delisted_count = len(delisted_tickers)
active_count = len(active_data)
print(f"可能已退市的数量: {delisted_count}")
print(f"仍活跃的数量: {active_count}")
print(f"退市比例: {delisted_count/total_count:.2%}")
```

### 5. 结果分析与解读

运行上述代码后，我们得到了统计结果。以课程中的示例数据为例：

*   历史股票代码总数（Tickers）：**22,196**
*   可能已退市数量（Delisted）：**11,752**
*   仍活跃数量（Active）：**10,417** （此数字包含因更名而用新代码交易的公司）
*   估算退市比例：**11,752 / 22,196 ≈ 53%**

这个粗略估算的53%的退市比例，与公开报道的数据趋势相符（例如，有报道称美国市场38年间退市约17,000家公司）。相比之下，A股市场同期的退市数量仅为前者的一个零头。

## 结论与启示

本节课我们一起学习了如何通过量化方法，估算美股市场的历史退市比例，并验证了其高退市率的特征。

高退市率是美股市场长期健康上涨的重要制度原因之一。它形成了一个“优胜劣汰”的良性循环：不符合要求的公司不断被清出市场，留存下来的公司整体质量更高，从而吸引了长期资金，支撑指数上行。这也解释了为何在美股市场，长期持有或逢低买入指数基金往往是有效的策略。

反观A股，过去退市制度执行不严，导致市场存量公司质量参差不齐，影响了整体表现。近期监管层正在加强退市力度，这是一个向好的关键变化。健康的股市不仅需要融资功能，更需要严格的淘汰机制。制度和执行，二者缺一不可。

通过本项目，我们不仅掌握了一个具体的量化分析技能，更重要的是理解了数据背后反映的市场运行逻辑。在后续课程中，我们还可以探索更多维度，例如将年度退市数量与股市年涨跌幅进行相关性分析，进一步深化理解。

---

![](img/98fc40211e7745c2f15b7c00771805e4_10.png)

![](img/98fc40211e7745c2f15b7c00771805e4_10.png)