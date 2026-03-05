# Python量化交易入门：1：60%胜率的短线信号代码详解

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_1.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_3.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_5.png)

在本节课中，我们将学习如何通过Python编程，分析北向资金净流入数据，并计算其对A股市场未来表现的预测效果。我们将从获取数据开始，逐步讲解数据处理、计算逻辑，最终生成一份统计报告。整个过程将模拟手工操作Excel的步骤，但使用代码实现自动化。

## 1. 问题定义与数据准备

上一节我们介绍了课程的目标。本节中，我们来看看我们要解决的具体问题以及需要准备哪些数据。

我们的目标是分析一个事件：**北向资金单日净流入超过特定金额**。我们想统计历史上该事件发生后，沪深300指数在未来1天、3天、5天的上涨概率和平均涨跌幅。

为了解决这个问题，我们需要两类数据：
1.  **北向资金历史数据**：包含沪股通和深股通每日的“成交净买额”。
2.  **沪深300指数历史数据**：包含每日的收盘价。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_7.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_9.png)

这些数据可以从香港交易所官网或主流财经网站（如东方财富网）获取。为简化教程，我们假设已获得整理好的CSV格式文件。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_11.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_13.png)

## 2. 编程思路：从Excel操作到代码

在开始编写代码之前，我们先通过Excel手动操作来理清思路。理解了这个过程，编程就会水到渠成。

以下是使用Excel进行分析的步骤：

1.  **新建表格**：创建一个新的Excel文件。
2.  **导入沪深300数据**：将沪深300历史数据中的“日期”和“收盘价”两列复制到新表格的A列和B列。
3.  **导入北向资金数据**：分别将沪股通和深股通数据中的“当日成交净买额”列，按日期对应复制到新表格的C列和D列。
4.  **计算总净流入**：在E列计算 `C列 + D列`，得到北向资金单日总净流入。注意原始数据单位通常是“百万”，可在此处除以100转换为“亿”。
5.  **计算未来涨跌幅**：
    *   在F列计算次日涨跌幅：`公式 = (次日收盘价 - 当日收盘价) / 当日收盘价`
    *   在G列计算未来3日涨跌幅：`公式 = (3日后收盘价 - 当日收盘价) / 当日收盘价`
    *   在H列计算未来5日涨跌幅：`公式 = (5日后收盘价 - 当日收盘价) / 当日收盘价`
6.  **数据筛选与统计**：
    *   对“总净流入”列进行筛选，例如筛选“>10”。
    *   统计筛选后的行数，即为“事件发生次数”。
    *   在筛选状态下，对“次日涨跌幅”列筛选“>0”，统计行数即为“上涨次数”。
    *   在筛选状态下，计算“次日涨跌幅”列的平均值，即为“平均涨跌幅”。
7.  **制作结果表**：将不同阈值（0亿, 10亿, 20亿...）下的统计结果整理到一张新表中。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_15.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_17.png)

接下来，我们将用Python代码自动化以上所有步骤。

## 3. Python代码逐行详解

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_19.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_21.png)

上一节我们通过Excel演示了分析流程。本节中我们来看看如何用Python代码实现相同的功能。我们将使用 `pandas` 库，它是处理表格数据的利器。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_23.png)

首先，我们需要导入必要的库并进行基础设置。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_25.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_27.png)

```python
import pandas as pd
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)
```

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_29.png)

### 3.1 读取数据

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_31.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_33.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_35.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_37.png)

我们的第一步是将CSV文件中的数据读取到Python中。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_39.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_41.png)

```python
# 读取沪深300指数数据
hs300 = pd.read_csv('hs300_data.csv', encoding='gbk', parse_dates=['trade_date'], index_col='trade_date')
```
*   `pd.read_csv()`: 读取CSV文件的函数。
*   `'hs300_data.csv'`: 要读取的文件名。
*   `encoding='gbk'`: 指定中文编码，防止乱码。
*   `parse_dates=['trade_date']`: 将‘trade_date’这一列解析为日期格式。
*   `index_col='trade_date'`: 将‘trade_date’列设置为数据表的索引（行标签）。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_43.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_45.png)

```python
# 筛选出2014年11月17日（沪港通开通日）之后的数据
start_date = '2014-11-17'
hs300 = hs300[hs300.index >= start_date]
```
这行代码筛选出行索引（日期）大于等于起始日期的数据，模拟了在Excel中删除早期数据的操作。

```python
# 读取沪股通和深股通数据
hgt = pd.read_csv('hu_gutong.csv', encoding='gbk', skiprows=1, parse_dates=['交易日期'], index_col='交易日期')
sgt = pd.read_csv('shen_gutong.csv', encoding='gbk', skiprows=1, parse_dates=['交易日期'], index_col='交易日期')
```
*   `skiprows=1`: 跳过文件的第一行（可能是数据说明），因为我们需要的是纯数据。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_47.png)

### 3.2 数据合并与处理

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_49.png)

现在，我们要像在Excel中新建表格一样，创建一个包含所有所需数据的新表格。

