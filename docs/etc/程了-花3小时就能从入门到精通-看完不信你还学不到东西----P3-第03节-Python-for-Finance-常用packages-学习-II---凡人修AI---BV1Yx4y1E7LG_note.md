# Python金融数据分析：03：常用库与数据处理实战 📊

在本节课中，我们将深入学习Python在金融数据分析中常用的核心库，包括用于统计建模的`statsmodels`、用于数学运算和优化的`SciPy`，以及金融数据的获取与处理方法。我们将通过具体代码示例，掌握回归分析、时间序列建模、数据插值与优化等关键技能。

![](img/fe5b6ec0f2faa85f014aad37daea36de_1.png)

## 第一部分：统计建模与`statsmodels`库 🔍

上一节我们介绍了数据处理的基础，本节中我们来看看如何进行统计建模。`statsmodels`是一个功能强大的Python库，用于拟合多种统计模型、进行统计检验以及数据探索。

![](img/fe5b6ec0f2faa85f014aad37daea36de_3.png)

### 线性回归模型

回归分析是探索变量间关系的基础。在金融领域，我们常用它来研究资产收益率与市场因子之间的关系。

以下是使用`statsmodels`进行普通最小二乘（OLS）回归的步骤：

![](img/fe5b6ec0f2faa85f014aad37daea36de_5.png)

1.  **定义回归函数**：首先，我们自定义一个回归函数，以便灵活应用。
    ```python
    import statsmodels.api as sm
    import pandas as pd

    def regression(data, yvar, xvars):
        Y = data[yvar]
        X = data[xvars]
        X = sm.add_constant(X)  # 添加常数项（截距）
        model = sm.OLS(Y, X)
        results = model.fit()
        return results.params
    ```

![](img/fe5b6ec0f2faa85f014aad37daea36de_7.png)

2.  **应用滚动回归**：金融数据常具有时变性，按月进行滚动回归可以捕捉参数的变化。
    ```python
    # 假设`df`是包含谷歌(GOOG)、苹果(AAPL)和标普500(SPX)收益率的数据框
    monthly_params = df.groupby(pd.Grouper(freq='M')).apply(regression, yvar='GOOG_ret', xvars=['SPX_ret', 'AAPL_ret'])
    print(monthly_params.head())
    ```
    上述代码会输出每个月回归模型的系数（例如，`SPX_ret * 0.96 + AAPL_ret * 0.14 + 截距`）。

3.  **整体回归与结果解读**：对整个时间段的数据进行回归，并查看详细统计结果。
    ```python
    # 对整个数据集进行回归
    Y = df['GOOG_ret']
    X = sm.add_constant(df[['SPX_ret', 'AAPL_ret']])
    model_full = sm.OLS(Y, X).fit()
    print(model_full.summary())
    ```
    在`summary()`中，你需要关注：
    *   **R-squared/Adj. R-squared**：模型解释力。
    *   **F-statistic & p-value**：模型整体显著性。
    *   **系数coef的p-value**：每个预测变量的显著性（通常p<0.05认为显著）。若p值过高（如>0.05），说明该变量与响应变量关系不显著。
    *   **AIC/BIC**：用于模型比较，值越小越好。

![](img/fe5b6ec0f2faa85f014aad37daea36de_9.png)

4.  **进行预测**：使用拟合好的模型对新数据进行预测。
    ```python
    # 假设`new_X`是新的自变量值（需包含常数项）
    predicted_Y = model_full.predict(new_X)
    ```

### 回归模型的扩展与检验

![](img/fe5b6ec0f2faa85f014aad37daea36de_11.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_13.png)

线性回归有多种形式，以适应不同的数据特性：

![](img/fe5b6ec0f2faa85f014aad37daea36de_15.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_17.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_19.png)

*   **OLS (普通最小二乘)**：假设误差项独立同分布，方差恒定（同方差）。公式为 **Y = Xβ + ε**, 其中 **ε ~ N(0, σ²I)**。
*   **WLS (加权最小二乘)**：当误差项存在异方差（方差随时间变化）但无自相关时使用。此时误差协方差矩阵为对角阵。
*   **GLS (广义最小二乘)**：当误差项既存在异方差又存在自相关时使用。误差协方差矩阵为任意正定矩阵。

