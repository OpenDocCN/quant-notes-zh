# Python金融量化实战：P10：04 实战练习：用Python处理股价数据 📈

![](img/12ae45aa4bea3cc8640b507ea0552a73_0.png)

在本节课中，我们将学习如何获取金融数据，并利用Python进行股价数据的实战分析。我们将涵盖从本地文件、网络以及专业金融数据接口（如Tushare和Wind）获取数据的方法，并完成计算股价涨跌幅、移动平均线、波动率以及行业估值分位数分析等核心任务。

![](img/12ae45aa4bea3cc8640b507ea0552a73_2.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_4.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_6.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_8.png)

## 数据获取的三种途径 🔍

![](img/12ae45aa4bea3cc8640b507ea0552a73_10.png)

在Python金融分析中，获取数据主要有三种途径。

### 从本地导入数据 💾

![](img/12ae45aa4bea3cc8640b507ea0552a73_12.png)

这种方法适用于已从国泰安、锐思、万得（Wind）或Tushare等数据库下载并存储在本地电脑的Excel、CSV或TXT文件中的数据。我们可以直接使用Pandas的`pd.read_*`系列函数来读取这些本地数据，作为金融分析的基础。

**示例代码：**
```python
import pandas as pd
data = pd.read_csv(‘your_data.csv’)
```

![](img/12ae45aa4bea3cc8640b507ea0552a73_14.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_16.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_18.png)

### 从Web抓取数据 🌐

![](img/12ae45aa4bea3cc8640b507ea0552a73_20.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_22.png)

Web抓取是指编写Python程序来处理网页内容，并从中提取、整理出用于数据分析的数据。这个操作会用到一些专门的模块。

![](img/12ae45aa4bea3cc8640b507ea0552a73_24.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_26.png)

以下是常用的Web抓取模块：
*   `webbrowser`: 控制浏览器。
*   `requests`: 发送HTTP请求。
*   `BeautifulSoup`: 解析HTML和XML文档。
*   `Selenium`: 自动化浏览器操作，处理动态网页。

我们后续会有专门的爬虫课程来详细讲解如何从网上爬取并组织数据。

### 使用金融数据接口 📊

这是最常用的方法，主要涉及两个金融数据接口包：Tushare和万得（Wind）的Python接口`WindPy`。

上一节我们介绍了数据获取的几种途径，本节中我们来看看如何使用Tushare这个开源接口。

#### Tushare模块简介

Tushare是一个开源的Python财经数据接口包。我们可以通过`pip install tushare`安装，然后像调用Pandas和NumPy一样导入使用。

**示例代码：**
```python
import tushare as ts
# 获取某只股票的历史数据
df = ts.get_hist_data(‘600848’, ktype=‘W’) # 获取周线数据
# 如果不指定ktype，默认获取日线数据
```

Tushare提供了友好的中文官方文档，详细说明了各类数据的获取方法，使用起来非常方便。

了解了通用的数据接口后，我们将重点介绍在国内金融行业使用最广泛的万得（Wind）终端及其Python接口。

#### 使用WindPy提取并保存数据

万得（Wind）为Python提供了名为`WindPy`的友好接口。使用`WindPy`提取数据的步骤可以归纳为三步。

**第一步：使用代码生成器设计提取代码**
万得提供了一个“代码生成器”功能，我们无需记忆复杂的API调用格式。只需在Wind终端中输入`cg`打开代码生成器，选择Python语言，并通过界面选择所需的数据、股票和日期，即可自动生成提取代码。

![](img/12ae45aa4bea3cc8640b507ea0552a73_28.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_30.png)

**第二步：封装提取函数**
在成功提取一次数据后，我们可以将这个过程封装成一个函数。这样，下次需要提取同类数据时，可以直接调用该函数，提高代码的复用性和效率。

![](img/12ae45aa4bea3cc8640b507ea0552a73_32.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_34.png)

**第三步：批量提取数据**
如果需要批量提取数据（例如，提取多只股票或多个时间点的数据），我们可以设计一个循环，并在循环中调用第二步封装的函数。

接下来，我们通过一个实战例子来演示这个过程：提取A股全部股票的TTM市盈率（PE TTM）。

**1. 启动WindPy并获取股票代码列表**
首先，我们需要连接Wind API并获取全部A股的股票代码列表。

**示例代码：**
```python
from WindPy import w
w.start() # 启动Wind接口

# 使用wset获取数据集，这里获取全部A股的代码和名称
code_data = w.wset(“sectorconstituent”, “date=2023-10-27; sectorid=a001010100000000“)
# 提取股票代码列表，它位于返回数据的data属性的第二个列表中
stock_codes = code_data.Data[1]
```

**2. 提取单日市盈率数据并保存**
获取代码列表后，我们可以使用`w.wss`函数一次性提取所有股票的市盈率数据，并将其保存为DataFrame和Excel文件。

