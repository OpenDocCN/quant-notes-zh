# Python数据分析与Web基础：P93：11 CSS常用属性 🎨

![](img/ae73de80aac1e455838709b6d636c606_1.png)

在本节课中，我们将学习CSS（层叠样式表）的一些常用属性。CSS属性数量庞大，对于后端开发人员而言，无需全部记忆，只需掌握核心部分并在需要时查阅即可。本节将重点介绍背景、边框、定位、字体等几个最常用且基础的属性，帮助你快速上手，为简单的Web页面添加样式。

---

## 背景设置

![](img/ae73de80aac1e455838709b6d636c606_3.png)

上一节我们介绍了CSS的基本概念，本节中我们来看看如何设置元素的背景。背景属性可以控制元素的颜色、图片等。

![](img/ae73de80aac1e455838709b6d636c606_5.png)

CSS的 `background` 是一个**复合属性**，这意味着它可以同时设置多个背景相关的值，例如颜色、图片、定位和重复方式，用空格分隔即可。

![](img/ae73de80aac1e455838709b6d636c606_7.png)

```css
background: #f0f0f0 url('image.jpg') no-repeat fixed center;
```

![](img/ae73de80aac1e455838709b6d636c606_9.png)

以下是几个关键的背景子属性：

*   **`background-color`**：仅用于设置背景颜色。颜色值可以是颜色名称、十六进制代码或RGB值。
    *   **公式/代码示例**：`background-color: lightblue;` 或 `background-color: #87CEEB;` 或 `background-color: rgb(135, 206, 235);`
*   **`background-image`**：用于设置背景图片。需要使用 `url()` 函数来指定图片路径。
    *   **公式/代码示例**：`background-image: url('path/to/image.png');`
*   **`background-size`**：用于控制背景图片的尺寸。可以设置具体的宽度和高度，或使用 `cover`、`contain` 等值。
    *   **公式/代码示例**：`background-size: 500px;` （设置宽度，高度自适应）或 `background-size: cover;` （覆盖整个区域）

> **过渡**：了解了如何为元素设置背景后，接下来我们看看如何定义元素的边框。

![](img/ae73de80aac1e455838709b6d636c606_11.png)

![](img/ae73de80aac1e455838709b6d636c606_13.png)

---

![](img/ae73de80aac1e455838709b6d636c606_15.png)

![](img/ae73de80aac1e455838709b6d636c606_17.png)

![](img/ae73de80aac1e455838709b6d636c606_19.png)

## 边框与轮廓

![](img/ae73de80aac1e455838709b6d636c606_21.png)

边框属性用于设置元素周围的边界线。`border` 同样是一个复合属性，可以一次性设置边框的宽度、样式和颜色。

![](img/ae73de80aac1e455838709b6d636c606_23.png)

![](img/ae73de80aac1e455838709b6d636c606_25.png)

```css
border: 2px solid black;
```

![](img/ae73de80aac1e455838709b6d636c606_27.png)

以下是边框相关的常用属性：

![](img/ae73de80aac1e455838709b6d636c606_29.png)

![](img/ae73de80aac1e455838709b6d636c606_31.png)

*   **边框样式**：通过 `border-style` 或 `border` 复合属性中的样式值来定义，例如 `solid`（实线）、`dashed`（虚线）、`dotted`（点线）。
    *   **公式/代码示例**：`border: 3px dashed red;`
*   **单边边框**：可以为元素的任一边单独设置边框，如 `border-bottom`、`border-top` 等。
    *   **公式/代码示例**：`border-bottom: 1px solid #ccc;`
*   **边框圆角**：使用 `border-radius` 属性可以让边框的角变得圆润。
    *   **公式/代码示例**：`border-radius: 10px;`
*   **盒子阴影**：使用 `box-shadow` 属性可以为元素添加阴影效果，增加立体感。其参数通常包括水平偏移、垂直偏移、模糊半径和颜色。
    *   **公式/代码示例**：`box-shadow: 5px 5px 10px rgba(0,0,0,0.3);`

![](img/ae73de80aac1e455838709b6d636c606_33.png)

> **过渡**：设置好边框后，元素在页面中的位置和与其他元素的间距同样重要。这涉及到内边距、外边距和定位。

![](img/ae73de80aac1e455838709b6d636c606_35.png)

![](img/ae73de80aac1e455838709b6d636c606_37.png)

---

## 内边距、外边距与定位

![](img/ae73de80aac1e455838709b6d636c606_39.png)

![](img/ae73de80aac1e455838709b6d636c606_41.png)

