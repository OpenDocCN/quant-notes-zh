# 从零开始量化：第11课：CtaTemplate与数据库连接池 🧩

在本节课中，我们将学习量化交易框架中的核心策略模板类 `CtaTemplate`，并深入了解其与数据库交互的机制，特别是如何实现一个线程安全的数据库连接池。

## 概述

![](img/8018154a7693ff84172e3a0ad4333485_1.png)

![](img/8018154a7693ff84172e3a0ad4333485_3.png)

`CtaTemplate` 是所有CTA策略的基类，实盘策略必须继承自它才能被引擎正确加载和管理。本节课我们将剖析这个模板类的关键方法，并探讨如何高效、安全地连接数据库以存取行情数据。

## CtaTemplate 核心功能解析

上一节我们介绍了策略引擎的基本概念，本节中我们来看看策略的“骨架”——`CtaTemplate`。

### 初始化与变量管理

在 `__init__` 方法中，模板主要完成以下工作：
*   接收并存储 `CtaEngine` 实例和策略名称。
*   初始化 `variables` 和 `parameters` 列表。这两个列表决定了哪些变量和参数会被保存到本地，并显示在图形化界面上。

以下是需要保存的变量示例：
*   当日持仓状态
*   未完成的委托单

以下是不建议保存的变量示例：
*   通过历史数据实时计算出的指标（如均线）

**注意**：`variables` 列表必须使用 `copy` 进行复制。如果不复制，当多个子类继承并实例化时，它们会共享父类的同一个列表，导致数据混乱。

```python
from copy import copy
self.variables = copy(self.variables)  # 正确做法
self.variables.insert(0, ‘inited‘)     # 添加管理变量
```

### 参数设置与获取

`update_setting` 方法用于将配置文件中读取的参数动态地赋值给策略实例。它使用 `setattr` 函数实现。

类提供了两种获取参数的方法：
*   `get_class_parameters`: 获取类定义时的初始参数值。
*   `get_parameters`: 获取策略实例化并调用 `update_setting` 后的当前参数值。

这两者的区别在于，后者反映了用户可能修改过的配置。

### 数据加载机制

`load_bar` 和 `load_tick` 是策略获取历史数据的关键方法。其内部逻辑优先顺序如下：
1.  如果 `use_database=False`，首先尝试从数据服务商（如 `RQData`）的接口下载。
2.  如果从数据服务商获取失败，或 `use_database=True`，则从本地数据库加载。

这个设计确保了即使没有购买外部数据服务，也能依赖本地数据库进行回测和实盘初始化。

## 数据库连接池实现 🔄

上一节我们了解了模板如何调用数据，本节中我们来看看数据存储的核心——数据库连接的高效管理。

在并发场景下（例如一边录制行情，一边查询历史数据），多个线程同时操作数据库可能导致资源争用和错误。为了解决这个问题，我们需要使用数据库连接池。

### 连接池改造步骤

以下是使用 `peewee` 库的 `playhouse.pool` 模块将普通 SQLite 连接改造成连接池的关键步骤：

1.  **使用代理数据库对象**：首先用 `Proxy()` 创建一个占位符。
2.  **实例化连接池**：使用 `PooledSqliteDatabase` 替代原来的 `SqliteDatabase`，并设置最大连接数、超时时间等参数。
3.  **初始化代理**：将连接池实例指派给代理对象。

```python
from peewee import *
from playhouse.pool import PooledSqliteDatabase
from playhouse.shortcuts import ReconnectMixin

![](img/8018154a7693ff84172e3a0ad4333485_5.png)

![](img/8018154a7693ff84172e3a0ad4333485_7.png)

class RetryPooledSqliteDatabase(ReconnectMixin, PooledSqliteDatabase):
    pass

# 1. 创建代理
db_proxy = Proxy()

# 2. 实例化连接池
database = RetryPooledSqliteDatabase(‘test.db‘, max_connections=8, stale_timeout=300)

![](img/8018154a7693ff84172e3a0ad4333485_9.png)

![](img/8018154a7693ff84172e3a0ad4333485_11.png)

![](img/8018154a7693ff84172e3a0ad4333485_13.png)

# 3. 初始化代理
db_proxy.initialize(database)
```

### 多线程操作示例

![](img/8018154a7693ff84172e3a0ad4333485_15.png)

以下是一个简化的多线程示例，演示一个线程保存数据，另一个线程同时查询数据：

![](img/8018154a7693ff84172e3a0ad4333485_17.png)

![](img/8018154a7693ff84172e3a0ad4333485_19.png)

```python
import threading
import time

def save_data_thread():
    with db_proxy.connection_context():
        # 执行数据保存操作
        BarData.insert_many(data_list).execute()
        print(“数据保存完成”)

def query_data_thread():
    while active:
        try:
            with db_proxy.connection_context():
                # 执行数据查询操作
                query = BarData.select().where(BarData.symbol == ‘rb2405‘)
                print(f“查询到 {query.count()} 条数据”)
        except Exception as e:
            print(f“查询出错: {e}”)
        time.sleep(1)

# 启动线程
t1 = threading.Thread(target=save_data_thread)
t2 = threading.Thread(target=query_data_thread)
t1.start()
t2.start()
```

![](img/8018154a7693ff84172e3a0ad4333485_21.png)

使用连接池后，数据库连接被池化管理，线程按需获取和归还连接，避免了频繁创建/销毁连接的开销，也解决了多线程并发冲突的问题。对于量化场景，轻量级的 **SQLite** 配合连接池通常是完全足够的选择。

![](img/8018154a7693ff84172e3a0ad4333485_23.png)

![](img/8018154a7693ff84172e3a0ad4333485_25.png)

## 总结

![](img/8018154a7693ff84172e3a0ad4333485_27.png)

![](img/8018154a7693ff84172e3a0ad4333485_29.png)

![](img/8018154a7693ff84172e3a0ad4333485_31.png)

本节课我们一起学习了：
1.  **CtaTemplate 的核心角色**：作为所有CTA策略的基类，负责管理策略生命周期、参数变量持久化以及历史数据加载。
2.  **关键方法解析**：包括变量列表的 `copy` 必要性、参数动态更新机制以及 `load_bar` 的数据加载优先级逻辑。
3.  **数据库连接池的重要性与实现**：通过使用 `peewee` 的 `PooledSqliteDatabase`，我们可以轻松构建支持多线程安全访问的数据库层，这对于实盘中并发读写行情数据至关重要。

![](img/8018154a7693ff84172e3a0ad4333485_33.png)

![](img/8018154a7693ff84172e3a0ad4333485_35.png)

![](img/8018154a7693ff84172e3a0ad4333485_37.png)

理解 `CtaTemplate` 和稳健的数据库连接机制，是构建可靠、高效量化交易系统的基础。