![](img/12ae45aa4bea3cc8640b507ea0552a73_36.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_38.png)

**示例代码：**
```python
# 批量提取市盈率数据
pe_data = w.wss(stock_codes, “pe_ttm”, “tradeDate=2023-10-27; priceAdj=U; cycle=D”)
# 使用use_df参数将结果直接转换为DataFrame
_, df_pe = w.wss(stock_codes, “pe_ttm”, “tradeDate=2023-10-27; priceAdj=U; cycle=D; usedf=True”)
# 保存到Excel
df_pe.to_excel(‘all_a_stock_pe.xlsx’)
```

![](img/12ae45aa4bea3cc8640b507ea0552a73_40.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_42.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_44.png)

**3. 封装为通用函数**
为了使代码更通用，我们将上述步骤封装成一个函数，允许指定板块、日期和指标。

![](img/12ae45aa4bea3cc8640b507ea0552a73_46.png)

**示例代码：**
```python
def extract_wind_data(sector_id, date_str, indicator):
    “””
    从Wind提取指定板块、日期和指标的数据。
    参数:
        sector_id: 板块代码，如 ‘a001010100000000‘ 代表全部A股
        date_str: 日期字符串，格式 ‘YYYY-MM-DD’
        indicator: 指标名称，如 ‘pe_ttm’
    “””
    # 1. 获取板块成分股代码
    code_data = w.wset(“sectorconstituent”, f“date={date_str}; sectorid={sector_id}“)
    codes = code_data.Data[1]
    # 2. 提取指标数据
    _, df = w.wss(codes, indicator, f“tradeDate={date_str}; usedf=True”)
    return df

# 使用函数提取数据
df_pe = extract_wind_data(‘a001010100000000‘, ‘2023-10-27’, ‘pe_ttm’)
```

**4. 批量提取历史数据**
最后，我们可以利用循环和上面封装的函数，批量提取历史数据。

**示例代码：**
```python
import pandas as pd
# 生成一个月度日期序列（每月最后一天）
date_range = pd.date_range(‘2022-01-31’, ‘2023-10-31’, freq=‘M’)
all_data = pd.DataFrame() # 创建一个空的DataFrame用于拼接结果

for single_date in date_range:
    date_str = single_date.strftime(‘%Y-%m-%d’)
    try:
        df_month = extract_wind_data(‘a001010100000000‘, date_str, ‘pe_ttm’)
        df_month[‘Month’] = date_str # 添加月份列
        # 纵向拼接数据
        all_data = pd.concat([all_data, df_month], axis=0, ignore_index=True)
    except Exception as e:
        print(f“提取{date_str}数据时出错: {e}”)
        continue

# 保存批量提取的结果
all_data.to_excel(‘historical_pe_data.xlsx’)
```
**注意**：Wind API通常有调用次数或数据量的限制，在批量提取大量数据时请注意配额，建议先小范围测试。

我们已经掌握了从Wind获取数据的方法，接下来进入实战环节，看看如何用Python处理和分析股价数据。

## 实战项目一：股价数据分析 📉

在这个实战项目中，我们将导入股票（或行业指数）数据，并完成以下分析：
1.  计算每日涨跌幅。
2.  计算股价的移动平均线（例如250日年线）。
3.  计算股价的波动率（标准差）。

![](img/12ae45aa4bea3cc8640b507ea0552a73_48.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_50.png)

### 数据导入与预处理

![](img/12ae45aa4bea3cc8640b507ea0552a73_52.png)

首先，我们使用Pandas导入本地存储的行业指数数据，并将日期列设置为索引。

![](img/12ae45aa4bea3cc8640b507ea0552a73_54.png)

**示例代码：**
```python
import pandas as pd
# 导入数据，跳过前两行（标题和空行）
data = pd.read_excel(‘行业指数.xlsx’, skiprows=2)
# 将‘日期’列设置为索引
data.set_index(‘日期’, inplace=True)
print(data.head())
```

### 计算每日涨跌幅

Pandas提供了`pct_change()`函数，可以方便地计算序列的百分比变化，即涨跌幅。

**示例代码：**
```python
# 计算所有列的日涨跌幅
daily_return = data.pct_change()
# 删除第一行（因为第一行没有前一日数据，结果为NaN）
daily_return = daily_return.dropna()
print(daily_return.head())
```

### 计算移动平均线

我们可以使用`rolling()`函数结合`mean()`来计算移动平均线。例如，计算250日移动平均线（年线）。

**示例代码：**
```python
# 计算‘全指金融’的250日移动平均线
ma_250 = data[‘全指金融’].rolling(window=250).mean()
# 删除NaN值
ma_250 = ma_250.dropna()
print(ma_250.tail())
```

### 计算股价波动率

同样使用`rolling()`函数，但结合`std()`来计算滚动窗口内的标准差，即波动率。

