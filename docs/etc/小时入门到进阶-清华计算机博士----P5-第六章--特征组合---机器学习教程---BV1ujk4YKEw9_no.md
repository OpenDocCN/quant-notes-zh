# 量化交易实战教程：第六章：期权数据处理与基础计算 📊

![](img/ad005f8643016de1524429483e1856c3_1.png)

在本节课中，我们将学习期权交易的基础知识，并动手构建一个用于期权定价和风险指标计算的Python类。我们将从获取和处理期权数据开始，逐步实现核心的定价模型。

![](img/ad005f8643016de1524429483e1856c3_3.png)

![](img/ad005f8643016de1524429483e1856c3_5.png)

![](img/ad005f8643016de1524429483e1856c3_7.png)

## 什么是期权？📈

上一节我们介绍了特征组合，本节中我们来看看金融衍生品中一个重要的领域——期权。期权是一种赋予持有者在未来特定日期以特定价格买入或卖出标的资产权利的合约。

期权交易在A股和商品市场正蓬勃发展，是量化投资的一个重要切入点。在成熟的欧美市场，期权的成交量通常大于期货。

## 期权基础概念与数据 🔍

要研究期权交易，第一步是获取数据。在生产环境中，我们通常需要维护两个数据库：一个是所有期权合约的基本信息库，另一个是市场行情数据。

以下是期权合约的基本信息字段，通常可以从万得（Wind）等数据终端获取：

*   **合约代码 (Security Code)**: 期权的唯一标识符。
*   **标的资产 (Underlying Asset)**: 期权对应的基础资产，如50ETF。
*   **期权类型 (Option Type)**: 看涨期权（Call）或看跌期权（Put）。
*   **行权价 (Exercise Price)**: 合约约定的买卖价格。
*   **合约乘数 (Contract Unit)**: 每张合约对应的标的资产数量，通常为10000。
*   **上市日 (Listed Date)**: 合约开始交易的日期。
*   **到期日 (Expiry Date)**: 合约失效的日期（通常比行权日晚一天）。

![](img/ad005f8643016de1524429483e1856c3_9.png)

![](img/ad005f8643016de1524429483e1856c3_11.png)

![](img/ad005f8643016de1524429483e1856c3_13.png)

![](img/ad005f8643016de1524429483e1856c3_15.png)

目前，场内交易的ETF期权主要有上证50ETF期权和沪深300ETF期权等。期权的交易时间与股票市场一致。

## 数据处理与注意事项 ⚙️

![](img/ad005f8643016de1524429483e1856c3_17.png)

![](img/ad005f8643016de1524429483e1856c3_19.png)

我们通常从交易所的CTP接口获取tick级别的行情数据。在实际处理时，需要注意以下几点：

*   **数据精度**: 期权tick数据的时间精度可能为1秒，而标的ETF的价格约3秒更新一次。对于高频策略，需要考虑更精确的数据源。
*   **分钟数据合成**: 对于大多数日内策略，使用分钟频率的数据已足够。合成分钟数据时，需注意处理时间戳的归属问题，避免引入未来函数。
*   **特殊事件处理**: 当标的ETF分红时，交易所会调整期权的合约乘数和行权价，以保证合约总值不变。在回测和处理历史数据时，需要单独处理这些日期。

## 构建期权定价类 💻

接下来，我们将构建一个用于欧式期权定价和风险指标计算的Python类。我们主要使用经典的Black-Scholes-Merton模型。

首先，我们定义一个基础的期权信息类，用于存储合约的基本属性。

```python
class BaseOption:
    def __init__(self, security_code, underlying, option_type, strike_price, expiry_date, contract_unit=10000):
        self.security_code = security_code
        self.underlying = underlying
        self.option_type = option_type  # ‘C‘ for Call, ‘P‘ for Put
        self.strike_price = strike_price
        self.expiry_date = expiry_date
        self.contract_unit = contract_unit
```

然后，我们实现一个用于定价和计算希腊值的类。它继承自`BaseOption`，并接收市场数据。

![](img/ad005f8643016de1524429483e1856c3_21.png)

