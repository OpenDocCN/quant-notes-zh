# AI云量化：第4关：列表、元组与Python量化策略代码学习 📊

![](img/3f1dad275def8af5340d47947492c851_0.png)

在本节课中，我们将学习Python中两个重要的数据结构：列表和元组。它们是存储和管理数据集合的基础工具，在量化策略开发中，常被用来存储股票代码、价格序列、交易信号等数据。掌握它们的使用，是编写有效量化策略代码的关键一步。

## 列表：可变的数据序列 📝

![](img/3f1dad275def8af5340d47947492c851_2.png)

列表是Python中最常用的数据结构之一，它是一个有序、可变的元素集合。这意味着你可以在创建列表后，自由地添加、删除或修改其中的元素。

![](img/3f1dad275def8af5340d47947492c851_4.png)

列表使用方括号 `[]` 定义，其中的元素用逗号分隔。

![](img/3f1dad275def8af5340d47947492c851_6.png)

**代码示例：**
```python
# 创建一个包含股票代码的列表
stock_list = ['AAPL', 'GOOGL', 'TSLA', 'MSFT']
print(stock_list)  # 输出: ['AAPL', 'GOOGL', 'TSLA', 'MSFT']
```

### 列表的基本操作

![](img/3f1dad275def8af5340d47947492c851_8.png)

以下是列表的一些核心操作，它们能帮助你灵活地处理数据。

**代码示例：**
```python
# 1. 访问元素：通过索引（从0开始）
print(stock_list[0])  # 输出: AAPL
print(stock_list[-1]) # 输出: MSFT (负索引表示从末尾开始)

![](img/3f1dad275def8af5340d47947492c851_9.png)

# 2. 修改元素
stock_list[1] = 'NVDA'
print(stock_list)  # 输出: ['AAPL', 'NVDA', 'TSLA', 'MSFT']

![](img/3f1dad275def8af5340d47947492c851_11.png)

# 3. 添加元素
stock_list.append('AMZN')  # 在末尾添加
print(stock_list)  # 输出: ['AAPL', 'NVDA', 'TSLA', 'MSFT', 'AMZN']

![](img/3f1dad275def8af5340d47947492c851_13.png)

stock_list.insert(2, 'META')  # 在索引2处插入
print(stock_list)  # 输出: ['AAPL', 'NVDA', 'META', 'TSLA', 'MSFT', 'AMZN']

# 4. 删除元素
removed_item = stock_list.pop()  # 删除并返回末尾元素
print(removed_item)  # 输出: AMZN
print(stock_list)    # 输出: ['AAPL', 'NVDA', 'META', 'TSLA', 'MSFT']

![](img/3f1dad275def8af5340d47947492c851_15.png)

stock_list.remove('TSLA')  # 删除第一个匹配到的‘TSLA’
print(stock_list)  # 输出: ['AAPL', 'NVDA', 'META', 'MSFT']

![](img/3f1dad275def8af5340d47947492c851_16.png)

# 5. 列表切片：获取子列表
sub_list = stock_list[1:3]  # 获取索引1到2的元素（不包含3）
print(sub_list)  # 输出: ['NVDA', 'META']
```

![](img/3f1dad275def8af5340d47947492c851_17.png)

## 元组：不可变的数据序列 🔒

![](img/3f1dad275def8af5340d47947492c851_18.png)

上一节我们介绍了灵活多变的列表，本节中我们来看看它的“兄弟”——元组。元组与列表非常相似，都是有序的元素集合。但关键区别在于，**元组一旦创建，其内容就不能被修改（不可变）**。这使得元组在某些需要数据保护或作为字典键的场景下非常有用。

元组使用圆括号 `()` 定义。

![](img/3f1dad275def8af5340d47947492c851_20.png)