```python
# 创建一个新的DataFrame，初始只包含沪深300的收盘价
df = hs300[['close']].copy()
```
我们创建了一个新的表格 `df`，它目前只包含沪深300的收盘价。

```python
# 将沪股通和深股通的净买入额数据合并到新表格中
df['净买额_H'] = hgt['当日成交净买额']
df['净买额_S'] = sgt['当日成交净买额']
```
我们将两个通的数据作为新列添加到 `df` 中。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_51.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_53.png)

```python
# 计算北向资金总净流入（单位：百万），并转换为“亿”
df['净流入'] = (df[['净买额_H', '净买额_S']].sum(axis=1)) / 100
```
*   `df[['净买额_H', '净买额_S']].sum(axis=1)`: 将两列对应行相加。`axis=1` 表示按行操作。
*   `/ 100`: 将单位从“百万”转换为“亿”。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_55.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_57.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_59.png)

```python
# 删除中间列
del df['净买额_H'], df['净买额_S']
```
数据合并完成后，可以删除不再需要的中间列。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_61.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_63.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_65.png)

### 3.3 计算未来涨跌幅

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_67.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_69.png)

接下来，我们计算未来N天的涨跌幅。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_71.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_73.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_75.png)

```python
# 计算未来1、3、5日的涨跌幅
df['未来1日涨跌幅'] = df['close'].shift(-1) / df['close'] - 1
df['未来3日涨跌幅'] = df['close'].shift(-3) / df['close'] - 1
df['未来5日涨跌幅'] = df['close'].shift(-5) / df['close'] - 1
```
*   `df['close'].shift(-1)`: `shift(-1)` 函数将整列数据向上移动一行。对于某一天，`shift(-1)`就代表“下一天”的收盘价。
*   公式 `(未来价格 / 当前价格) - 1` 即计算涨跌幅。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_77.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_79.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_81.png)

```python
# 剔除未来没有涨跌幅数据的日期（例如最后几天）
end_date = '2021-05-31'
df = df[df.index <= end_date]
```

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_83.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_85.png)

### 3.4 统计与生成结果表

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_87.png)

这是核心部分，我们将根据不同的净流入阈值进行统计。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_89.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_91.png)

```python
# 创建一个空的表格来存放最终结果
result = pd.DataFrame()

# 定义要统计的净流入阈值列表
threshold_list = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]

# 循环处理每一个阈值
for flow in threshold_list:
    # 筛选出净流入大于当前阈值的数据
    temp_df = df[df['净流入'] > flow]

    # 计算事件发生次数
    event_count = temp_df.shape[0]  # shape[0] 获取行数

    # 初始化一个字典来存储这一行的结果
    row_result = {'出现次数': event_count}

    # 循环计算未来1、3、5日的指标
    for day in [1, 3, 5]:
        col_name = f'未来{day}日涨跌幅'
        # 计算上涨次数
        up_count = temp_df[temp_df[col_name] > 0].shape[0]
        # 计算平均涨跌幅
        avg_return = temp_df[col_name].mean()

        row_result[f'未来{day}日上涨次数'] = up_count
        row_result[f'未来{day}日平均涨跌幅'] = avg_return
        # 计算上涨概率
        if event_count > 0:
            row_result[f'未来{day}日上涨概率'] = up_count / event_count
        else:
            row_result[f'未来{day}日上涨概率'] = None

    # 将这一行的结果添加到最终结果表中
    # 使用 .loc 通过索引（这里用阈值flow）来添加一行数据
    result.loc[flow, :] = row_result

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_93.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_95.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_97.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_99.png)

# 对结果表进行排序，让阈值从小到大排列
result = result.sort_index()
```

### 3.5 输出结果

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_101.png)

最后，我们将统计好的结果表格保存到本地文件。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_103.png)

```python
# 将结果输出为CSV文件
result.to_csv('北向资金分析结果.csv', encoding='gbk')
```
*   `to_csv()`: 将DataFrame写入CSV文件。
*   `encoding='gbk'`: 同样指定中文编码。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_105.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_107.png)

运行全部代码后，你将在程序目录下得到一个名为 `北向资金分析结果.csv` 的文件，里面就是自动生成的统计分析表。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_109.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_111.png)

## 总结

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_113.png)

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_115.png)

本节课中我们一起学习了如何用一个完整的Python量化分析案例。我们从**定义问题**（分析北向资金净流入事件的影响）出发，讲解了**数据准备**（获取沪深300和北向资金数据），并通过**Excel模拟**理清了分析思路。最后，我们**逐行详解了Python代码**，实现了数据读取、合并、计算涨跌幅、按条件筛选统计以及结果输出全流程自动化。

![](img/ebc0bd045d4d318f5fabdbcd07c819d6_117.png)

这个案例的核心在于将手工分析步骤转化为代码逻辑。你学到的不仅是几行Python语法，更是一种用程序化思维解决金融数据分析问题的方法。如果你能理解并重现这个过程，说明你已经具备了量化编程的良好开端。