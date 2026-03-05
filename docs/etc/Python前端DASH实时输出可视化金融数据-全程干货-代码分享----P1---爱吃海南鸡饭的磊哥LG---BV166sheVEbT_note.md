# Python Dash 金融数据实时可视化教程：P1：项目概述与核心代码解析

![](img/88071c34fc0d6119a0c06c3c75f14b11_0.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_2.png)

在本节课中，我们将学习如何使用 Python 的 Dash 框架构建一个实时更新的金融数据可视化仪表盘。我们将解析一个实战项目，该项目能够实时展示沪深300指数的K线图、交易量、技术指标（RSI、CCI）以及通过机器学习计算的支撑/阻力位。

## 概述

Dash 是一个基于 Flask 的 Python 前端框架，它允许我们使用纯 Python 代码构建交互式、美观的数据可视化 Web 应用。本教程将拆解一个完整的实时金融数据看板项目，涵盖数据获取、指标计算、机器学习应用和可视化渲染等核心环节。

![](img/88071c34fc0d6119a0c06c3c75f14b11_4.png)

## 环境配置与核心库介绍

上一节我们介绍了项目的整体目标，本节中我们来看看实现它所需的环境和核心库。

![](img/88071c34fc0d6119a0c06c3c75f14b11_6.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_8.png)

首先，代码文件的第一行必须声明编码格式，以确保中文字符能正常处理。
```python
# -*- coding: utf-8 -*-
```

![](img/88071c34fc0d6119a0c06c3c75f14b11_10.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_12.png)

以下是项目依赖的核心 Python 库及其作用：

![](img/88071c34fc0d6119a0c06c3c75f14b11_14.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_16.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_17.png)

*   **`dash`, `dash_core_components`, `dash_html_components`**: Dash 框架的核心组件，用于构建Web应用布局和交互元素。
*   **`dash_bootstrap_components`**: 提供 Bootstrap 风格的UI组件，本项目用于设置 `cyborg` 黑色主题。
*   **`plotly.graph_objects`**: 强大的绘图库，用于生成K线图、柱状图等交互式图表。
*   **`plotly.subplots`**: 用于创建包含多个子图的复合图表。
*   **`pandas`**: 数据处理和分析的核心库。
*   **`pandas_ta`**: 一个便捷的技术分析指标库，用于计算RSI、CCI等。
*   **`qstock`**: 一个优秀的国内金融数据获取库，用于获取实时行情和资金流数据。
*   **`scipy.signal`, `sklearn.neighbors`**: 用于机器学习算法，计算支撑位和阻力位。
*   **自定义库 (`maozhuli`)**: 将常用参数和函数封装成个人库，提高代码复用性和整洁度。

![](img/88071c34fc0d6119a0c06c3c75f14b11_19.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_21.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_23.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_25.png)

**环境建议**：推荐使用 Python 3.10.15 或 3.11.10 等稳定版本。避免使用 3.12.x 等早期 bug-fix 版本。

## 应用初始化与布局设计

![](img/88071c34fc0d6119a0c06c3c75f14b11_27.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_28.png)

了解了核心库之后，我们开始构建Dash应用的主体结构。

![](img/88071c34fc0d6119a0c06c3c75f14b11_30.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_32.png)

应用初始化并设置主题的代码如下：
```python
app = dash.Dash(__name__, external_stylesheets=[dbc.themes.CYBORG])
```

![](img/88071c34fc0d6119a0c06c3c75f14b11_34.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_36.png)

应用布局 (`app.layout`) 定义了网页的结构。以下是核心组件：

*   **`dcc.Interval`**: 这是实现实时更新的关键组件。它按照设定的时间间隔（如 `interval=5*1000` 毫秒）自动触发回调函数，更新图表。
*   **`dcc.Graph`**: 用于展示 Plotly 图表的容器，其 `id` 为 `‘live-chart’`。
*   **`dcc.Dropdown`**: 下拉选择菜单，用于选择不同的股票代码 (`ticker`) 和K线周期 (`fr`)，实现交互式切换。

布局代码结构大致如下：
```python
app.layout = html.Div([
    html.H2(“金融数据实时看板”),
    dcc.Interval(id=‘interval-component’, interval=5*1000, n_intervals=0),
    dcc.Dropdown(id=‘ticker-selector’, options=..., value=‘沪深300’),
    dcc.Dropdown(id=‘freq-selector’, options=..., value=‘10min’),
    dcc.Graph(id=‘live-chart’)
])
```

## 数据获取与处理函数

现在界面框架已经搭好，我们需要获取数据来填充它。本节介绍几个关键的数据处理函数。

![](img/88071c34fc0d6119a0c06c3c75f14b11_38.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_40.png)

1.  **`get_data(ticker, freq)` 函数**:
    *   **功能**：从数据源（如讯投API）获取指定代码和周期的历史K线数据。
    *   **流程**：请求数据 -> 将返回的字典格式数据转换为Pandas DataFrame -> 规范化时间列格式 -> 返回处理好的DataFrame。