**代码示例：**
```python
# 创建一个包含股票代码和价格的元组
stock_info = ('AAPL', 175.25)
print(stock_info)  # 输出: ('AAPL', 175.25)

![](img/3f1dad275def8af5340d47947492c851_22.png)

# 尝试修改元组会引发错误
# stock_info[1] = 180.00  # 这行代码会报错：TypeError
```

### 元组的基本操作

![](img/3f1dad275def8af5340d47947492c851_24.png)

由于元组的不可变性，其操作比列表少，主要集中在访问和查询上。

![](img/3f1dad275def8af5340d47947492c851_26.png)

**代码示例：**
```python
# 1. 访问元素：与列表相同
print(stock_info[0])  # 输出: AAPL
print(stock_info[-1]) # 输出: 175.25

# 2. 元组切片：返回一个新的元组
new_tuple = stock_info[:]
print(new_tuple)  # 输出: ('AAPL', 175.25)

![](img/3f1dad275def8af5340d47947492c851_28.png)

# 3. 查询元素索引和计数
index = stock_info.index('AAPL')
count = stock_info.count(175.25)
print(f"‘AAPL’的索引是: {index}")  # 输出: ‘AAPL’的索引是: 0
print(f"175.25出现的次数: {count}") # 输出: 175.25出现的次数: 1
```

![](img/3f1dad275def8af5340d47947492c851_30.png)

## 列表与元组在量化策略中的应用实例 💡

![](img/3f1dad275def8af5340d47947492c851_32.png)

理解了列表和元组的基本概念后，我们来看一个简单的量化策略片段，了解它们如何在实际中协同工作。

![](img/3f1dad275def8af5340d47947492c851_33.png)

假设我们有一个策略，需要跟踪一篮子股票的历史最高价，并当某只股票价格突破其历史最高价时发出买入信号。

**代码示例：**
```python
# 使用元组存储每只股票的代码及其历史最高价（一旦设定，不应被意外修改）
stock_ath = [('AAPL', 182.63), ('MSFT', 340.65), ('GOOGL', 142.58)]

![](img/3f1dad275def8af5340d47947492c851_35.png)

# 使用列表存储当前的市场价格（价格是实时变动的）
current_prices = [185.10, 338.20, 141.75]

![](img/3f1dad275def8af5340d47947492c851_36.png)

# 策略逻辑：遍历检查
buy_signals = []  # 用一个空列表来收集买入信号
for i, (code, ath_price) in enumerate(stock_ath):
    if current_prices[i] > ath_price:
        # 当现价突破历史最高价，生成一个信号元组（不可变，保证信号记录不被篡改）
        signal = (code, ‘BUY’, current_prices[i])
        buy_signals.append(signal)  # 将信号添加到可变的信号列表中

print(“生成的买入信号:”, buy_signals)
# 输出可能为: 生成的买入信号: [('AAPL', 'BUY', 185.10)]
```

在这个例子中：
*   **元组** `(code, ath_price)` 和 `(code, ‘BUY’, price)` 用于存储需要被保护、定义后不再改变的数据结构。
*   **列表** `stock_ath`, `current_prices`, `buy_signals` 用于存储需要动态更新、添加或遍历的数据集合。

![](img/3f1dad275def8af5340d47947492c851_38.png)

## 总结 🎯

![](img/3f1dad275def8af5340d47947492c851_40.png)

本节课中我们一起学习了Python中两个核心的数据结构：列表和元组。
*   **列表** 是有序、可变的序列，使用 `[]` 定义，适用于需要频繁增删改数据的场景。
*   **元组** 是有序、不可变的序列，使用 `()` 定义，适用于存储不应更改的数据，或作为字典的键。

在量化编程中，列表常用于存储随时间变化的序列（如价格列表、持仓列表），而元组则适合存储固定的配置参数或作为数据记录。理解它们的区别并合理运用，是构建稳健、清晰量化策略代码的基础。在接下来的课程中，我们将利用这些数据结构，学习更复杂的策略逻辑和函数封装。