```python
import numpy as np
from scipy.stats import norm

![](img/ad005f8643016de1524429483e1856c3_23.png)

class EuropeanOption(BaseOption):
    def __init__(self, security_code, underlying, option_type, strike_price, expiry_date, contract_unit=10000):
        super().__init__(security_code, underlying, option_type, strike_price, expiry_date, contract_unit)

    def calculate_price(self, S, T, r, sigma):
        """根据Black-Scholes公式计算期权价格。"""
        d1 = (np.log(S / self.strike_price) + (r + 0.5 * sigma**2) * T) / (sigma * np.sqrt(T))
        d2 = d1 - sigma * np.sqrt(T)

        if self.option_type == ‘C‘:  # 看涨期权
            price = S * norm.cdf(d1) - self.strike_price * np.exp(-r * T) * norm.cdf(d2)
        else:  # 看跌期权
            price = self.strike_price * np.exp(-r * T) * norm.cdf(-d2) - S * norm.cdf(-d1)
        return price

    def calculate_delta(self, S, T, r, sigma):
        """计算期权的Delta值。"""
        d1 = (np.log(S / self.strike_price) + (r + 0.5 * sigma**2) * T) / (sigma * np.sqrt(T))
        if self.option_type == ‘C‘:
            delta = norm.cdf(d1)
        else:
            delta = norm.cdf(d1) - 1
        return delta

    def calculate_gamma(self, S, T, r, sigma):
        """计算期权的Gamma值。"""
        d1 = (np.log(S / self.strike_price) + (r + 0.5 * sigma**2) * T) / (sigma * np.sqrt(T))
        gamma = norm.pdf(d1) / (S * sigma * np.sqrt(T))
        return gamma

    # 其他希腊值（如Vega, Theta, Rho）可以类似实现
```

![](img/ad005f8643016de1524429483e1856c3_25.png)

## 隐含波动率计算与向量化优化 🚀

![](img/ad005f8643016de1524429483e1856c3_27.png)

![](img/ad005f8643016de1524429483e1856c3_29.png)

在实际交易和研究中，我们常常需要根据市场价格反推隐含波动率。对于大量合约的计算，使用循环效率很低，我们需要进行向量化优化。

计算隐含波动率的常用方法是二分法。一个关键的优化技巧是：对于多个合约，我们固定二分法的迭代次数（例如12次），而不是等待每个合约都收敛到特定精度。这样可以避免对每个合约进行条件判断，从而实现高效的向量化计算。

以下是向量化计算隐含波动率的思路伪代码：

![](img/ad005f8643016de1524429483e1856c3_31.png)

![](img/ad005f8643016de1524429483e1856c3_33.png)

```python
def calculate_implied_vol_vectorized(S_array, K_array, T_array, r, option_price_array, option_type_array, max_iter=12):
    """
    使用二分法向量化计算多个期权的隐含波动率。
    S_array, K_array等均为NumPy数组。
    """
    vol_low = np.ones_like(S_array) * 0.001  # 波动率下限
    vol_high = np.ones_like(S_array) * 5.0   # 波动率上限

    for i in range(max_iter):
        vol_mid = (vol_low + vol_high) / 2
        # 向量化计算当前波动率下的理论价格
        calculated_price = vectorized_bs_price(S_array, K_array, T_array, r, vol_mid, option_type_array)
        # 向量化比较并更新边界
        # 如果理论价格 > 市场价格，则波动率偏高，调低vol_high
        mask_high = calculated_price > option_price_array
        vol_high[mask_high] = vol_mid[mask_high]
        # 反之，调高vol_low
        vol_low[~mask_high] = vol_mid[~mask_high]

    implied_vol = (vol_low + vol_high) / 2
    return implied_vol
```

![](img/ad005f8643016de1524429483e1856c3_35.png)

![](img/ad005f8643016de1524429483e1856c3_36.png)

![](img/ad005f8643016de1524429483e1856c3_38.png)

## 实践任务与总结 📝

本节课我们一起学习了期权的基础概念、数据处理方法，并动手构建了期权定价的核心类。

以下是本次课程的实践任务，请同学们课后完成：

1.  **数据处理**: 使用提供的tick数据，合成50ETF期权的分钟频率数据。
2.  **定价实现**: 完善`EuropeanOption`类，实现看跌期权的定价方法，并补充其他希腊值（如Vega, Theta）的计算。
3.  **隐含波动率**: 实现单个期权隐含波动率的计算函数（使用二分法或牛顿法）。
4.  **实战计算**: 找到2018年1月2日10:00时，当月到期的平值期权合约，利用当时的市场数据，计算其理论价格、Delta、Gamma和隐含波动率。
5.  **可视化探索（可选）**: 将2018年2月（贸易战初期）平值期权的隐含波动率走势绘制出来，观察市场波动率的剧烈变化。

![](img/ad005f8643016de1524429483e1856c3_40.png)

通过完成这些任务，你将建立起期权分析的基本框架，为后续构建期权策略和风险管理系统打下坚实基础。