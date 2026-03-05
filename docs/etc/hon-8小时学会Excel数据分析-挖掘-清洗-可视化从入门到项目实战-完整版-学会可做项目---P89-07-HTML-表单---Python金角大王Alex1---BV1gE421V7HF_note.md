# Python数据分析与可视化：07：HTML表单入门

![](img/e9bd001365cf23108f46f5fad6771d93_0.png)

在本节课中，我们将学习HTML表单的基础知识。表单是网页中用于收集用户输入信息的关键组件，例如注册、登录、搜索等交互功能都依赖于表单。我们将了解表单的构成、常用输入元素的类型及其属性。

![](img/e9bd001365cf23108f46f5fad6771d93_2.png)

## 表单的基本结构

![](img/e9bd001365cf23108f46f5fad6771d93_4.png)

HTML表单使用 `<form>` 标签定义，它像一个容器，包裹着各种输入元素。一个基本的表单结构如下：

```html
<form action="提交地址" method="提交方法">
    <!-- 各种输入元素放在这里 -->
</form>
```

*   **`action`** 属性指定表单数据提交到哪个服务器地址。
*   **`method`** 属性定义数据提交的方式，常见的有 `GET` 和 `POST`。

## 常用输入元素类型

以下是构成表单的核心输入元素，我们将逐一介绍。

### 文本框与密码框

文本框用于输入单行文本，密码框则用于输入密码，输入内容会以圆点或星号隐藏。

*   **文本框**：使用 `<input type="text">`。
*   **密码框**：使用 `<input type="password">`。

![](img/e9bd001365cf23108f46f5fad6771d93_6.png)

![](img/e9bd001365cf23108f46f5fad6771d93_8.png)

```html
用户名: <input type="text" name="username">
密码: <input type="password" name="password">
```

### 数字与日期输入框

HTML5提供了更具体的输入类型，以改善用户体验和数据验证。

![](img/e9bd001365cf23108f46f5fad6771d93_10.png)

*   **数字框**：使用 `<input type="number">`，只能输入数字。
*   **日期选择器**：使用 `<input type="date">`，会弹出日期选择控件。

```html
年龄: <input type="number" name="age">
出生日期: <input type="date" name="birthday">
```

### 文本域

当需要输入多行文本时，例如个人简介，可以使用 `<textarea>` 标签。

```html
个人介绍:
<textarea name="intro" cols="50" rows="5" placeholder="请输入不少于50字的个人介绍"></textarea>
```

*   **`cols`** 和 **`rows`** 属性分别定义文本域的列宽和行高。
*   **`placeholder`** 属性提供输入提示，当用户开始输入时提示会消失。

### 单选框与复选框

单选框用于在多个选项中选择一个，复选框用于选择多个。

*   **单选框**：使用 `<input type="radio">`。**关键点**：同一组的单选框必须拥有相同的 `name` 属性值，才能实现互斥选择。
*   **复选框**：使用 `<input type="checkbox">`。

```html
性别:
<input type="radio" name="sex" value="male"> 男
<input type="radio" name="sex" value="female"> 女

爱好:
<input type="checkbox" name="hobby" value="girl"> 女孩
<input type="checkbox" name="hobby" value="python"> Python
```

*   **`value`** 属性定义了该选项被选中后，提交给服务器的值。
*   **`name`** 属性是服务器识别数据的“键名”。

### 下拉列表

下拉列表使用 `<select>` 和 `<option>` 标签创建，适合选项较多的情况。

```html
喜欢的类型:
<select name="girl_type">
    <option value="1">御姐型</option>
    <option value="2">小可爱</option>
    <option value="3">微胖型</option>
</select>
```

可以使用 `<optgroup>` 标签对选项进行分组。

```html
<select name="region">
    <optgroup label="区域">
        <option value="eu">欧美的</option>
        <option value="jk">日韩的</option>
        <option value="hn">河南开封的</option>
    </optgroup>
</select>
```

### 提交按钮

最后，需要一个按钮来触发表单数据的提交，使用 `<input type="submit">`。

![](img/e9bd001365cf23108f46f5fad6771d93_12.png)

![](img/e9bd001365cf23108f46f5fad6771d93_14.png)

```html
<input type="submit" value="注册">
```

*   **`value`** 属性定义了按钮上显示的文字。

## 表单数据如何提交

![](img/e9bd001365cf23108f46f5fad6771d93_16.png)

![](img/e9bd001365cf23108f46f5fad6771d93_18.png)

当用户点击提交按钮后，浏览器会将表单内所有具有 `name` 属性的输入元素的值进行收集，并按照 `action` 指定的地址和 `method` 指定的方式发送出去。

数据会以 `键=值` 的形式拼接，例如：`username=alex&password=123&sex=male`。服务器端程序（如Python的Flask或Django）可以通过这些“键”（即`name`属性的值）来获取对应的用户输入“值”。

![](img/e9bd001365cf23108f46f5fad6771d93_20.png)

![](img/e9bd001365cf23108f46f5fad6771d93_22.png)

## 表单分组与美化

为了使表单结构更清晰，可以使用 `<fieldset>` 和 `<legend>` 标签对相关字段进行分组。

```html
<fieldset>
    <legend>个人信息</legend>
    用户名: <input type="text" name="username"><br>
    密码: <input type="password" name="password">
</fieldset>
```

*   `<fieldset>` 会在表单元素周围创建一个边框。
*   `<legend>` 定义了该分组的标题。

## 练习任务

请尝试编写一个HTML表单，包含以下三个分组，并尽可能还原图示样式：
1.  **个人信息**：包含用户名、密码、年龄、出生日期字段。
2.  **详细资料**：包含个人介绍（文本域）、性别（单选框）、爱好（复选框）字段。
3.  **其他**：包含喜欢的类型（下拉列表）字段。
最后，在表单底部添加一个提交按钮。

![](img/e9bd001365cf23108f46f5fad6771d93_24.png)

## 总结

![](img/e9bd001365cf23108f46f5fad6771d93_26.png)

本节课我们一起学习了HTML表单的核心知识。我们认识了表单的基本结构 `<form>`，掌握了文本框、密码框、单选框、复选框、下拉列表等多种输入元素的创建方法，并理解了 `name` 和 `value` 属性在数据提交中的关键作用。此外，我们还了解了如何使用 `<fieldset>` 对表单进行分组美化。表单是Web数据交互的基石，熟练掌握其用法对后续学习Web开发和数据分析中的数据收集环节至关重要。