![](img/fe5b6ec0f2faa85f014aad37daea36de_21.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_23.png)

为了确保模型假设成立，需要进行一系列统计检验。以下是常用的检验方法：

![](img/fe5b6ec0f2faa85f014aad37daea36de_25.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_27.png)

*   **Durbin-Watson检验**：检验残差是否存在自相关性。统计量接近2表明无自相关。
    ```python
    from statsmodels.stats.stattools import durbin_watson
    dw = durbin_watson(model_full.resid)
    print(f'Durbin-Watson statistic: {dw}')
    ```
*   **JB检验 / Omnibus检验**：检验残差是否服从正态分布。
*   **偏度/峰度检验**：正态分布的偏度应为0，峰度应为3。
*   **Breusch-Pagan / White检验**：检验残差是否存在异方差性。
*   **ARCH效应检验**：用于时间序列模型，检验残差方差是否随时间变化（即是否存在ARCH效应），是构建GARCH模型的前提。
*   **Harvey-Collier / Rainbow检验**：检验模型线性假设是否成立。
*   **方差膨胀因子(VIF)**：检验自变量之间是否存在多重共线性。VIF值大于5或10通常认为存在共线性，需要通过变量筛选（如逐步回归）或降维（如PCA）处理。

![](img/fe5b6ec0f2faa85f014aad37daea36de_29.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_31.png)

### 经验分布函数（ECDF）

![](img/fe5b6ec0f2faa85f014aad37daea36de_33.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_35.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_37.png)

经验分布函数用于描述样本数据的累积分布情况。

![](img/fe5b6ec0f2faa85f014aad37daea36de_39.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_41.png)

```python
from statsmodels.distributions.empirical_distribution import ECDF
import numpy as np

sample_data = np.random.randn(100)  # 生成一些样本数据
ecdf = ECDF(sample_data)
# 绘制ECDF图
import matplotlib.pyplot as plt
plt.plot(ecdf.x, ecdf.y)
plt.show()
```

![](img/fe5b6ec0f2faa85f014aad37daea36de_43.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_45.png)

## 第二部分：时间序列处理与建模 ⏳

上一节我们介绍了横截面数据的回归分析，本节中我们来看看时间序列数据。金融数据大多是时间序列，其分析与建模至关重要。

![](img/fe5b6ec0f2faa85f014aad37daea36de_47.png)

### 时间日期处理 (`datetime`)

![](img/fe5b6ec0f2faa85f014aad37daea36de_49.png)

`datetime`模块是处理时间点的核心工具。

![](img/fe5b6ec0f2faa85f014aad37daea36de_51.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_53.png)

```python
from datetime import datetime, timedelta

![](img/fe5b6ec0f2faa85f014aad37daea36de_55.png)

now = datetime.now()  # 当前时间
print(now)

![](img/fe5b6ec0f2faa85f014aad37daea36de_57.png)

past_time = datetime(2023, 1, 1)
time_diff = now - past_time  # 计算时间差
print(time_diff.days)  # 相差天数

![](img/fe5b6ec0f2faa85f014aad37daea36de_59.png)

# 字符串与datetime互转
time_str = now.strftime('%Y-%m-%d %H:%M:%S')
print(time_str)
parsed_time = datetime.strptime('2023-12-25', '%Y-%m-%d')
print(parsed_time)

![](img/fe5b6ec0f2faa85f014aad37daea36de_61.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_63.png)

# 使用pandas批量转换
import pandas as pd
date_series = pd.to_datetime(['2023-01-01', '2023-02-01'])
```

![](img/fe5b6ec0f2faa85f014aad37daea36de_65.png)

### 数据重采样与频率转换

![](img/fe5b6ec0f2faa85f014aad37daea36de_67.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_69.png)

在金融分析中，经常需要转换数据频率（如将周数据转换为日数据）。

![](img/fe5b6ec0f2faa85f014aad37daea36de_71.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_73.png)

