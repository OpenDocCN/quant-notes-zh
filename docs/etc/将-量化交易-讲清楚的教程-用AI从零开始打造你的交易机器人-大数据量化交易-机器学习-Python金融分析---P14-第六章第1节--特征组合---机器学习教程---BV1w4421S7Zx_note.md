# 量化交易教程：P14：第六章第1节：特征组合 🧩

![](img/d34fea3f3c466b09ee0082908f68882a_1.png)

## 概述
在本节课中，我们将学习如何为期权交易构建核心的计算模块。我们将从理解期权的基本概念和数据开始，逐步实现一个用于期权定价和风险指标计算的Python类，并探讨如何高效地处理大量期权合约数据。

---

![](img/d34fea3f3c466b09ee0082908f68882a_3.png)

![](img/d34fea3f3c466b09ee0082908f68882a_5.png)

## 什么是期权？📜

![](img/d34fea3f3c466b09ee0082908f68882a_7.png)

上一节我们介绍了因子组合的概念，本节中我们来看看如何将这些概念应用到更复杂的金融衍生品——期权上。

期权是一种金融衍生工具，赋予持有者在未来特定日期或之前，以特定价格买入或卖出标的资产的权利。我们从官方定义开始理解。

![](img/d34fea3f3c466b09ee0082908f68882a_1.png)

上海证券交易所将期权定义为一种合约。其核心要素包括：
*   **标的资产**：期权所基于的资产，如ETF或指数。
*   **行权价**：预先约定的买卖价格。
*   **到期日**：期权有效的最后日期。
*   **类型**：看涨期权或看跌期权。
*   **合约乘数**：每张合约代表的标的资产数量。

对于初学者，理解这些基本概念是构建交易系统的基础。

---

## 期权市场与数据 📊

理解了期权是什么之后，我们需要了解它的交易场所和可获得的数据格式。

目前，A股市场主要的场内期权产品包括：
1.  **ETF期权**：如上证50ETF期权、沪深300ETF期权，在上海和深圳证券交易所交易。
2.  **股指期权**：如沪深300股指期权，在中国金融期货交易所交易。

机构交易目前仍以流动性较好的ETF期权为主。以下是期权报价的典型界面，称为“T型报价表”：

![](img/d34fea3f3c466b09ee0082908f68882a_9.png)

![](img/d34fea3f3c466b09ee0082908f68882a_11.png)

![](img/d34fea3f3c466b09ee0082908f68f882a_5.png)

![](img/d34fea3f3c466b09ee0082908f68882a_13.png)

![](img/d34fea3f3c466b09ee0082908f68882a_15.png)

它按行权价和到期月份排列，直观展示了不同合约的买卖报价。

### 期权数据来源与处理
以下是构建研究系统时需要考虑的数据：

![](img/d34fea3f3c466b09ee0082908f68882a_17.png)

![](img/d34fea3f3c466b09ee0082908f68882a_19.png)

**1. 合约基本信息**
我们需要一个数据库来存储所有期权合约的静态信息。以下是从万得终端获取的数据表示例：

![](img/d34fea3f3c466b09ee0082908f68882a_3.png)

关键字段包括：合约代码、标的资产、类型、行权价、到期日、合约乘数等。

**2. 行情数据**
对于研究和回测，我们需要历史行情数据。以下是原始的tick数据示例：

![](img/d34fea3f3c466b09ee0082908f68882a_7.png)

数据包含时间、五档买卖盘、最新价、成交量等。通常，我们会将tick数据合成分钟级数据用于分析。

**数据处理注意事项**：
*   **分红处理**：ETF分红会导致合约调整（行权价和乘数变化），历史数据需要对应处理。
*   **新合约上市**：当标的价格变动剧烈时，交易所会挂牌新合约以保证市场覆盖。
*   **数据精度**：期权tick数据精度可能为1秒，对于高频策略可能需要寻找更高精度的数据源。

---

