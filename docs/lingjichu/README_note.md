# 零基础 Python 量化交易入门笔记：1：环境搭建与基础概念

在本节课中，我们将要学习如何搭建 Python 量化交易的基础环境，并理解量化交易的核心概念。这是开启量化之旅的第一步。

## 概述

量化交易是利用数学模型和计算机程序来制定和执行交易策略的方法。它通过分析历史数据来寻找盈利机会，并自动执行交易。本节将指导你完成环境的准备工作。

## 安装 Python 与必要库

要进行量化分析，首先需要安装 Python 和一些核心库。以下是安装步骤。

1.  从 Python 官网下载并安装最新版本的 Python。
2.  打开命令行工具（如终端或命令提示符）。
3.  使用 pip 安装以下库：
    *   `numpy`: 用于数值计算。
    *   `pandas`: 用于数据处理和分析。
    *   `matplotlib`: 用于绘制图表。
    *   `yfinance`: 用于获取金融数据。

安装命令示例：
```bash
pip install numpy pandas matplotlib yfinance
```

## 理解核心概念：数据与回报率

上一节我们介绍了环境搭建，本节中我们来看看量化交易的两个基础概念：金融数据和回报率。

金融数据通常以时间序列的形式存在，包含开盘价、收盘价、最高价、最低价和成交量。我们可以使用 `pandas` 库的 `DataFrame` 来处理这种表格数据。

计算回报率是量化分析的基础。简单回报率（Simple Return）和对数回报率（Log Return）是两种常用计算方式。

*   **简单回报率** 的公式为：
    **R_t = (P_t - P_{t-1}) / P_{t-1}**
    其中 `P_t` 代表当前时期的价格，`P_{t-1}` 代表上一时期的价格。
*   **对数回报率** 的公式为：
    **r_t = ln(P_t / P_{t-1})**
    对数回报率在数学处理上更方便，特别是在进行多期回报率相加时。

以下是使用 Python 计算简单回报率的示例代码：
```python
import pandas as pd
# 假设 ‘price‘ 是一个包含价格数据的 pandas Series
returns = price.pct_change() # 计算百分比变化，即简单回报率
```

## 获取并查看股票数据

理解了基础概念后，我们需要获取真实数据来进行分析。我们将使用 `yfinance` 库来获取股票历史数据。

以下是获取苹果公司（AAPL）股票数据的步骤：
```python
import yfinance as yf
# 下载苹果公司从2023年1月1日至今的历史数据
data = yf.download(‘AAPL‘, start=‘2023-01-01‘)
# 查看前几行数据
print(data.head())
```
这段代码会下载并显示一个包含日期、开盘价、最高价、最低价、收盘价和成交量的表格。

## 绘制价格走势图

数据获取完成后，直观地查看价格走势非常重要。我们可以使用 `matplotlib` 库将数据可视化。

以下是绘制收盘价走势图的代码：
```python
import matplotlib.pyplot as plt
# 假设 ‘data‘ 是包含股票数据的 DataFrame
data[‘Close‘].plot(figsize=(10, 6), title=‘AAPL Close Price‘)
plt.xlabel(‘Date‘)
plt.ylabel(‘Price (USD)‘)
plt.grid(True)
plt.show()
```
这张图可以帮助我们初步了解股票的价格变动趋势。

## 总结

本节课中我们一起学习了量化交易的起步知识。我们完成了 Python 环境的搭建，安装了必要的库（`numpy`, `pandas`, `matplotlib`, `yfinance`）。我们理解了金融数据的基本结构和回报率的计算方法。最后，我们实践了如何获取真实的股票数据并将其价格走势可视化。这些是构建更复杂量化策略的基石。在接下来的课程中，我们将基于这些基础，开始探索具体的交易策略。