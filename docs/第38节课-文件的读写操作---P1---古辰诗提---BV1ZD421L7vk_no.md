# Python基础课：第38课：文件的读写操作 📂

在本节课中，我们将学习Python中文件读写操作的基础知识。文件读写是编程中非常核心的功能，它允许我们将程序运行中的数据（如变量、参数、持仓信息等）持久化地保存到硬盘上，以便在程序关闭后或下次启动时重新加载使用。这对于量化交易等需要保存状态的场景尤为重要。

## 打开与关闭文件

文件操作通常遵循三个基本步骤：打开文件、进行读写操作、关闭文件。

以下是文件读写的基本流程：

1.  **打开文件**：使用 `open()` 函数，指定文件名、打开模式和编码格式。
2.  **执行操作**：对文件对象进行读取（`read`）或写入（`write`）操作。
3.  **关闭文件**：使用 `close()` 方法关闭文件，释放系统资源。

**代码示例：读取文件**
```python
f = open('test.txt', 'r', encoding='utf-8')
data = f.read()
print(data)
f.close()
```
在上面的代码中，`open('test.txt', 'r', encoding='utf-8')` 打开了名为 `test.txt` 的文件。`'r'` 表示以只读模式打开，`encoding='utf-8'` 指定了文件的编码格式。`f.read()` 读取了整个文件的内容，最后 `f.close()` 关闭了文件。

![](img/dc7f4e8fccba17134af03d1f70bfc706_1.png)

**重要提示**：在Python中，打开的文件对象必须被显式关闭，否则可能导致资源泄露。虽然Python有自动垃圾回收机制，但对于文件这类系统资源，它无法自动管理。

![](img/dc7f4e8fccba17134af03d1f70bfc706_3.png)

## 文件路径与打开模式

上一节我们介绍了文件操作的基本步骤，本节中我们来看看如何指定文件位置以及不同的文件打开模式。

![](img/dc7f4e8fccba17134af03d1f70bfc706_5.png)

### 文件路径

![](img/dc7f4e8fccba17134af03d1f70bfc706_7.png)

`open()` 函数的第一个参数是文件路径。它可以是当前目录下的文件名，也可以是绝对路径或相对路径。

**代码示例：使用路径**
```python
# 使用原始字符串避免转义问题
f = open(r'C:\Users\YourName\Documents\test.txt', 'r', encoding='utf-8')
# 或者使用双反斜杠
f = open('C:\\Users\\YourName\\Documents\\test.txt', 'r', encoding='utf-8')
```
在Windows系统中，路径分隔符是反斜杠 `\`。由于反斜杠在Python字符串中是转义字符，因此通常使用原始字符串（在字符串前加 `r`）或使用双反斜杠 `\\` 来表示路径。在macOS和Linux系统中，路径分隔符是正斜杠 `/`。

### 文件打开模式

`open()` 函数的第二个参数是模式（`mode`），它决定了文件如何被打开。

以下是主要的文件打开模式：

![](img/dc7f4e8fccba17134af03d1f70bfc706_9.png)

*   **`'r'`**：只读模式。文件必须存在，否则会报错。
*   **`'rb'`**：以二进制格式只读打开文件。
*   **`'r+'` 或 `'rb+'`**：读写模式。文件必须存在。写入的内容会从文件开头覆盖原有内容。
*   **`'w'`**：写入模式。如果文件存在，则清空文件内容；如果文件不存在，则创建新文件。
*   **`'wb'`**：以二进制格式写入模式。
*   **`'w+'` 或 `'wb+'`**：读写模式。如果文件存在则清空，不存在则创建。
*   **`'a'`**：追加模式。如果文件存在，则在文件末尾追加内容；如果文件不存在，则创建新文件。
*   **`'ab'`**：以二进制格式追加模式。
*   **`'a+'` 或 `'ab+'`**：读写模式。如果文件存在，则在文件末尾追加；如果文件不存在，则创建。

**记忆技巧**：
1.  只想读文件，用 `r`。
2.  想写文件，先判断：是否需要清空原文件？需要则用 `w`，不需要（只想追加）则用 `a`。
3.  想同时读写文件，先判断：是否需要清空？需要则用 `w+`，不需要则再判断：是从头覆盖写（`r+`）还是从末尾追加写（`a+`）。
4.  操作二进制文件（如图片、视频），在对应模式后加 `b`，如 `'rb'`, `'wb'`。

## 使用 `with` 语句简化操作

手动管理文件的打开和关闭容易遗忘，Python提供了 `with` 语句来自动管理上下文。

**代码示例：使用 `with` 语句**
```python
with open('test.txt', 'r', encoding='utf-8') as f:
    data = f.read()
    print(data)