![](img/88071c34fc0d6119a0c06c3c75f14b11_42.png)

2.  **`get_realtime_data(ticker)` 函数**:
    *   **功能**：使用 `qstock` 库获取股票的实时报价数据。
    *   **公式/逻辑**：`实时价格 = getattr(qstock.real_tick(ticker), ‘price’)`

![](img/88071c34fc0d6119a0c06c3c75f14b11_44.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_46.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_48.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_50.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_51.png)

3.  **`get_money_flow(ticker)` 函数**:
    *   **功能**：使用 `qstock` 库获取实时资金流数据，包括主力净流入、超大单净流入等。
    *   **信号生成**：定义一个简单的资金流信号。例如，当 `主力净流入 + 超大单净流入 > 0` 时，标记为 `‘+’`（看多信号），否则标记为 `‘-’`。

## 核心回调与图表生成函数

![](img/88071c34fc0d6119a0c06c3c75f14b11_53.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_55.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_57.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_59.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_61.png)

数据准备就绪后，最重要的部分是将它们动态地绘制成图表。这是通过Dash的回调函数 `update_figure` 实现的。

![](img/88071c34fc0d6119a0c06c3c75f14b11_63.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_65.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_67.png)

回调函数装饰器将页面交互与数据更新绑定：
```python
@app.callback(
    Output(‘live-chart’, ‘figure’),
    [Input(‘interval-component’, ‘n_intervals’),
     Input(‘ticker-selector’, ‘value’),
     Input(‘freq-selector’, ‘value’)]
)
def update_figure(n, selected_ticker, selected_freq):
    # 1. 获取数据
    df = get_data(selected_ticker, selected_freq)
    real_price = get_realtime_data(selected_ticker)
    mf = get_money_flow(selected_ticker)

    # 2. 创建包含子图的画布
    fig = make_subplots(rows=3, cols=1, ...)

    # 3. 添加各个子图的轨迹 (Trace)
    # 3.1 第一行：K线图
    fig.add_trace(go.Candlestick(x=df[‘datetime’], open=df[‘open’], ...), row=1, col=1)
    # 添加实时价格标注
    fig.add_annotation(x=df[‘datetime’].iloc[-1], y=real_price, text=f’ATM: {real_price}’, ...)
    # 添加资金流信号标注（颜色根据 +/- 变化）
    color = ‘tomato’ if mf_signal == ‘+’ else ‘seagreen’
    fig.add_annotation(..., text=f’Flow: {mf_signal}’, font_color=color)

    # 3.2 第二行：交易量柱状图（使用自动色阶增强视觉）
    fig.add_trace(go.Bar(x=df[‘datetime’], y=df[‘volume’],
                         marker=dict(autocolorscale=True)), row=2, col=1)

    # 3.3 第三行：技术指标区
    # 计算并绘制调整后的RSI（-50 到 50，柱状显示）
    df[‘rsi_adj’] = ta.rsi(df[‘close’], length=14) - 50
    fig.add_trace(go.Bar(x=df[‘datetime’], y=df[‘rsi_adj’], name=‘RSI(14)’), row=3, col=1)
    # 计算并绘制CCI（使用右侧Y轴）
    df[‘cci_25’] = ta.cci(df[‘high’], df[‘low’], df[‘close’], length=25)
    fig.add_trace(go.Scatter(..., yaxis=‘y2’), row=3, col=1)

    # 4. 添加机器学习计算的支撑/阻力位（在第一行K线图上）
    support_lines, resistance_lines = calculate_support_resistance(df[‘close’])
    for line in support_lines:
        fig.add_hline(y=line, line_dash=“dash”, line_color=“blue”, row=1, col=1)

    # 5. 全局布局更新
    fig.update_layout(height=800, ...)
    # 处理A股非连续交易时间导致的图表空白
    fig.update_xaxes(type=“category”)

    return fig
```

![](img/88071c34fc0d6119a0c06c3c75f14b11_69.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_71.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_73.png)

![](img/88071c34fc0d6119a0c06c3c75f14b11_75.png)

**关键技巧**：
*   **双Y轴对齐**：为了使RSI（范围-50~50）和CCI（范围-200~200）在同一个子图中比例协调，需要对Y轴范围进行对称化 (`range=[-max_val, max_val]`) 和比例调整。
*   **数据表格注释**：将资金流等数据DataFrame转置后，以字符串形式分行显示为图表注释，提升可读性。

![](img/88071c34fc0d6119a0c06c3c75f14b11_76.png)

## 总结

![](img/88071c34fc0d6119a0c06c3c75f14b11_78.png)

本节课中我们一起学习了如何使用 Python Dash 框架搭建一个专业的金融数据实时可视化看板。我们从环境配置和库依赖讲起，逐步剖析了应用的布局设计、数据获取函数、以及最核心的图表生成与回调逻辑。这个项目融合了实时数据获取、技术指标分析、简单的资金流信号以及机器学习算法，提供了一个完整的实战范例。你可以在此基础上，修改数据源、增加新的指标或优化视觉样式，以适应个人的分析需求。