**示例代码：**
```python
# 计算‘全指金融’的250日波动率（滚动标准差）
volatility_250 = data[‘全指金融’].rolling(window=250).std()
volatility_250 = volatility_250.dropna()
print(volatility_250.tail())
# 计算整个历史序列的总体波动率
overall_volatility = data[‘全指金融’].std()
print(f“全指金融历史整体波动率: {overall_volatility}”)
```

完成了基础的股价指标计算，我们来看一个更深入的分析：如何评估行业估值在历史中所处的位置。

## 实战项目二：行业估值分位数分析 🧮

在行业或策略研究中，我们经常需要评估一个指数当前的估值（如市盈率PE）在历史上的位置。这类似于想知道一个人的成绩在公司里的排名百分比。

**目标**：计算各行业指数市盈率（PE TTM）从0%到100%每隔10%的分位数值，并与当前值对比，最终输出一个清晰的表格。

### 方法一：使用`quantile`函数与循环

这是最直接的方法。我们首先对单个指数计算分位数，然后通过循环扩展到所有指数。

**1. 数据处理与单指数分析**

**示例代码：**
```python
# 导入估值数据
pe_data = pd.read_csv(‘行业PETTM.csv’, engine=‘python’, skiprows=1)
pe_data.set_index(‘时间’, inplace=True)
# 为剔除可能存在的无效数据（如0或负值），我们进行筛选
pe_data = pe_data[pe_data > 0]
# 计算单个指数（例如‘全指能源’）的分位数
import numpy as np
quantiles = np.arange(0, 1.1, 0.1) # 生成[0, 0.1, 0.2, …, 1.0]
single_series = pe_data[‘全指能源’].dropna()
quantile_values = single_series.quantile(q=quantiles)
print(quantile_values)
# 查看当前值
current_value = pe_data[‘全指能源’].iloc[0]
print(f“当前PE: {current_value}”)
```

**2. 循环计算所有指数**

![](img/12ae45aa4bea3cc8640b507ea0552a73_56.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_57.png)

**示例代码：**
```python
result_df = pd.DataFrame() # 用于存储最终分位数结果
for industry in pe_data.columns:
    series = pe_data[industry].dropna()
    if not series.empty:
        quantile_series = series.quantile(q=quantiles)
        # 将结果转换为DataFrame并转置，以便拼接
        temp_df = pd.DataFrame(quantile_series).T
        temp_df.index = [industry] # 设置行名为行业名称
        result_df = pd.concat([result_df, temp_df], sort=False)
# 添加当前值行
current_row = pd.DataFrame(pe_data.iloc[0]).T
current_row.index = [‘当前值’]
final_result = pd.concat([current_row, result_df], sort=False)
# 输出到Excel
final_result.to_excel(‘行业PE分位数分析.xlsx’)
```

### 方法二：使用`qcut`函数进行分箱

`pd.qcut`可以根据样本分位数对数据进行自动分箱。我们可以利用它快速查看每个数据点所处的历史分位区间。

**示例代码：**
```python
labels = [‘0%-10%’, ‘10%-20%’, ‘20%-30%’, ‘30%-40%’, ‘40%-50%’,
          ‘50%-60%’, ‘60%-70%’, ‘70%-80%’, ‘80%-90%’, ‘90%-100%’]
# 对单个指数进行分箱
binned_series = pd.qcut(pe_data[‘全指医药’], q=10, labels=labels, duplicates=‘drop’)
print(binned_series.iloc[0]) # 查看最新数据点所在区间
# 同样可以通过循环为所有指数计算并汇总
```

### 方法三：使用`rank`函数计算百分比排名

`rank()`函数可以计算每个数据点在序列中的排名（默认从1开始）。通过排名除以数据总数，即可得到百分比分位数。

**示例代码：**
```python
# 计算百分比排名 (pct=True 直接返回排名百分比)
percentile_rank = pe_data.rank(pct=True, method=‘first’)
# 查看最新日期的各行业估值历史百分位
latest_percentile = percentile_rank.iloc[0]
print(latest_percentile.sort_values())
```

## 总结 🎯

本节课中我们一起学习了：
1.  **数据获取**：掌握了从本地文件、Web抓取以及通过Tushare和WindPy专业接口获取金融数据的三种主要方法，并重点演练了使用WindPy批量提取和保存数据的完整流程。
2.  **股价数据分析**：利用Pandas计算了股票的日涨跌幅、移动平均线（如250日线）和波动率，这些都是技术分析的基础指标。
3.  **估值分位数分析**：通过三种不同的方法（`quantile`循环、`qcut`分箱、`rank`排名）实现了对行业指数历史估值分位数的计算，从而能够科学评估当前估值所处的历史水平，为投资决策提供量化参考。

![](img/12ae45aa4bea3cc8640b507ea0552a73_59.png)

![](img/12ae45aa4bea3cc8640b507ea0552a73_60.png)

这些技能是构建更复杂量化策略和金融分析模型的重要基石。