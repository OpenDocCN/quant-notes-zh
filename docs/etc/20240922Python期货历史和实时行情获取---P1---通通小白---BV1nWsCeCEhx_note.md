# Python期货量化入门：P1：为什么学Python与基础行情获取 🐍

在本节课中，我们将要学习Python在金融量化领域的巨大优势，并初步掌握如何获取期货的历史与实时行情数据。课程将从Python的实用性讲起，逐步过渡到具体的行情接口使用。

## 为什么学习Python？💡

学会Python能带来多方面的收益。首先，它能显著提高工作效率。Python可以批量处理、汇总电子表格，其能力远超电子表格的宏或公式。其次，Python能够实现网络爬虫，自动获取所需的资料文档，或者实现自动抢单等功能，例如以往的抢购或网购抢单。再者，掌握Python技能可以用于业余接单赚钱。在当前投资实体生意回报困难的环境下，学习Python是一项几乎无本的投资，因为人人家里都有电脑。此外，所有与钱相关的投资，如股票、期货、外汇、彩票等，都可以利用Python进行量化分析和交易。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_1.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_3.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_5.png)

从2017年至今，Python在编程语言排行榜中一直位居第一。如果想学习编程，建议首先学习Python。若想进一步深入，可以再学习C++。掌握了Python和C++，就有可能成为顶级程序员。其他许多语言，如R语言、C#等，其应用可能受限于操作系统平台。例如，未来的鸿蒙系统及国产操作系统可能无法运行C#程序。而Python是跨平台的，可以在包括玩具机器人、手机、Linux、Windows在内的所有系统上运行，这对于提高办公效率有很大作用。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_7.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_9.png)

## 如何学习Python？🚀

学习Python，建议选择“穿云箭中文Python”。它的核心理念是“会中文就会写程序”，支持中英文Python语法混合编程。所有能在标准Python环境（如IDLE、VS Code、PyCharm）上运行的程序，都能在穿云箭上运行。同时，它支持中文和英文代码的一键无缝切换。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_11.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_13.png)

以下是穿云箭中文Python的一个简单示例，演示了获取股票数据并计算KDJ指标的过程：

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_15.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_17.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_19.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_20.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_22.png)

```python
导入 numpy 命名为 np
从 hp_tdx 导入 *
行情接口 = 连接站点(ip='地址', port=端口)
日线数据 = 获取股票数据(类型=4, 市场=1, 代码='股票代码')
输出(日线数据)
小白初始化(日线数据)
K, D, J = KDJ(日线数据, N=9, M1=3, M2=3)
```

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_24.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_26.png)

该平台还提供了大量高级图形模块和通达信指标函数，并能将复杂的通达信指标公式（如“六脉神剑”）一键转换为可回测、可实盘交易的策略代码，极大提升了量化策略的开发效率。

## 期货行情获取方式 📊

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_28.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_30.png)

在量化交易中，获取准确、及时的行情数据是第一步。本节我们将介绍两种获取期货行情的方式。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_32.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_34.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_36.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_38.png)

### 方式一：通过通达信接口获取

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_40.png)

我们对 `PYTDX` 库进行了封装，形成了 `hp_ext_tdx` 模块（EXT表示扩展行情），用于获取期货等扩展市场数据。这个模块不是开源的，但包含在穿云箭目录中，安装穿云箭后即可直接调用。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_42.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_44.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_46.png)

使用步骤如下：
1.  **连接行情服务器**：需要连接基本行情服务器和扩展行情服务器。
2.  **获取数据**：使用特定的市场代码和合约代码获取K线、分时、分笔、盘口等数据。
3.  **断开连接**：完成操作后断开服务器连接，释放资源。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_48.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_50.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_52.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_54.png)

以下是核心操作代码框架：

