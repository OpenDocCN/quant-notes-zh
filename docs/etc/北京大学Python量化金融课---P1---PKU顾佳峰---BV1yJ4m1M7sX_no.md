# 北京大学Python量化金融课：第13讲：使用Tushare获取实时金融数据 📈

在本节课中，我们将学习如何使用Python库Tushare来获取实时的股票历史数据。这是进行量化金融分析的第一步，为后续的技术分析、策略回测和预测奠定数据基础。

![](img/ebd56c3fe80cd0c493b1b6cd113746dd_1.png)

## 概述与准备工作

上一节我们介绍了量化金融的基本概念。本节中，我们来看看如何获取核心的分析材料——金融数据。我们将使用一个名为Tushare的免费、全面的金融数据接口。

首先，需要完成以下准备工作：

1.  **安装必要的库**：确保已安装`pandas`、`numpy`和`tushare`库。若未安装Tushare，可通过以下命令安装：
    ```bash
    pip install tushare
    ```
2.  **注册Tushare账号并获取Token**：访问Tushare官网注册，获取一个唯一的接口令牌（Token）。这个Token是调用数据接口的凭证。

完成准备工作后，我们就可以开始编写代码获取数据了。

## 获取单只股票的历史数据

以下是获取单只股票（以上证指数为例）历史日线数据的完整步骤和代码。

首先，导入必要的库并设置你的Tushare Token。
```python
import pandas as pd
import numpy as np
import tushare as ts

# 设置你的Tushare Token，将‘your_token_here’替换为你自己的Token
ts.set_token('your_token_here')
pro = ts.pro_api()
```

接着，调用接口获取数据。以下代码获取股票代码为`000001.SH`（上证指数）从2020年1月1日到2024年6月14日的日线行情数据。
```python
# 获取数据
df = pro.daily(ts_code='000001.SH',
                start_date='20200101',
                end_date='20240614',
                fields='ts_code, trade_date, open, high, low, close, vol')
```
获取到的数据默认是按日期倒序排列的（最新的日期在前）。为了方便后续分析，我们通常将其按日期正序排列。
```python
# 将数据按交易日期正序排列
df = df.sort_values(by='trade_date')
```

最后，将处理好的数据保存到本地CSV文件，并读取查看。
```python
# 保存到CSV文件，不保存行索引
df.to_csv('shanghai_index.csv', index=False)

# 从CSV文件读取数据并打印前几行查看
df_read = pd.read_csv('shanghai_index.csv')
print(df_read.head())
```
执行上述代码后，你将在本地得到一个名为`shanghai_index.csv`的文件，其中包含了所需时间段内的股票开盘价、最高价、最低价、收盘价和成交量等数据。

## 批量获取多只股票的数据

掌握了获取单只股票数据的方法后，我们可以轻松地将其扩展到多只股票。以下是获取深圳成指（`399001.SZ`）和另一只示例股票（`000981.SZ`）数据的方法。

其核心逻辑与获取单只股票完全相同，只需改变股票代码参数。我们可以通过循环或分别调用来实现。
```python
# 定义股票代码列表
stock_codes = ['399001.SZ', '000981.SZ']
# 定义起止日期
start = '20200101'
end = '20240614'

for code in stock_codes:
    df_temp = pro.daily(ts_code=code, start_date=start, end_date=end)
    df_temp = df_temp.sort_values(by='trade_date')
    # 以股票代码命名文件，例如‘399001_SZ.csv’
    filename = f"{code.replace('.', '_')}.csv"
    df_temp.to_csv(filename, index=False)
    print(f"已保存 {code} 的数据到 {filename}")
```
运行后，你的本地目录将新增两个CSV数据文件。这样，你就拥有了多只股票的历史数据，可以用于对比分析或构建投资组合。

## 数据格式与保存选项

我们主要将数据保存为CSV格式，这是一种通用且易于读取的格式。实际上，Pandas库支持将数据保存为多种格式。

例如，你也可以轻松地将数据保存为Excel格式：
```python
df.to_excel('shanghai_index.xlsx', index=False)
```
选择CSV还是Excel取决于你的个人习惯和后续处理工具的需求。CSV文件更轻量，兼容性极广；Excel文件则便于直接手动查看和做一些简单操作。

## 总结

本节课中我们一起学习了量化金融数据分析的第一步：获取历史数据。

1.  我们介绍了**Tushare**这个强大且免费的金融数据源。
2.  我们逐步演示了如何使用Tushare的`pro.daily()`接口，通过指定股票代码和日期范围来**获取日线行情数据**。
3.  我们讲解了如何对获取的数据进行**排序**，并将其**保存**到本地CSV或Excel文件中，以便后续分析使用。

![](img/ebd56c3fe80cd0c493b1b6cd113746dd_3.png)

拥有准确、完整的历史数据是进行任何量化分析、策略回测和模型预测的基石。现在，你已经掌握了搭建这个基石的关键技能。在接下来的课程中，我们将利用这些数据展开进一步的分析。

---
**附**：北京大学将于8月2日至4日举办Python量化金融课程，感兴趣的同学可以关注相关报名信息。

![](img/ebd56c3fe80cd0c493b1b6cd113746dd_1.png)
*（示意图：数据获取流程）*

![](img/ebd56c3fe80cd0c493b1b6cd113746dd_5.png)

![](img/ebd56c3fe80cd0c493b1b6cd113746dd_3.png)
*（示意图：本地数据文件）*

![](img/ebd56c3fe80cd0c493b1b6cd113746dd_5.png)
*（示意图：Tushare简介）*