## 构建期权定价类 🛠️

掌握了数据格式后，我们就可以开始构建程序化的核心——期权定价类。

我们的目标是创建一个`EuropeanOption`类，它能够：
1.  根据市场参数计算期权理论价格。
2.  计算期权的风险指标。
3.  根据市场价格反推隐含波动率。

![](img/d34fea3f3c466b09ee0082908f68882a_21.png)

![](img/d34fea3f3c466b09ee0082908f68882a_23.png)

### 1. 基础信息类
首先，我们创建一个用于存储期权合约基本信息的类。

![](img/d34fea3f3c466b09ee0082908f68882a_25.png)

![](img/d34fea3f3c466b09ee0082908f68882a_27.png)

```python
class BaseOption:
    """存储期权基本信息的类"""
    def __init__(self, sec_code, underlying, option_type, strike_price, expire_date, contract_unit=10000):
        self.sec_code = sec_code          # 合约代码
        self.underlying = underlying      # 标的资产代码
        self.option_type = option_type    # 类型：'C'（看涨）或'P'（看跌）
        self.strike_price = strike_price  # 行权价
        self.expire_date = expire_date    # 到期日
        self.contract_unit = contract_unit # 合约乘数
```

### 2. 定价与风险计算类
接下来，我们创建核心的定价类，它继承自`BaseOption`，并实现布莱克-斯科尔斯模型。

![](img/d34fea3f3c466b09ee0082908f68882a_29.png)

```python
import numpy as np
from scipy.stats import norm

![](img/d34fea3f3c466b09ee0082908f68882a_31.png)

![](img/d34fea3f3c466b09ee0082908f68882a_33.png)

class EuropeanOption(BaseOption):
    """欧式期权定价与风险计算类"""
    
    def __init__(self, sec_code, underlying, option_type, strike_price, expire_date, contract_unit=10000):
        super().__init__(sec_code, underlying, option_type, strike_price, expire_date, contract_unit)
    
    def calculate_d1_d2(self, S, K, T, sigma, r=0.03):
        """计算布莱克-斯科尔斯模型中的d1和d2"""
        d1 = (np.log(S / K) + (r + 0.5 * sigma ** 2) * T) / (sigma * np.sqrt(T))
        d2 = d1 - sigma * np.sqrt(T)
        return d1, d2
    
    def price(self, S, K, T, sigma, r=0.03):
        """计算期权理论价格"""
        d1, d2 = self.calculate_d1_d2(S, K, T, sigma, r)
        
        if self.option_type == 'C':  # 看涨期权
            price = S * norm.cdf(d1) - K * np.exp(-r * T) * norm.cdf(d2)
        else:  # 看跌期权
            price = K * np.exp(-r * T) * norm.cdf(-d2) - S * norm.cdf(-d1)
        return price
    
    def delta(self, S, K, T, sigma, r=0.03):
        """计算Delta值"""
        d1, _ = self.calculate_d1_d2(S, K, T, sigma, r)
        if self.option_type == 'C':
            delta_val = norm.cdf(d1)
        else:
            delta_val = norm.cdf(d1) - 1
        return delta_val
    
    def gamma(self, S, K, T, sigma, r=0.03):
        """计算Gamma值"""
        d1, _ = self.calculate_d1_d2(S, K, T, sigma, r)
        gamma_val = norm.pdf(d1) / (S * sigma * np.sqrt(T))
        return gamma_val
    
    def implied_volatility(self, market_price, S, K, T, r=0.03, precision=1e-5, max_iter=100):
        """使用二分法计算隐含波动率"""
        vol_low, vol_high = 0.001, 5.0  # 波动率搜索范围
        
        for i in range(max_iter):
            vol_mid = (vol_low + vol_high) / 2
            price_mid = self.price(S, K, T, vol_mid, r)
            
            if abs(price_mid - market_price) < precision:
                return vol_mid
            
            if price_mid < market_price:
                vol_low = vol_mid
            else:
                vol_high = vol_mid
        
        return (vol_low + vol_high) / 2  # 返回近似值
```