# 离开 with 代码块后，文件会自动关闭，无需调用 f.close()
```
`with open(...) as f:` 这行代码打开文件并将文件对象赋值给变量 `f`。当 `with` 代码块内的语句执行完毕后，无论是否发生异常，文件都会自动被正确关闭。这是推荐的文件操作方式。

`with` 语句的背后原理是上下文管理协议。一个类只要实现了 `__enter__()` 和 `__exit__()` 这两个魔术方法，就可以被 `with` 语句使用。`as` 后面的变量接收的是 `__enter__()` 方法的返回值。

## JSON文件的读写

在量化编程和Web开发中，JSON（JavaScript Object Notation）是一种非常常用的轻量级数据交换格式。Python提供了 `json` 模块来方便地处理JSON数据。

`json` 模块主要有四个常用方法：
*   `json.loads()`：将JSON格式的**字符串**转换为Python对象（如字典、列表）。
*   `json.dumps()`：将Python对象转换为JSON格式的**字符串**。
*   `json.load()`：从**文件对象**中读取数据，并将其转换为Python对象。
*   `json.dump()`：将Python对象转换为JSON格式，并写入**文件对象**。

对于文件操作，我们最常用的是 `json.load()` 和 `json.dump()`。

### 写入JSON文件

**代码示例：保存数据到JSON文件**
```python
import json

data_to_save = {
    "ma5": 100,
    "ma20": 200,
    "position": 30
}

with open('config.json', 'w', encoding='utf-8') as f:
    json.dump(data_to_save, f, indent=4, ensure_ascii=False)
```
`json.dump(obj, fp)` 将Python字典 `data_to_save` 写入到文件对象 `f` 中。`indent=4` 参数使生成的JSON文件带有缩进，更易于阅读。`ensure_ascii=False` 参数确保中文字符能正常保存，而不是被转换成 `\uXXXX` 形式的Unicode转义序列。

### 读取JSON文件

**代码示例：从JSON文件加载数据**
```python
import json

with open('config.json', 'r', encoding='utf-8') as f:
    loaded_data = json.load(f)

print(loaded_data)
# 输出: {'ma5': 100, 'ma20': 200, 'position': 30}
```
`json.load(fp)` 从文件对象 `f` 中读取数据，并自动将其解析为Python字典。

### 封装实用的JSON工具函数

在实际项目中，我们经常需要封装一些工具函数来简化JSON的读写，并处理文件可能不存在的情况。

**代码示例：封装加载和保存JSON的函数**
```python
import json
from pathlib import Path

def load_json(file_path: Path) -> dict:
    """从指定路径加载JSON文件，返回字典。如果文件不存在，返回空字典。"""
    if file_path.is_file():
        with open(file_path, 'r', encoding='utf-8') as f:
            return json.load(f)
    else:
        return {}

def save_json(data: dict, file_path: Path) -> None:
    """将字典数据保存到指定路径的JSON文件中。"""
    with open(file_path, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=4, ensure_ascii=False)

# 使用示例
config_path = Path('my_config.json')
# 尝试加载配置，如果没有则得到空字典
my_config = load_json(config_path)
# 修改配置
my_config['last_update'] = '2023-10-27'
# 保存配置
save_json(my_config, config_path)
```
`load_json` 函数会检查文件是否存在，存在则读取，不存在则返回一个空字典，避免了程序因找不到文件而崩溃。`save_json` 函数使用 `'w'` 模式，如果文件不存在会自动创建，存在则会覆盖。这两个函数构成了一个简单可靠的数据持久化方案。在许多量化框架（如vn.py）的工具函数中，也能找到类似实现。

## 总结

![](img/dc7f4e8fccba17134af03d1f70bfc706_11.png)

本节课中我们一起学习了Python文件操作的核心知识。我们首先了解了文件读写的基本流程：打开、操作、关闭，并强调了使用 `with` 语句来自动管理文件上下文是最佳实践。接着，我们详细探讨了不同的文件打开模式（`r`, `w`, `a`等）及其适用场景。最后，我们重点学习了如何使用 `json` 模块来读写结构化的数据，这是量化交易中保存参数、配置和状态的常用方法。掌握这些技能，你就能轻松地将程序运行中的数据持久化保存到本地，并在需要时重新加载，为构建更复杂的应用程序打下坚实基础。