```python
# 创建一个周频率的示例数据框
date_rng = pd.date_range(start='2000-01-01', periods=10, freq='W')
df_weekly = pd.DataFrame(date_rng, columns=['date'])
df_weekly['value'] = np.random.randn(len(date_rng))
df_weekly.set_index('date', inplace=True)

![](img/fe5b6ec0f2faa85f014aad37daea36de_75.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_77.png)

# 将周数据重采样为日数据，并用前向填充法填充缺失值
df_daily = df_weekly.resample('D').ffill()
print(df_daily.head())

![](img/fe5b6ec0f2faa85f014aad37daea36de_79.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_81.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_83.png)

# 其他重采样示例
df_quarterly = df_weekly.resample('Q-DEC').mean()  # 按季度重采样，取均值
```

![](img/fe5b6ec0f2faa85f014aad37daea36de_85.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_87.png)

### 时间序列平滑

为了观察价格趋势，常使用移动平均来平滑数据。

*   **简单移动平均 (SMA)**：窗口内各点权重相等。
    ```python
    df['SMA_65'] = df['Close'].rolling(window=65).mean()
    ```
*   **指数移动平均 (EMA)**：赋予近期数据更高权重，对趋势反应更灵敏。公式为：**EMA_t = α * Price_t + (1 - α) * EMA_{t-1}**，其中α为平滑因子。
    ```python
    df['EMA_20'] = df['Close'].ewm(span=20).mean()
    ```
*   **滚动分位数**：用于计算滚动窗口内的风险值（VaR）。
    ```python
    df['Rolling_5th_Percentile'] = df['Close'].rolling(window=65).apply(lambda x: np.percentile(x, 5))
    ```

![](img/fe5b6ec0f2faa85f014aad37daea36de_89.png)

### 时间序列模型 (ARIMA)

平稳性是许多时间序列模型（如ARIMA）的基础假设。平稳序列需满足：均值恒定、方差恒定、自协方差只与时间间隔有关。

**自回归模型 (AR)**：当前值由过去若干期的线性组合加上白噪声构成。例如AR(2)模型：**X_t = φ1 * X_{t-1} + φ2 * X_{t-2} + ε_t**。

```python
from statsmodels.tsa.ar_model import AutoReg
import numpy as np

# 生成一个AR(2)序列
np.random.seed(123)
n = 100
ar_params = [0.8, -0.4]  # φ1, φ2
errors = np.random.normal(0, 1, n)
ar_series = np.zeros(n)
for t in range(2, n):
    ar_series[t] = ar_params[0] * ar_series[t-1] + ar_params[1] * ar_series[t-2] + errors[t]

![](img/fe5b6ec0f2faa85f014aad37daea36de_91.png)

# 拟合AR模型，通过自相关函数(ACF)图初步判断阶数
from statsmodels.graphics.tsaplots import plot_acf
plot_acf(ar_series, lags=20)
plt.show()
# ACF图显示滞后1、2阶显著，之后截尾，提示可能为AR(2)过程。

![](img/fe5b6ec0f2faa85f014aad37daea36de_93.png)

# 拟合AR(2)模型
model_ar = AutoReg(ar_series, lags=2).fit()
print(model_ar.params)  # 查看估计的参数
```

**ARIMA模型**：结合了自回归(AR)、差分(I)和移动平均(MA)。`statsmodels`也提供了`ARIMA`模型接口，更复杂的模型（如考虑波动率聚类的GARCH）将在后续课程深入讲解。

![](img/fe5b6ec0f2faa85f014aad37daea36de_95.png)

**卡尔曼滤波**：一种递归算法，用于从一系列包含噪声的观测数据中估计动态系统的状态。在系统交易中应用广泛，我们将在后续课程结合实例讨论。

![](img/fe5b6ec0f2faa85f014aad37daea36de_97.png)

## 第三部分：科学计算与`SciPy`库 🧮

![](img/fe5b6ec0f2faa85f014aad37daea36de_99.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_101.png)

`SciPy`库建立在NumPy之上，提供了大量数学算法和便利函数，是金融工程中不可或缺的工具。

![](img/fe5b6ec0f2faa85f014aad37daea36de_103.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_105.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_107.png)

### 数值插值

![](img/fe5b6ec0f2faa85f014aad37daea36de_109.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_111.png)

当数据点稀疏时，插值可以帮助我们估计未知点的值。

![](img/fe5b6ec0f2faa85f014aad37daea36de_113.png)

