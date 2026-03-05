# 基于Python的股票分析与量化交易入门到实践 - P35：9.3 股市投资第一步-Python基本面量化选股_量化选股--财务因子选股

在本节课中，我们将要学习财务因子选股中的利润因子。利润因子是衡量公司盈利能力的重要指标，对于量化选股至关重要。我们将详细介绍五种常见的利润因子指标，并通过Python代码演示如何获取和分析这些数据。

## 📈 利润因子概述

上一节我们介绍了财务因子中的成长类因子，特别是营收因子。本节中，我们来看看成长类因子中的另一个核心部分——利润因子。利润因子直接反映公司的盈利能力和经营效率，是投资者进行基本面分析时必须关注的重点。

以下是本节将要介绍的五个利润因子指标：
1.  净利润同比增长率
2.  净利润环比增长率
3.  营业利润率
4.  销售净利润
5.  销售毛利润

接下来，我们将逐一解析这些指标的含义、计算公式及代码实现。

## 1. 净利润同比增长率

在介绍净利润同比增长率之前，我们先明确什么是净利润。净利润是企业的税后利润，即企业的销售额减去所有成本和税费后的剩余部分。计算公式为：
**净利润 = 销售额 - 成本 - 税费**

净利润同比增长率衡量的是公司当期净利润相对于上一年同期净利润的增长情况。其计算公式为：
**净利润同比增长率 = (当期净利润 - 上期净利润) / |上期净利润| × 100%**
这里使用上期净利润的绝对值作为分母，是为了避免当上期净利润为负值时计算结果失真。

在代码实现中，我们主要使用 `get_fundamentals` 函数，对应的字段为 `inc_net_profit_year_on_year`。

```python
# 示例：获取净利润同比增长率数据
query(valuation).filter(valuation.inc_net_profit_year_on_year > 300)
```

## 2. 净利润环比增长率

净利润环比增长率衡量的是公司本期净利润相对于上一期（如上月比上月）净利润的增长情况。其计算公式与同比类似：
**净利润环比增长率 = (本期净利润 - 上期净利润) / 上期净利润 × 100%**

代码实现同样使用 `get_fundamentals` 函数，对应的字段为 `inc_net_profit_annual`。

```python
# 示例：获取净利润环比增长率数据
query(valuation).filter(valuation.inc_net_profit_annual > 500)
```

## 3. 营业利润率

介绍完净利润的增长情况，我们来看一个衡量经营效率的指标——营业利润率。营业利润率是指企业经营所得的营业利润占其全部业务收入的百分比，反映了企业通过主营业务获取利润的能力。计算公式为：
**营业利润率 = 营业利润 / 全部业务收入 × 100%**

在代码中，该指标对应的字段为 `operating_profit_to_total_revenue`。

```python
# 示例：获取营业利润率数据
query(valuation).filter(valuation.operating_profit_to_total_revenue > 200)
```

## 4. 销售净利润

与营业利润率相关的一个指标是销售净利润。这个指标衡量的是企业在一定时期内，销售收入的获利能力。其计算公式为：
**销售净利润 = 净利润 / 销售收入 × 100%**
需要注意的是，该指标与净利润成正比，与销售收入成反比。这意味着销售收入增长时，若净利润未同步增长，该指标反而会下降，它体现了企业规模与效益之间的平衡关系。

代码实现中，该指标对应的字段为 `net_profit_margin`。

```python
# 示例：获取销售净利润数据
query(valuation).order_by(valuation.net_profit_margin.desc()).limit(50)
```

## 5. 销售毛利润

![](img/761ad919679de047e4a01544992523d5_1.png)

最后一个指标是销售毛利润。首先要区分净利润和毛利润：毛利润是销售净收入与产品成本的差额，尚未扣除税费；而净利润是扣税后的利润。销售毛利率（即销售毛利润）的计算公式为：
**销售毛利率 = (销售净收入 - 产品成本) / 销售净收入 × 100%**

它与销售净利润的主要区别在于是否包含税费。在代码中，该指标对应的字段为 `gross_profit_margin`。

```python
# 示例：获取销售毛利润数据
query(valuation).order_by(valuation.gross_profit_margin.desc()).limit(5)
```

## 💻 代码实战环节

以上我们介绍了五个利润因子的理论部分，下面进入实战环节，演示如何使用Python获取这些数据。

首先，我们获取净利润同比增长率大于300%的股票。

```python
# 获取净利润同比增长率数据
df_profit_yoy = query(valuation).filter(valuation.inc_net_profit_year_on_year > 300)
print(df_profit_yoy)
```

接下来，我们获取净利润环比增长率大于500%的股票，并限制输出5条结果。

```python
# 获取净利润环比增长率数据
df_profit_qoq = query(valuation).filter(valuation.inc_net_profit_annual > 500).limit(5)
print(df_profit_qoq)
```

![](img/761ad919679de047e4a01544992523d5_3.png)

然后，我们查询营业利润率大于200%的股票。

```python
# 获取营业利润率数据
df_operating_margin = query(valuation).filter(valuation.operating_profit_to_total_revenue > 200)
print(df_operating_margin)
```

接着，我们查看销售净利润最高的50只股票。

```python
# 获取销售净利润数据
df_net_profit_margin = query(valuation).order_by(valuation.net_profit_margin.desc()).limit(50)
print(df_net_profit_margin)
```

最后，我们找出销售毛利润最高的5只股票。

```python
# 获取销售毛利润数据
df_gross_profit_margin = query(valuation).order_by(valuation.gross_profit_margin.desc()).limit(5)
print(df_gross_profit_margin)
```

## 📝 本章总结

本节课中我们一起学习了利润因子相关的五个核心指标。
我们首先介绍了**净利润同比增长率**和**净利润环比增长率**，明确了同比（与上年同期比）和环比（与上一期比）的区别。
接着，我们讲解了**营业利润率**，即营业利润占全部业务收入的比重。
然后，我们分析了**销售净利润**，这个指标揭示了净利润与销售收入的关系，反映了企业在特定销售收入下的获利能力。
最后，我们探讨了**销售毛利润**，明确了其与销售净利润的关键差异在于是否扣除税费。
所有这些指标的代码实现都主要依赖于 `get_fundamentals` 函数，通过指定不同的字段来获取相应数据。

![](img/761ad919679de047e4a01544992523d5_5.png)

理解并运用这些利润因子，能帮助我们更有效地从财务角度筛选出具有盈利潜力的股票，为量化投资决策提供坚实的数据基础。