```python
# 连接服务器
连接基本行情服务器(ip='基本服务器IP', port=基本服务器端口)
连接扩展行情服务器(ip='扩展服务器IP', port=扩展服务器端口)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_56.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_58.png)

# 定义合约代码（示例：市场代码, 合约代码）
合约代码 = (市场代码, '合约代码字符串')

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_60.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_62.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_64.png)

# 获取日K线数据
K线数据 = 获取K线数据(合约代码, 周期='日线')

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_66.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_68.png)

# 获取实时分时图数据
分时数据 = 获取分时图(合约代码)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_70.png)

# 操作完成后断开连接
断开扩展行情服务器()
断开基本行情服务器()
```

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_72.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_74.png)

不同的交易所对应不同的市场代码，例如大连商品交易所可能是29，上海期货交易所可能是30。通过此接口，可以获取国内期货、国际期货甚至全球股票市场的行情数据。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_76.png)

### 方式二：通过天勤量化接口获取

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_78.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_80.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_82.png)

天勤量化是一个专注于国内期货、期权市场的量化交易接口。它支持实时行情获取和全自动交易。散户在期货公司开户后，即可使用天勤接口进行程序化交易。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_84.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_86.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_88.png)

天勤接口的典型使用模式是“调仓循环”，其核心逻辑如下：

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_90.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_92.png)

```python
# 伪代码逻辑示意
while True:
    最新K线 = 获取最新K线()
    当前持仓 = 获取账户持仓()
    目标仓位 = 计算目标仓位(最新K线) # 根据策略计算，例如 1（多一手）、0（空仓）、-1（空一手）

    if 当前持仓 != 目标仓位:
        下单调仓(目标仓位) # 此函数会处理平仓、开仓，使净持仓等于目标仓位
```

这种模式会自动处理多空头寸的调整，确保持仓状态与策略信号一致。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_94.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_96.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_98.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_100.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_102.png)

## 利用AI辅助与指标公式实战 🤖

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_104.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_106.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_108.png)

现在学习编程比以往更容易，可以借助AI工具（如ChatGPT、文心一言）。你可以直接要求AI“用Python写一个双均线交易策略”，它会生成基础代码框架。虽然生成的代码可能需要调整，但极大地降低了入门门槛。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_110.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_112.png)

在穿云箭平台中，最强大的功能之一是直接运行通达信指标公式源码。你无需将复杂的公式手动翻译成Python代码。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_113.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_115.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_117.png)

实战步骤：
1.  获得一个通达信指标公式源码（文本格式）。
2.  在穿云箭中，使用 `通达信公式库` 模块来解析和运行该源码。
3.  直接得到计算结果，并可用于绘图或交易信号判断。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_119.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_121.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_122.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_124.png)

以下是一个简化的示例流程：

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_126.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_128.png)

```python
# 1. 导入公式解析模块
从 hp_formula 导入 *

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_130.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_132.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_134.png)

# 2. 获取基础行情数据
行情数据 = 获取期货K线数据(...)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_136.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_138.png)

# 3. 创建公式对象并加载数据
公式引擎 = 通达信公式(行情数据)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_140.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_142.png)

# 4. 运行指标公式源码（例如六脉神剑）
指标结果 = 公式引擎.运行(指标公式源码字符串)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_144.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_146.png)

# 5. 指标结果已自动添加到行情数据中，可直接使用
买卖信号 = 指标结果['信号']
```

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_148.png)

通过这种方式，即使是不懂编程的投资者，只要熟悉通达信指标公式，也能快速将自己的交易思想转化为可回测、可实盘的量化策略。

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_150.png)

## 总结 🎯

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_152.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_154.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_156.png)

![](img/e7cd0ba72d3ea2f4a21cf2632ebbf9f9_158.png)

本节课我们一起学习了Python在金融量化领域的核心价值，包括提高效率、自动化和创收的潜力。我们重点介绍了两种获取期货行情数据的方法：基于通达信封装的 `hp_ext_tdx` 接口和面向国内期货的`天勤量化`接口。最后，我们探讨了如何利用AI工具辅助编程，并重磅演示了在“穿云箭中文Python”平台中，如何无需编写复杂代码，直接使用通达信指标公式源码进行量化分析，这为初学者开启量化交易之门提供了一条捷径。掌握这些基础，是构建自动化交易系统的第一步。