```python
from scipy import interpolate
import numpy as np

![](img/fe5b6ec0f2faa85f014aad37daea36de_115.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_117.png)

# 定义原函数
def f(x):
    return np.sin(x) + 0.5 * x

![](img/fe5b6ec0f2faa85f014aad37daea36de_119.png)

# 生成样本点
x = np.linspace(0, 10, 20)
y = f(x)

# 线性插值
linear_interp = interpolate.interp1d(x, y, kind='linear')
x_new = np.linspace(0, 10, 100)
y_linear = linear_interp(x_new)

# 三次样条插值（更平滑）
cubic_interp = interpolate.interp1d(x, y, kind='cubic')
y_cubic = cubic_interp(x_new)
```

![](img/fe5b6ec0f2faa85f014aad37daea36de_121.png)

### 优化问题

金融中的资产配置、参数校准等问题常归结为优化问题。

![](img/fe5b6ec0f2faa85f014aad37daea36de_123.png)

*   **无约束优化**：寻找函数最小值。
    ```python
    from scipy.optimize import minimize, brute, fmin

    def objective(x):
        return x[0]**2 + x[1]**2 + 2*x[0] + 4*x[1] + 6

    # 暴力搜索（网格法）
    ranges = ((-5, 5), (-5, 5))
    result_brute = brute(objective, ranges, finish=None)
    print(f"Brute force result: {result_brute}")

    # 使用局部优化器（需要初始点）
    initial_guess = [2.0, 2.0]
    result_min = minimize(objective, initial_guess, method='BFGS', options={'maxiter': 250})
    print(f"Minimization result: {result_min.x}")
    ```

*   **约束优化**：在满足一定条件下进行优化。
    ```python
    from scipy.optimize import minimize

    def objective(x):
        return -1 * (x[0] * x[1] + x[2])  # 求最大值等价于求负的最小值

    # 定义约束： 100 - 10*x[0] - 10*x[1] >= 0
    cons = ({'type': 'ineq', 'fun': lambda x: 100 - 10*x[0] - 10*x[1]})
    # 定义变量边界
    bounds = ((0, 1000), (0, 1000), (0, 1000))

    initial_guess = [5, 5, 5]
    result = minimize(objective, initial_guess, method='SLSQP', bounds=bounds, constraints=cons)
    print(f"Constrained optimization result: {result.x}, Objective: {result.fun}")
    ```

![](img/fe5b6ec0f2faa85f014aad37daea36de_125.png)

### 数值积分

用于计算函数在某个区间内的面积（定积分），在期权定价等领域有应用。

![](img/fe5b6ec0f2faa85f014aad37daea36de_127.png)

```python
from scipy import integrate

![](img/fe5b6ec0f2faa85f014aad37daea36de_129.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_131.png)

def integrand(x):
    return np.sin(x) + x**2

a, b = 0, 2  # 积分上下限

result_quad, error = integrate.quad(integrand, a, b)
print(f"Integration result (quad): {result_quad}")

# 其他积分方法
result_fixed = integrate.fixed_quad(integrand, a, b, n=5)
result_romberg = integrate.romberg(integrand, a, b)
```

![](img/fe5b6ec0f2faa85f014aad37daea36de_133.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_135.png)

### 统计分布

![](img/fe5b6ec0f2faa85f014aad37daea36de_137.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_139.png)

`scipy.stats`模块包含大量概率分布。

![](img/fe5b6ec0f2faa85f014aad37daea36de_141.png)

```python
from scipy import stats
import matplotlib.pyplot as plt

# 二项分布
n, p = 10, 0.5
x = np.arange(0, n+1)
pmf = stats.binom.pmf(x, n, p)
plt.bar(x, pmf, label='Binomial PMF')
plt.legend()
plt.show()

![](img/fe5b6ec0f2faa85f014aad37daea36de_143.png)

# 正态分布
mu, sigma = 2.5, 0.5
x_cont = np.linspace(mu - 3*sigma, mu + 3*sigma, 100)
pdf = stats.norm.pdf(x_cont, mu, sigma)
plt.plot(x_cont, pdf, label='Normal PDF')
plt.legend()
plt.show()

![](img/fe5b6ec0f2faa85f014aad37daea36de_145.png)

# 描述性统计
data = np.random.randn(1000)
desc = stats.describe(data)
print(f"Mean: {desc.mean}, Variance: {desc.variance}")
print(f"Skewness: {desc.skewness}, Kurtosis: {desc.kurtosis}")
```