**代码要点说明**：
*   `calculate_d1_d2`: 计算模型核心参数。
*   `price`: 根据B-S公式计算理论价格。
*   `delta`/`gamma`: 计算期权的一阶和二阶风险指标。
*   `implied_volatility`: 通过二分法，根据市场价格反推隐含波动率。

---

## 高效计算与向量化 ⚡

在实盘交易或处理大量历史数据时，效率至关重要。我们需要同时为数百个合约计算Greeks和隐含波动率。

### 循环与向量化的对比
低效的做法是使用循环逐个合约计算：
```python
# 低效做法
results = []
for option in option_list:
    iv = option.implied_volatility(market_price, S, K, T)
    results.append(iv)
```

![](img/d34fea3f3c466b09ee0082908f68882a_35.png)

![](img/d34fea3f3c466b09ee0082908f68882a_37.png)

![](img/d34fea3f3c466b09ee0082908f68882a_39.png)

高效的做法是利用NumPy进行向量化计算。核心思想是将所有合约的参数组成数组，一次性计算。

![](img/d34fea3f3c466b09ee0082908f68882a_41.png)

```python
# 高效做法：向量化计算隐含波动率（概念示例）
def vectorized_implied_vol(market_prices, S_array, K_array, T_array, r=0.03, iterations=10):
    """
    批量计算隐含波动率
    使用固定次数的二分法迭代，避免while循环判断
    market_prices, S_array等均为形状相同的numpy数组
    """
    vol_low = np.full_like(market_prices, 0.001)
    vol_high = np.full_like(market_prices, 5.0)
    
    for _ in range(iterations):  # 固定迭代10-12次，足以达到所需精度
        vol_mid = (vol_low + vol_high) / 2
        # 此处需调用向量化的price计算函数
        prices_mid = vectorized_bs_price(S_array, K_array, T_array, vol_mid, r)
        
        # 更新边界
        mask = prices_mid < market_prices
        vol_low = np.where(mask, vol_mid, vol_low)
        vol_high = np.where(mask, vol_high, vol_mid)
    
    return (vol_low + vol_high) / 2
```

**向量化关键**：
1.  将`price`、`calculate_d1_d2`等函数改写成完全接受numpy数组输入的形式。
2.  用固定次数的二分迭代替代`while`循环，便于并行处理。
3.  利用布尔索引`np.where`同时更新所有合约的波动率边界。

---

## 实践任务与总结 🎯

### 本节课实践任务
为了巩固所学，请完成以下任务：
1.  **完善`EuropeanOption`类**：补全`vega`, `theta`, `rho`等其他Greeks的计算方法。
2.  **数据处理**：使用提供的期权tick数据，合成分钟级数据（9:30-11:30, 13:00-15:00）。
3.  **计算ATM合约指标**：针对2018年1月2日10:00的平值期权合约，计算其理论价格、Delta、Gamma和隐含波动率。
4.  **尝试向量化**：思考如何修改`EuropeanOption`类，使其核心方法能接受数组输入进行批量计算。

### 总结
本节课中我们一起学习了：
*   **期权基础**：了解了期权的定义、核心要素和国内市场构成。
*   **数据基石**：认识了期权交易所需的两类关键数据：合约基本信息和市场行情数据。
*   **核心实现**：从零开始构建了一个欧式期权定价类，实现了理论定价、风险指标和隐含波动率计算。
*   **效率优化**：探讨了使用向量化技术批量处理数百个期权合约的重要性与方法。

![](img/d34fea3f3c466b09ee0082908f68882a_43.png)

构建稳健高效的期权定价模块，是开发复杂量化策略（如波动率交易、期权做市、套利）的第一步。在接下来的课程中，我们将以此为基础，探索如何将期权策略整合到我们的回测框架中。