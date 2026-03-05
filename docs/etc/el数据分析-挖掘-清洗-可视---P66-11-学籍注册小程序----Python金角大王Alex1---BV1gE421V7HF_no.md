# Python数据分析实战：P66：11 学籍注册小程序-手机&身份证合法性验证 📱

在本节课中，我们将学习如何为学籍注册程序添加手机号和身份证号的唯一性验证，并扩展其合法性验证功能。我们将通过加载现有数据到内存、提取关键列并进行比对来实现唯一性检查，同时学习如何编写独立的验证函数来确保输入数据的格式正确。

---

## 数据加载与提取

上一节我们完成了注册程序的基本框架，本节中我们来看看如何确保用户输入的手机号和身份证号在系统中是唯一的。实现思路是：程序启动时，将存储已有账户数据的文件加载到内存中，并提取出手机号和身份证号两列，分别存储为列表。当用户输入信息时，只需检查输入值是否已存在于对应的列表中即可。

以下是实现数据加载功能的步骤：

![](img/3693219b052ddf70bcbdeb1e69af671d_1.png)

1.  定义一个名为 `load_validated_data` 的函数，它接收文件名作为参数。
2.  在函数内部，以读取模式打开指定文件。
3.  初始化两个空列表：`phone_list` 用于存放手机号，`id_number_list` 用于存放身份证号。
4.  循环读取文件的每一行。
5.  对每一行字符串，使用 `split(‘,’)` 方法按逗号分隔，将其转换为列表。根据列索引（第2列是手机号，第3列是身份证号）获取对应数据。
6.  将获取到的手机号和身份证号分别添加到步骤3创建的两个列表中。
7.  函数执行完毕后，返回这两个列表。

![](img/3693219b052ddf70bcbdeb1e69af671d_3.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_5.png)

对应的核心代码如下：

```python
def load_validated_data(file_name):
    phone_list = []
    id_number_list = []
    with open(file_name, ‘r’) as f:
        for line in f:
            data = line.strip().split(‘,’)
            phone = data[2]  # 假设手机号在第2列
            id_number = data[3]  # 假设身份证号在第3列
            phone_list.append(phone)
            id_number_list.append(id_number)
    return phone_list, id_number_list
```

![](img/3693219b052ddf70bcbdeb1e69af671d_7.png)

---

![](img/3693219b052ddf70bcbdeb1e69af671d_9.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_11.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_13.png)

## 调用与唯一性验证

![](img/3693219b052ddf70bcbdeb1e69af671d_15.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_17.png)

在程序的主逻辑开始前，我们需要调用上述函数来获取已注册的手机号和身份证号列表。Python支持多变量同时赋值，这可以让代码更简洁。

![](img/3693219b052ddf70bcbdeb1e69af671d_19.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_21.png)

在主程序中，我们可以这样调用：

![](img/3693219b052ddf70bcbdeb1e69af671d_23.png)

```python
# 同时获取两个返回的列表
phone_list, id_number_list = load_validated_data(‘student_data.csv’)
```

![](img/3693219b052ddf70bcbdeb1e69af671d_25.png)

获取到列表后，就可以在用户注册的函数（例如 `register_api`）中进行唯一性验证了。验证逻辑非常简单：使用 `in` 关键字判断用户输入是否已存在于对应的列表中。

![](img/3693219b052ddf70bcbdeb1e69af671d_27.png)

以下是验证逻辑的代码示例：

![](img/3693219b052ddf70bcbdeb1e69af671d_29.png)

```python
def register_api():
    # ... 获取用户输入的 phone 和 id_number ...
    if phone in phone_list:
        print(“该手机号已注册”)
        return  # 退出注册流程
    if id_number in id_number_list:
        print(“该身份证号已注册”)
        return  # 退出注册流程
    # ... 后续的注册逻辑 ...
```

![](img/3693219b052ddf70bcbdeb1e69af671d_31.png)

通过这样的检查，当用户输入一个已注册的手机号或身份证号时，程序会给出明确提示并中断注册过程，从而保证了数据的唯一性。

---

![](img/3693219b052ddf70bcbdeb1e69af671d_33.png)

## 扩展：字段合法性验证

![](img/3693219b052ddf70bcbdeb1e69af671d_35.png)

除了唯一性，实际应用中还需要验证输入数据的格式是否合法。例如，手机号必须是11位数字，身份证号有固定的长度和格式规则。我们可以为每个需要复杂验证的字段编写独立的验证函数，使代码结构更清晰、更易于维护。

以下是一个验证手机号合法性的函数示例：

```python
def validate_phone(number):
    # 验证1：是否全为数字
    if not number.isdigit():
        print(“手机号必须是数字”)
        return False
    # 验证2：长度是否为11位
    if len(number) != 11:
        print(“手机号必须是11位”)
        return False
    # 验证3：可以添加更多规则，例如是否以特定号段开头
    # if not number.startswith(‘13’):
    #     print(“手机号格式不正确”)
    #     return False
    # 所有验证通过
    return True
```

在注册流程中，可以在唯一性检查之前或之后调用这个验证函数：

![](img/3693219b052ddf70bcbdeb1e69af671d_37.png)

```python
def register_api():
    # ... 获取用户输入的 phone ...
    if not validate_phone(phone):
        return  # 验证不通过，退出
    # ... 继续进行唯一性验证和后续逻辑 ...
```

![](img/3693219b052ddf70bcbdeb1e69af671d_39.png)

同样的思路可以应用于身份证号、邮箱地址等其他字段的验证。将验证逻辑模块化，是编写高质量、可维护代码的常见做法。

![](img/3693219b052ddf70bcbdeb1e69af671d_41.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_43.png)

---

![](img/3693219b052ddf70bcbdeb1e69af671d_45.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_47.png)

## 总结

![](img/3693219b052ddf70bcbdeb1e69af671d_49.png)

![](img/3693219b052ddf70bcbdeb1e69af671d_51.png)

本节课中我们一起学习了如何为学籍注册程序增强数据验证功能。我们首先实现了通过加载数据到内存并进行列表比对，来保证手机号和身份证号的唯一性。接着，我们进一步探讨了如何通过编写独立的验证函数，来确保用户输入数据的格式合法性，例如检查手机号是否为11位数字。这些方法不仅适用于当前项目，也是未来开发中处理用户输入验证的通用且重要的技巧。通过将功能分解为独立的模块，可以使代码逻辑更清晰，更易于调试和扩展。