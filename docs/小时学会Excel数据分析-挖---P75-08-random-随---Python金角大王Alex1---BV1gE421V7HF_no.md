# Python数据分析入门：P75：08 random 随机数模块 🎲

![](img/87baf51a118adf75d37fb6c0474712a4_1.png)

在本节课中，我们将要学习Python中用于生成随机数的`random`模块。这个模块在数据分析、模拟测试和游戏开发中非常有用。我们将回顾其核心功能，并通过简单的例子帮助初学者理解。

## 回顾random模块

上一节我们介绍了Python的基础知识，本节中我们来看看`random`模块的具体用法。它主要用于生成各种随机数。

以下是`random`模块中一些常用方法的介绍：

![](img/87baf51a118adf75d37fb6c0474712a4_3.png)

*   **`random.randrange(start, stop, step)`**：返回一个在指定范围内、按指定步长递增的随机整数。例如，`random.randrange(0, 10, 2)`会从`[0, 2, 4, 6, 8]`中随机选择一个数。
*   **`random.randint(a, b)`**：返回一个在`[a, b]`区间内的随机整数，**包括**两端的值。例如，`random.randint(1, 10)`可能返回1到10之间的任何整数。
*   **`random.random()`**：返回一个在`[0.0, 1.0)`区间内的随机浮点数。公式表示为：`0.0 <= 返回值 < 1.0`。
*   **`random.choice(sequence)`**：从一个非空序列（如列表、元组、字符串）中随机选择一个元素。
*   **`random.sample(population, k)`**：从指定的序列或集合中，随机选择`k`个**不重复**的元素。
*   **`random.shuffle(x)`**：将序列`x`中的元素顺序随机打乱（“洗牌”）。注意，这会直接修改原序列。

## 应用示例

![](img/87baf51a118adf75d37fb6c0474712a4_5.png)

理解了核心方法后，我们来看看它们在实际场景中的应用。

以下是几个使用`random`模块的代码示例：

```python
import random
import string

# 1. 生成随机整数
random_int = random.randint(1, 100)
print(f"1到100之间的随机整数：{random_int}")

# 2. 生成随机浮点数
random_float = random.random()
print(f"0到1之间的随机浮点数：{random_float}")

# 3. 从列表中随机选择
fruits = ['苹果', '香蕉', '橙子', '葡萄']
chosen_fruit = random.choice(fruits)
print(f"随机选择的水果：{chosen_fruit}")

# 4. 生成随机密码（由数字和小写字母组成）
# 先定义字符池
characters = string.digits + string.ascii_lowercase
# 随机选取6个字符并拼接
random_password = ''.join(random.sample(characters, 6))
print(f"生成的随机密码：{random_password}")

# 5. 洗牌（打乱列表顺序）
card_list = ['红桃A', '方块K', '黑桃Q', '梅花J']
random.shuffle(card_list)
print(f"洗牌后的列表：{card_list}")
```

## 关于序列化模块的说明

在结束`random`模块的学习后，课程中原本计划介绍序列化模块（如`pickle`）。但考虑到本集训营的核心目标是让大家快速掌握数据分析的必备技能，而序列化在基础阶段使用频率较低，为了保持课程紧凑高效，我们决定暂时跳过这部分内容。

> 所有相关课件和视频材料均已备好。建议你在完成本次集训营的所有核心内容后，若仍有兴趣和精力，再自行回看学习。

![](img/87baf51a118adf75d37fb6c0474712a4_7.png)

本节课中我们一起学习了Python `random`模块的核心功能，包括生成随机整数、浮点数、从序列中随机选择元素以及打乱序列顺序。掌握这些方法，你就能在数据分析、模拟或小游戏中轻松引入随机性。接下来，我们将进入一个更重要的实战环节：学习如何使用Python处理Excel文件。