![](img/fe5b6ec0f2faa85f014aad37daea36de_147.png)

## 第四部分：金融数据处理实战 💹

![](img/fe5b6ec0f2faa85f014aad37daea36de_149.png)

前面我们学习了各种分析工具，本节中我们来看看如何系统地获取和处理金融数据。

![](img/fe5b6ec0f2faa85f014aad37daea36de_151.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_153.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_155.png)

### 数据频率与来源

![](img/fe5b6ec0f2faa85f014aad37daea36de_157.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_159.png)

选择数据频率是策略设计的关键，它影响存储、计算复杂度与策略类型。

*   **低频数据（周/月/季度）**：常用于宏观经济数据分析（如CPI、GDP）。获取相对容易。
*   **日频数据（End-of-Day）**：最常见，用于大多数中低频量化策略。可从雅虎财经（`yfinance`）、Quandl等免费或低成本获取。
*   **日内数据（Intraday）**：分钟级、秒级或逐笔数据。用于高频交易，通常需要从彭博（Bloomberg）、路透等专业数据商付费获取，对系统要求高。

![](img/fe5b6ec0f2faa85f014aad37daea36de_161.png)

### 数据获取与清洗

![](img/fe5b6ec0f2faa85f014aad37daea36de_163.png)

1.  **获取日频数据**（以`pandas-datareader`为例，注意其可能已变更，`yfinance`是当前流行选择）：
    ```python
    import yfinance as yf
    # 获取苹果公司股票数据
    aapl = yf.download('AAPL', start='2020-01-01', end='2023-01-01')
    print(aapl.head())
    print(aapl.shape)
    ```

![](img/fe5b6ec0f2faa85f014aad37daea36de_165.png)

2.  **基础处理**：
    ```python
    # 提取收盘价
    close_prices = aapl['Close']
    # 计算简单收益率
    returns = close_prices.pct_change().dropna()
    # 重采样为周数据（每周三收盘价）
    weekly_prices = close_prices.resample('W-WED').last()
    # 将周数据扩展为日数据并填充
    daily_expanded = weekly_prices.resample('D').ffill()
    ```

![](img/fe5b6ec0f2faa85f014aad37daea36de_167.png)

![](img/fe5b6ec0f2faa85f014aad37daea36de_169.png)

3.  **处理季节性**：某些金融数据（如能源价格）具有季节性。可使用季节分解来识别和去除。
    ```python
    from statsmodels.tsa.seasonal import seasonal_decompose
    # 假设`series`是待分析的时间序列
    decomposition = seasonal_decompose(series, model='additive', period=12) # 假设周期为12个月
    trend = decomposition.trend
    seasonal = decomposition.seasonal
    residual = decomposition.resid
    # 去除季节性： deseasonalized = series - seasonal
    ```

## 总结与作业 📝

本节课中我们一起学习了Python金融数据分析的核心库：`statsmodels`用于统计建模与检验，`SciPy`用于数学计算与优化，并掌握了金融时间序列的获取、清洗、转换与基础建模方法。

![](img/fe5b6ec0f2faa85f014aad37daea36de_171.png)

**课后作业**：
1.  从数据源（推荐使用`yfinance`）获取谷歌（GOOGL）、苹果（AAPL）和脸书（META）从2005年1月1日到2011年12月31日的日收盘价数据。
2.  将数据合并到一个`DataFrame`中，索引为时间，列名为各股票代码。
3.  计算三只股票的日收益率。
4.  以2005年至2010年的数据作为训练集，建立以谷歌收益率为因变量（Y），苹果和脸书收益率为自变量（X）的线性回归模型。
5.  使用2011年的数据作为测试集，用训练好的模型预测谷歌的收益率。
6.  在同一张图上绘制2011年谷歌收益率的**预测值**与**真实值**，进行对比。

![](img/fe5b6ec0f2faa85f014aad37daea36de_173.png)

通过完成这个作业，你将综合运用数据获取、处理、建模与预测的全流程。