# Python文件操作实战：P52：11 开发全局文本检索替换脚本 🔍

![](img/5ea7e8eeff58afa1fcdf3ccef5688cc6_1.png)

在本节课中，我们将学习如何开发一个实用的Python脚本，用于对指定文件内容进行全局的检索和替换。我们将重点掌握如何通过命令行向脚本传递参数，并完成文件的读取、内容替换和结果统计。

---

上一节我们介绍了文件的基本读写操作，本节中我们来看看如何将这些操作整合成一个实用的工具。

## 读取外部指令

![](img/5ea7e8eeff58afa1fcdf3ccef5688cc6_3.png)

要实现通过命令行调用脚本并传递参数，我们需要借助Python的 `sys` 模块。这个模块可以让我们获取到脚本执行时传入的所有参数。

```python
import sys
# sys.argv 是一个列表，包含了所有命令行参数
# 其中 sys.argv[0] 是脚本自身的文件名
arguments = sys.argv
```

以下是 `sys.argv` 列表的构成：
*   `sys.argv[0]`：脚本自身的文件名和路径。
*   `sys.argv[1]`：传入的第一个参数。
*   `sys.argv[2]`：传入的第二个参数，依此类推。

## 脚本逻辑设计

![](img/5ea7e8eeff58afa1fcdf3ccef5688cc6_5.png)

![](img/5ea7e8eeff58afa1fcdf3ccef5688cc6_7.png)

理解了如何获取参数后，我们就可以设计脚本的核心逻辑了。整个过程可以分为清晰的四个步骤。

以下是实现全局替换的步骤：
1.  **读取文件内容到内存**：打开目标文件，将其全部内容读取到一个字符串变量中。
2.  **统计并替换内容**：在内存中统计旧字符串出现的次数，并使用 `replace` 方法进行全局替换。
3.  **清空原文件**：将文件指针移动到开头，并清空文件原有内容。
4.  **写入新数据**：将替换后的新内容写回文件。

## 代码实现

现在，我们将上述逻辑转化为具体的Python代码。

```python
import sys

# 1. 从命令行参数中获取信息
old_str = sys.argv[1]  # 要替换的旧字符串
new_str = sys.argv[2]  # 替换后的新字符串
file_name = sys.argv[3] # 目标文件名

# 2. 打开文件并读取内容到内存
with open(file_name, 'r+') as f:
    data = f.read()

    # 3. 统计旧字符串出现的次数并进行替换
    old_str_count = data.count(old_str)
    new_data = data.replace(old_str, new_str)

    # 4. 清空原文件并写入新数据
    f.seek(0)
    f.truncate()
    f.write(new_data)

# 5. 输出替换结果
print(f'成功将字符“{old_str}”替换为“{new_str}”，共 {old_str_count} 处。')
```

## 运行示例

脚本编写完成后，我们可以在命令行（终端）中运行它。

假设我们的脚本名为 `file_replace.py`，目标文件是 `contacts.txt`，我们想将其中的“马千羽”替换为“黑姑娘”。

运行命令如下：
```bash
python file_replace.py 马千羽 黑姑娘 contacts.txt
```
执行后，程序会输出类似“成功将字符‘马千羽’替换为‘黑姑娘’，共 1 处。”的结果，并完成文件内容的修改。

---

![](img/5ea7e8eeff58afa1fcdf3ccef5688cc6_9.png)

本节课中我们一起学习了如何开发一个全局文本检索替换脚本。我们掌握了使用 `sys.argv` 读取命令行参数的方法，并实践了“读取-处理-回写”的文件修改流程。这个脚本虽然简单，但结合了参数传递、文件操作和字符串处理等多个核心知识点，是一个非常实用的综合练习。