# Python金融量化投资与股票交易：P68：回测框架主体实现

在本节课中，我们将实现回测框架的核心主体部分，即 `run` 函数。我们将学习如何模拟每日交易、计算每日资产价值与收益率，并最终生成用于对比分析的收益曲线图。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_1.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_3.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_5.png)

---

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_7.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_9.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_11.png)

## 回测框架 `run` 函数实现

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_13.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_15.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_17.png)

上一节我们完成了回测框架的基础结构搭建。本节中，我们将实现核心的 `run` 函数，其目的是模拟整个回测周期内的每日交易，并计算两条关键曲线：策略自身的收益曲线和基准（如某只股票或指数）的收益曲线。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_19.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_21.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_23.png)

首先，我们需要创建一个 `DataFrame` 来记录每个交易日的资产价值或收益率。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_25.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_27.png)

```python
plt_df = pd.DataFrame(index=pd.to_datetime(context.date_range), columns=['value'])
```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_29.png)

这里，`context.date_range` 是一个日期数组，我们使用 `pd.to_datetime` 将其转换为 `DatetimeIndex` 作为 `DataFrame` 的索引。`value` 列将用于存储每日的资产总价值。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_31.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_33.png)

接下来，我们需要模拟每个交易日执行的操作。以下是模拟每日循环的步骤：

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_35.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_37.png)

以下是每日循环中需要执行的关键步骤：

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_39.png)

1.  **更新当前日期**：将循环中的日期字符串转换为时间对象，并更新到 `context.dt` 中。
    ```python
    context.dt = dt_util.parse(dt)
    ```

2.  **调用用户初始化函数**：在循环开始前，调用用户定义的 `initialize` 函数，传入 `context` 对象进行策略初始化。
    ```python
    initialize(context)
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_41.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_43.png)

3.  **执行每日交易逻辑**：在每日循环中，调用用户定义的 `handle_data` 函数，让用户根据当天的数据执行买卖下单等操作。
    ```python
    handle_data(context)
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_45.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_47.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_48.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_50.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_51.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_53.png)

4.  **计算当日资产价值**：在用户执行完当日操作后，计算当日的总资产价值。总价值由现金和持有的股票市值两部分组成。
    ```python
    value = context.cash
    for stock in context.positions:
        today_data = get_today_data(stock)
        if len(today_data) == 0:
            # 股票停牌，使用最近的有效价格
            p = last_price.get(stock, 0)
        else:
            p = today_data['open']
            last_price[stock] = p # 更新最近价格
        value += p * context.positions[stock]
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_55.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_57.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_59.png)

5.  **记录价值并计算收益率**：将计算出的当日总价值 `value` 记录到 `plt_df` 中。循环结束后，根据初始资金计算每日的收益率。
    ```python
    plt_df.loc[dt, 'value'] = value
    # 循环结束后计算收益率
    init_value = context.init_cash
    plt_df['ratio'] = (plt_df['value'] - init_value) / init_value
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_61.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_63.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_65.png)

## 基准收益率计算

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_67.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_69.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_71.png)

为了进行策略对比，我们还需要计算基准的收益率。假设基准是一只股票。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_73.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_75.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_77.png)

以下是设置和计算基准收益率的步骤：

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_79.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_81.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_83.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_85.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_86.png)

1.  **设置基准**：提供一个 `set_benchmark` 函数，允许用户在 `initialize` 中设置基准股票代码。
    ```python
    def set_benchmark(context, security):
        context.benchmark = security
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_88.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_90.png)

2.  **获取基准历史数据**：在 `run` 函数中，获取基准股票在整个回测周期内的历史开盘价数据。
    ```python
    bm_df = attribute_history(context.benchmark, context.start_date, context.end_date, fields=['open'])
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_92.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_94.png)

3.  **计算基准收益率**：以第一天的开盘价为初始价值，计算每日相对于该初始价值的收益率，并添加到 `plt_df` 中。
    ```python
    bm_init = bm_df['open'].iloc[0]
    plt_df['benchmark_ratio'] = (bm_df['open'] - bm_init) / bm_init
    ```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_96.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_98.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_100.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_102.png)

## 结果可视化

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_104.png)

最后，我们将策略收益率和基准收益率绘制在同一张图上进行对比。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_106.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_108.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_110.png)

```python
plt_df[['ratio', 'benchmark_ratio']].plot()
plt.show()
```

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_112.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_114.png)

至此，回测框架的 `run` 函数主体已实现完成。接下来，我们可以像真正的用户一样，编写 `initialize` 和 `handle_data` 函数来测试框架的运行效果，并查看生成的收益对比图。

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_116.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_118.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_120.png)

---

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_122.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_124.png)

![](img/bc0037efb8b3c1d0e426ed06fb33cffd_126.png)

本节课中我们一起学习了如何构建回测框架的核心 `run` 函数。我们实现了模拟每日交易、计算资产价值与收益率、集成基准对比以及结果可视化等功能。这是将策略逻辑转化为可评估结果的关键一步。