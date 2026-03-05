# 量化回测指标评价神器Quantstats：P1：入门与核心功能 🚀

在本节课中，我们将学习一个名为Quantstats的Python库。它是一个功能强大的量化分析工具，能够帮助我们快速计算和可视化各种回测指标，从而极大地简化量化策略的评价工作。

## 安装与导入 📦

上一节我们介绍了Quantstats库的用途，本节中我们来看看如何安装和导入它。

安装Quantstats非常简单，只需使用pip命令即可。如果下载速度较慢，可以使用国内的镜像源来加速。

以下是安装命令：
```bash
pip install quantstats
```

![](img/1805648ae496c1e43d51090140bf9181_1.png)

![](img/1805648ae496c1e43d51090140bf9181_3.png)

安装完成后，在Python代码中导入该库，通常使用`qs`作为别名。
```python
import quantstats as qs
```

## 获取收益率数据 📈

安装并导入库之后，下一步是准备数据。Quantstats特别方便的一点是，对于美股数据，它支持一键下载。

我们可以使用`qs.utils.download_returns`函数来下载股票的收益率数据。需要注意的是，该函数返回的数据格式是Pandas的`Series`，而不是NumPy数组或DataFrame。如果传入错误的数据格式，程序会报错。

![](img/1805648ae496c1e43d51090140bf9181_5.png)

以下是获取苹果公司股票收益率数据的示例代码：
```python
# 下载苹果公司股票的收益率数据，数据格式为Pandas Series
aapl_returns = qs.utils.download_returns(‘AAPL’)
```

![](img/1805648ae496c1e43d51090140bf9181_7.png)

## 计算核心指标 🔢

![](img/1805648ae496c1e43d51090140bf9181_9.png)

有了收益率数据，我们就可以开始计算各种量化指标了。Quantstats内置了非常全面的指标计算函数，无需我们自己编写复杂的代码。

![](img/1805648ae496c1e43d51090140bf9181_11.png)

例如，要计算夏普比率，只需调用`qs.stats.sharpe`函数。该库包含了几乎所有常用的回测指标，如最大回撤、年化收益率、索提诺比率等。

![](img/1805648ae496c1e43d51090140bf9181_13.png)

以下是一个计算夏普比率的示例：
```python
# 计算苹果公司股票收益率的夏普比率
sharpe_ratio = qs.stats.sharpe(aapl_returns)
print(f"夏普比率: {sharpe_ratio}")
```

## 可视化分析 📊

除了数值计算，Quantstats还提供了强大的可视化功能，能够帮助我们直观地理解策略表现。

![](img/1805648ae496c1e43d51090140bf9181_15.png)

我们可以使用`qs.plots.snapshot`函数生成一个包含累计收益率、每日收益率和回撤情况的可视化图表。图中蓝色线代表累计收益率，红色线代表回撤，黄色线代表每日收益率。

![](img/1805648ae496c1e43d51090140bf9181_17.png)

以下是生成可视化图表的代码：
```python
# 生成苹果公司股票的可视化分析图表
qs.plots.snapshot(aapl_returns, title=‘Apple Performance’)
```

## 生成完整策略报告 📑

![](img/1805648ae496c1e43d51090140bf9181_19.png)

对于向投资者或团队展示策略结果，一份清晰、专业的报告至关重要。Quantstats可以一键生成包含所有关键指标的完整HTML报告。

![](img/1805648ae496c1e43d51090140bf9181_21.png)

使用`qs.reports.html`函数，我们可以将策略的收益率序列与一个基准（如标普500指数）进行比较，生成详细的绩效分析报告。报告内容包括起止时间、年化收益率、最大回撤、夏普比率等，并以图表形式展示策略与基准的收益对比曲线。

![](img/1805648ae496c1e43d51090140bf9181_23.png)

以下是生成策略报告的示例代码：
```python
# 下载基准（如SPY指数）的收益率数据
benchmark_returns = qs.utils.download_returns(‘SPY’)

![](img/1805648ae496c1e43d51090140bf9181_25.png)

![](img/1805648ae496c1e43d51090140bf9181_27.png)

# 生成策略（苹果股票）相对于基准的完整HTML报告
qs.reports.html(aapl_returns, benchmark_returns, output=‘./aapl_strategy_report.html’)
```

![](img/1805648ae496c1e43d51090140bf9181_29.png)

## 总结 ✨

![](img/1805648ae496c1e43d51090140bf9181_31.png)

本节课中我们一起学习了Quantstats库的核心功能。我们从安装和导入开始，学习了如何获取收益率数据、计算关键量化指标、进行可视化分析，以及最终生成一份专业的策略报告。

![](img/1805648ae496c1e43d51090140bf9181_33.png)

Quantstats极大地简化了量化回测的评价流程，将原本需要数百行代码才能完成的工作，浓缩为几个简单的函数调用。无论是用于策略开发、学术研究还是向客户展示，它都是一个非常高效和实用的工具。