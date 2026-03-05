# 北京大学Python量化金融16讲：P1：批量下载股票数据 📈

在本节课中，我们将学习如何使用Python批量下载股票的历史交易数据。这是进行量化金融研究的基础步骤，能够帮助我们高效地获取大量数据，为后续的分析和建模做准备。

![](img/c7702e4ce09d139c76f3a28172e1aaa2_1.png)

## 课程概述

量化金融研究的一个关键基础是从相关平台下载股票的历史交易数据。例如，我们可以从网易财经的个股行情中心下载数据。本节课将演示如何下载单只股票数据，并进一步扩展到批量下载多只股票数据。

## 下载单只股票数据

![](img/c7702e4ce09d139c76f3a28172e1aaa2_3.png)

上一节我们介绍了课程目标，本节中我们来看看如何下载单只股票的数据。首先，我们需要安装必要的库，并使用代码从网易财经获取数据。

以下是下载单只股票数据的关键步骤和代码：

1.  **安装必要库**：确保已安装 `requests` 和 `pandas` 库。如果未安装，系统会提示你安装。
    ```python
    # 通常使用 pip 安装
    # pip install requests pandas
    ```

2.  **构建下载函数**：定义一个函数，通过指定股票代码和日期范围来下载数据。
    ```python
    import requests
    import pandas as pd

    def download_stock_data(stock_code, start_date, end_date):
        # 网易财经数据接口示例（接口地址可能变化，需根据实际情况调整）
        url = f'http://quotes.money.163.com/service/chddata.html?code={stock_code}&start={start_date}&end={end_date}'
        response = requests.get(url)
        # 将数据保存到本地文件
        with open(f'{stock_code}.csv', 'wb') as f:
            f.write(response.content)
        print(f"股票 {stock_code} 数据下载完毕")
    ```

3.  **执行下载**：调用函数下载指定股票在特定时间段的数据。
    ```python
    # 示例：下载浦发银行(600000)在2021年12月的数据
    download_stock_data('0600000', '20211201', '20211231')
    ```

## 绘制K线图

数据下载后，我们可以使用 `mplfinance` 库来绘制经典的K线图，直观展示股票的开盘价、收盘价、最高价和最低价。

以下是绘制K线图的步骤：

1.  **安装库**：安装 `mplfinance` 库。
    ```python
    # pip install mplfinance
    ```

2.  **读取数据并绘图**：使用 `pandas` 读取下载的CSV文件，并用 `mplfinance` 绘图。
    ```python
    import mplfinance as mpf
    import pandas as pd

    # 读取数据，注意设置索引和解析日期
    df = pd.read_csv('600000.csv', index_col=0, parse_dates=True)
    # 绘制K线图
    mpf.plot(df, type='candle', style='charles', title='浦发银行 K线图', volume=True)
    ```
    在绘制大量数据时，Python可能会提示图形细节不够清晰。此时，可以考虑将时间段划分得更细（例如按年或按月），以便更清楚地观察走势。

## 批量下载多只股票数据

手动逐一下载股票数据效率低下，尤其当需要处理成百上千只股票时。因此，我们需要实现批量自动下载。

以下是实现批量下载的方法：

1.  **准备股票代码列表**：创建一个包含所有目标股票代码的列表。
    ```python
    stock_list = ['0600000', '1600009', '0600398', ...] # 此处应填入实际的股票代码列表
    ```

2.  **循环调用下载函数**：遍历股票代码列表，为每只股票调用之前定义的下载函数。
    ```python
    start_date = '20210101'
    end_date = '20211231'

    for code in stock_list:
        download_stock_data(code, start_date, end_date)
    ```
    运行此代码后，程序将自动按顺序下载列表中的所有股票数据，并在控制台输出如“下载完毕 600009”、“下载完毕 600398”等提示。每只股票的数据都会保存为一个独立的CSV文件。

## 课程总结

本节课中我们一起学习了量化金融数据获取的基础操作。我们首先介绍了如何下载单只股票的历史交易数据并绘制K线图进行可视化。接着，我们重点讲解了如何通过编写循环，实现股票数据的批量自动下载，这极大地提升了数据收集的效率，是进行大规模量化分析的必要技能。

![](img/c7702e4ce09d139c76f3a28172e1aaa2_5.png)

---

**提示**：本课程提及的北京大学量化金融研修班相关信息，可通过关注相关微信公众号获取具体详情。