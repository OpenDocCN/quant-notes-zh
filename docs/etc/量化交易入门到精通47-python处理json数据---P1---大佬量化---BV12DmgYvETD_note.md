# 量化交易入门到精通：47：Python处理JSON数据 📊

在本节课中，我们将要学习如何使用Python处理JSON数据。JSON是量化交易中存储和传输数据的常用格式，掌握其处理方法对于获取和分析股票行情、财务报表等数据至关重要。

## 概述

![](img/430ffab556893e91751e155f17d1a28e_1.png)

JSON（JavaScript Object Notation）是一种轻量级的数据交换格式，易于人阅读和编写，也易于机器解析和生成。Python内置了`json`模块，可以方便地进行JSON数据的编码（序列化）和解码（反序列化）。

![](img/430ffab556893e91751e155f17d1a28e_3.png)

## 1. 编码：将Python对象转换为JSON字符串

![](img/430ffab556893e91751e155f17d1a28e_5.png)

上一节我们介绍了JSON的基本概念，本节中我们来看看如何将Python中的数据（如字典）转换成JSON格式的字符串。这个过程称为编码或序列化。

首先，需要导入Python的`json`模块。

```python
import json
```

接下来，我们定义一个包含股票信息的Python字典，并使用`json.dumps()`函数将其转换为JSON字符串。

![](img/430ffab556893e91751e155f17d1a28e_7.png)

```python
# 定义一个Python字典
data = {
    "name": "贵州茅台",
    "code": "600519",
    "price": 1800
}

![](img/430ffab556893e91751e155f17d1a28e_9.png)

# 将字典编码为JSON字符串
json_string = json.dumps(data)
print(json_string)
print(type(json_string))
```

执行上述代码，你会得到一个JSON字符串。默认情况下，中文字符会被转换为Unicode转义序列（如`\u8d35\u5dde\u8305\u53f0`）。为了让输出更易读，我们可以使用`indent`参数进行格式化。

![](img/430ffab556893e91751e155f17d1a28e_11.png)

```python
# 格式化输出JSON字符串
formatted_json = json.dumps(data, indent=4, ensure_ascii=False)
print(formatted_json)
```

这里，`indent=4`表示使用4个空格进行缩进，`ensure_ascii=False`确保中文字符正常显示。

![](img/430ffab556893e91751e155f17d1a28e_13.png)

## 2. 解码：将JSON字符串转换为Python对象

学会了编码，接下来我们学习解码，即如何将JSON字符串转换回Python对象。这在我们从网络API或文件中读取JSON数据时非常有用。

![](img/430ffab556893e91751e155f17d1a28e_15.png)

我们使用`json.loads()`函数来完成这个操作。

![](img/430ffab556893e91751e155f17d1a28e_17.png)

```python
# 假设我们有一个JSON字符串
json_str = '{"name": "贵州茅台", "code": "600519", "price": 1800}'

# 将JSON字符串解码为Python字典
decoded_data = json.loads(json_str)
print(decoded_data)
```

![](img/430ffab556893e91751e155f17d1a28e_19.png)

解码后，`decoded_data`就是一个标准的Python字典，我们可以像操作普通字典一样访问其中的数据。

![](img/430ffab556893e91751e155f17d1a28e_21.png)

## 3. 文件读写：与JSON文件交互

在实际应用中，JSON数据常常存储在文件中。下面我们看看如何将Python数据写入JSON文件，以及如何从JSON文件中读取数据。

![](img/430ffab556893e91751e155f17d1a28e_23.png)

以下是写入JSON文件的步骤：

![](img/430ffab556893e91751e155f17d1a28e_25.png)

```python
# 要写入的数据
data_to_write = {
    "name": "贵州茅台",
    "code": "600519",
    "price": 1800
}

![](img/430ffab556893e91751e155f17d1a28e_27.png)

# 使用 with 语句打开文件，模式为 ‘w‘ (写入)
with open(‘stock.json‘, ‘w‘, encoding=‘utf-8‘) as f:
    # 使用 json.dump 将数据写入文件
    json.dump(data_to_write, f, indent=4, ensure_ascii=False)

![](img/430ffab556893e91751e155f17d1a28e_29.png)

print("文件写入成功")
```

执行后，当前目录下会生成一个名为`stock.json`的文件，内容是我们格式化的JSON数据。

接下来是从JSON文件读取数据的步骤：

```python
# 使用 with 语句打开文件，模式为 ‘r‘ (读取)
with open(‘stock.json‘, ‘r‘, encoding=‘utf-8‘) as f:
    # 使用 json.load 从文件加载数据
    loaded_data = json.load(f)

![](img/430ffab556893e91751e155f17d1a28e_31.png)

print("读取JSON文件成功，数据为：")
print(loaded_data)
```

![](img/430ffab556893e91751e155f17d1a28e_33.png)

## 4. 处理复杂数据结构

![](img/430ffab556893e91751e155f17d1a28e_35.png)

JSON不仅支持简单的键值对，还能嵌套列表、字典等复杂结构。这对于存储像股票历史行情这样的数据非常方便。

![](img/430ffab556893e91751e155f17d1a28e_37.png)

以下是一个处理包含历史价格列表的复杂JSON数据的例子：

```python
# 定义包含历史行情列表的复杂数据
complex_data = {
    "name": "贵州茅台",
    "code": "600519",
    "history": [
        {"date": "2020-01-01", "price": 1800},
        {"date": "2020-02-01", "price": 2000}
    ]
}

![](img/430ffab556893e91751e155f17d1a28e_39.png)

# 编码为JSON字符串
json_str_complex = json.dumps(complex_data, indent=4, ensure_ascii=False)
print("编码后的JSON字符串：")
print(json_str_complex)

# 解码回Python对象
decoded_complex = json.loads(json_str_complex)
print("\n解码后的Python对象：")
print(decoded_complex)

# 访问嵌套数据
print("\n访问特定数据：")
print("股票名称:", decoded_complex[‘name‘])
print("历史行情第一个记录:", decoded_complex[‘history‘][0])
print("历史行情第一个记录的日期:", decoded_complex[‘history‘][0][‘date‘])
print("历史行情第一个记录的价格:", decoded_complex[‘history‘][0][‘price‘])
```

![](img/430ffab556893e91751e155f17d1a28e_41.png)

通过下标和键名，我们可以轻松地访问到嵌套在数据结构深处的任何值。

![](img/430ffab556893e91751e155f17d1a28e_43.png)

## 总结

本节课中我们一起学习了Python处理JSON数据的核心技能。我们掌握了：
1.  **编码**：使用`json.dumps()`将Python对象转为JSON字符串。
2.  **解码**：使用`json.loads()`将JSON字符串转回Python对象。
3.  **文件操作**：使用`json.dump()`和`json.load()`进行JSON文件的写入和读取。
4.  **复杂数据处理**：如何编码、解码以及访问嵌套的列表和字典结构。

![](img/430ffab556893e91751e155f17d1a28e_45.png)

在量化交易中，熟练运用这些技能能帮助我们高效地处理各种来源的JSON格式数据，为数据分析和策略开发打下坚实基础。