我们已经接触过 `margin`（外边距）和 `padding`（内边距），它们分别控制元素外部和内部的空白区域。本节重点介绍 **定位（Positioning）**。

![](img/ae73de80aac1e455838709b6d636c606_43.png)

![](img/ae73de80aac1e455838709b6d636c606_45.png)

![](img/ae73de80aac1e455838709b6d636c606_47.png)

定位属性 `position` 决定了元素在文档中的定位方式，是CSS布局的核心概念之一。

![](img/ae73de80aac1e455838709b6d636c606_49.png)

![](img/ae73de80aac1e455838709b6d636c606_51.png)

![](img/ae73de80aac1e455838709b6d636c606_53.png)

以下是主要的定位方式：

![](img/ae73de80aac1e455838709b6d636c606_55.png)

![](img/ae73de80aac1e455838709b6d636c606_57.png)

*   **`static`（静态定位）**：默认值。元素遵循正常的文档流。
    *   **公式/代码示例**：`position: static;`
*   **`relative`（相对定位）**：元素相对于其正常位置进行偏移。使用 `top`、`right`、`bottom`、`left` 属性来调整位置。
    *   **公式/代码示例**：`position: relative; top: 10px; left: 20px;`
*   **`absolute`（绝对定位）**：元素相对于最近的非 `static` 定位的祖先元素进行定位。如果没有，则相对于初始包含块（通常是浏览器窗口）。
    *   **公式/代码示例**：`position: absolute; top: 0; right: 0;`
*   **`fixed`（固定定位）**：元素相对于浏览器窗口进行定位，即使页面滚动，它也会停留在固定位置。
    *   **公式/代码示例**：`position: fixed; bottom: 20px; right: 20px;`

![](img/ae73de80aac1e455838709b6d636c606_59.png)

> **过渡**：掌握了元素的位置控制，最后我们来看看如何美化元素内部的文字内容。

![](img/ae73de80aac1e455838709b6d636c606_61.png)

![](img/ae73de80aac1e455838709b6d636c606_63.png)

---

![](img/ae73de80aac1e455838709b6d636c606_65.png)

![](img/ae73de80aac1e455838709b6d636c606_67.png)

## 字体与文本

![](img/ae73de80aac1e455838709b6d636c606_69.png)

![](img/ae73de80aac1e455838709b6d636c606_71.png)

字体和文本属性用于控制元素内文字的外观，包括字体家族、大小、粗细、对齐方式等。

![](img/ae73de80aac1e455838709b6d636c606_73.png)

![](img/ae73de80aac1e455838709b6d636c606_75.png)

![](img/ae73de80aac1e455838709b6d636c606_77.png)

以下是常用的字体和文本属性：

![](img/ae73de80aac1e455838709b6d636c606_79.png)

*   **`font-family`**：设置字体家族，可以指定多个字体作为备选。
    *   **公式/代码示例**：`font-family: Arial, sans-serif;`
*   **`font-size`**：设置字体大小。
    *   **公式/代码示例**：`font-size: 16px;`
*   **`font-weight`**：设置字体粗细，如 `normal`（正常）、`bold`（加粗）。
    *   **公式/代码示例**：`font-weight: bold;`
*   **`text-align`**：设置文本的水平对齐方式，如 `left`（左对齐）、`center`（居中）、`right`（右对齐）。
    *   **公式/代码示例**：`text-align: center;`
*   **`color`**：设置文本颜色。
    *   **公式/代码示例**：`color: #333;`

![](img/ae73de80aac1e455838709b6d636c606_81.png)

![](img/ae73de80aac1e455838709b6d636c606_83.png)

---

![](img/ae73de80aac1e455838709b6d636c606_85.png)

![](img/ae73de80aac1e455838709b6d636c606_87.png)

![](img/ae73de80aac1e455838709b6d636c606_89.png)

## 总结

![](img/ae73de80aac1e455838709b6d636c606_91.png)

![](img/ae73de80aac1e455838709b6d636c606_93.png)

本节课中我们一起学习了CSS的核心常用属性。我们首先了解了如何用**复合属性** `background` 设置背景，然后学习了**边框**的样式、圆角和阴影效果。接着，我们回顾了 `margin` 和 `padding`，并深入探讨了 `position` 属性的几种**定位方式**，这是控制页面布局的关键。最后，我们学习了如何通过**字体和文本属性**来美化文字内容。

![](img/ae73de80aac1e455838709b6d636c606_95.png)

记住，CSS属性繁多，无需死记硬背。掌握这些基础属性后，你已能完成许多常见的样式设置。在实际开发中，遇到更复杂的需求时，善于查阅文档（如菜鸟教程等在线资源）是最高效的方法。