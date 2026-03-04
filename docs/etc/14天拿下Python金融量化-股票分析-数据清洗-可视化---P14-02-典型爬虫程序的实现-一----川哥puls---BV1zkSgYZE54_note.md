# Python金融量化：P14：02 典型爬虫程序的实现（一） 🕷️

![](img/72056fc12e4b4b9ec432e19d7ecc3348_0.png)

在本节课中，我们将要学习如何实现一个典型的网络爬虫程序。我们将从爬虫的基本概念和工作原理入手，逐步学习如何发起网络请求、解析网页内容，并最终将获取到的数据进行存储和分析。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_2.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_4.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_6.png)

## 什么是爬虫？🤔

![](img/72056fc12e4b4b9ec432e19d7ecc3348_8.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_10.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_12.png)

爬虫首先是一段程序，它的作用是为我们获取数据。爬虫模仿了我们在浏览器获取数据的过程。

我们如何在浏览器上获取数据呢？首先，我们输入网址，然后对网站发送一个HTTP请求。网站所在的服务器接收到HTTP请求后，会将所请求的数据发送回来。这些数据经过浏览器的渲染以后，就会以我们看到的样子呈现在眼前。这是一个完整的请求和响应的过程。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_14.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_16.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_18.png)

整个页面都是我们想要的数据吗？不一定。例如，如果我们只想要一张图片，最简单的方法是右键点击图片，选择“图片另存为”存到本地，这样就获取到了想要的数据。

## 爬虫程序的实现步骤 📝

利用Python爬虫来爬取静态网页，简单来说可以分为四个步骤。在介绍步骤之前，我们先来看一个概念：静态网页。

什么是静态网页呢？静态网页是相对于动态页面而言的。这里的“动态”并不是指页面会动，而是指页面存在与数据库的交互。静态网页的数据和代码都已经写在了网页上，每次请求时得到的结果都是一样的。我们之前看到的网页上滑动的图片，是为了使网页更美观而加入的动态效果，它本身不影响网页是静态还是动态。我们后续的操作步骤都是基于静态网页来实现的。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_20.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_22.png)

以下是爬虫程序的四个核心步骤：

### 1. 发起请求

通过HTTP库（例如 `requests` 和 `urllib` 等库）向目标站点发起一个请求。这个操作就像我们输入网址并按下回车一样，它会向服务器发送一个 `request` 请求。这个请求可以包含额外的 `header` 等信息。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_24.png)

什么是 `header` 信息呢？我们可以使用 `F12` 打开浏览器的控制台，刷新网页后，在 `Network` 标签下查看任意一个请求。在 `Request Headers` 中，可以看到 `header` 信息。以下是两个常用字段：
*   **User-Agent**：用户代理，告诉服务器用户的操作系统和浏览器版本信息。
*   **Cookie**：告诉服务器用户的身份，用于帮助网站识别用户。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_26.png)

在使用浏览器请求时，浏览器会自动添加这些字段。而在编写爬虫程序时，则需要手动添加，以将程序伪装成浏览器，避免被服务器的反爬机制识别和封禁。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_28.png)

### 2. 获取响应内容

正常访问浏览器后，浏览器会回应一个响应，即 `response`。`response` 中存储着我们想要获取的页面内容，其类型可能是 `HTML`、`JSON` 字符串、图片、视频或音频等文件。

### 3. 解析内容

获取到响应内容（通常是 `HTML` 代码）后，我们需要从中提取出我们想要的具体数据（如链接、文本、图片地址等）。解析内容的方法有很多，例如：
*   正则表达式
*   `BeautifulSoup` 等页面解析库

### 4. 保存数据

获取并解析数据之后，需要进行保存。保存数据的方式有很多：
*   如果数据量较大，可以存储到数据库（如 `MySQL`、`Oracle`）。
*   如果数据量较小，可以存储到本地文件（如 `TXT` 文本文件、`CSV` 文件、`Excel` 文件，或直接保存图片、视频等）。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_30.png)

## 解析网页内容：正则表达式 🔍

![](img/72056fc12e4b4b9ec432e19d7ecc3348_32.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_34.png)

上一节我们介绍了爬虫程序的四个步骤，本节中我们来看看如何解析爬取到的网页内容。这部分内容对应爬虫实现过程中的第三个步骤：解析内容。我们将重点介绍正则表达式。

正则表达式是记录文本规则的一段代码。它通过预先设定好的一些特殊字符和符号进行组合，形成规则字符串。然后用这个规则字符串对一大串文本进行过滤，最终获得符合规则的字符串。例如，我们可以用它来过滤一段文本中的邮箱、图片地址或手机号码。除了文本匹配功能，正则表达式还具备文本替换、匹配验证等功能，在爬虫领域应用非常广泛。

正则表达式在Python中通过 `re` 模块使用。`re` 模块是Python的标准库之一，无需安装，可以直接导入使用。

以下是 `re` 模块中几种常用方法的介绍：

*   **`re.match()`**：从字符串的**开头**开始匹配。如果开头匹配成功，则返回匹配对象；如果开头不匹配，则直接返回 `None`，即使字符串后面有符合规则的内容也不会再匹配。
*   **`re.search()`**：扫描整个字符串，返回**第一个**成功的匹配。如果开头不匹配，它会继续向下查找，直到找到匹配项或字符串结束。
*   **`re.findall()`**：扫描整个字符串，以**列表**形式返回**所有**成功的匹配。
*   **`re.compile()`**：将正则表达式字符串编译成一个 `Pattern` 对象，可以供 `match()`、`search()`、`findall()` 等方法重复使用，提高效率。

下面是一个使用正则表达式匹配图片链接的示例：

```python
import re

# 假设这是从网页获取的HTML文本
html_text = ‘...<img src=“https://example.com/image1.jpg”>...<img src=“https://example.com/pic2.png”>...‘

# 编写正则表达式，匹配图片链接
# 解释：匹配以 `src=“` 开头，后面跟着任意字符（非贪婪模式），直到遇到 `“` 结束的字符串
pattern = re.compile(r‘src=“(.+?)“’)

# 使用 findall 方法找到所有匹配项
image_urls = pattern.findall(html_text)
print(image_urls)  # 输出: [‘https://example.com/image1.jpg‘， ‘https://example.com/pic2.png‘]
```

## 数据处理与分析：Pandas模块 🐼

上一节我们学习了如何使用正则表达式从网页中提取数据。本节中，我们将介绍一个强大的数据处理工具——Pandas模块，它可以帮助我们高效地存储和分析爬取到的数据。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_36.png)

Pandas是Python的一个数据处理和分析库。它提供了高效操作大型数据集的数据结构（如 `DataFrame`）和大量快速处理数据的方法与函数。此外，它还可以方便地读写本地文件，如 `CSV` 文件和 `Excel` 文件。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_38.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_40.png)

Pandas不是Python的内置标准库，需要使用 `pip install pandas` 命令安装。如果你使用的是Anaconda环境，则已自带Pandas。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_42.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_44.png)

以下是使用Pandas进行文件读写和简单数据分析的示例：

![](img/72056fc12e4b4b9ec432e19d7ecc3348_46.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_48.png)

```python
import pandas as pd

# 1. 读取Excel文件
# 假设有一个名为 ‘students.xlsx‘ 的文件，其中有一个名为 ‘Sheet1‘ 的工作表
df = pd.read_excel(‘students.xlsx‘， sheet_name=‘Sheet1‘)
print(“读取的数据前5行：“)
print(df.head())  # 默认显示前5行数据

# 2. 数据写入新的Excel文件
df.to_excel(‘new_students.xlsx‘， sheet_name=‘NewSheet‘， index=False)  # index=False表示不写入行索引

# 3. 简单的数据分析：按‘班级’分组并统计人数
if ‘班级‘ in df.columns:
    class_count = df.groupby(‘班级‘).size()
    print(“\n各班人数统计：“)
    print(class_count)
```

## 实战：百度贴吧图片爬虫 🖼️

![](img/72056fc12e4b4b9ec432e19d7ecc3348_50.png)

现在，让我们综合运用前面所学的知识，来构建一个完整的图片爬虫程序。我们将以爬取百度贴吧某个页面的图片为例。

以下是实现该爬虫的核心步骤和代码框架：

![](img/72056fc12e4b4b9ec432e19d7ecc3348_52.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_54.png)

```python
import requests
import re
import os

![](img/72056fc12e4b4b9ec432e19d7ecc3348_56.png)

def get_page_html(url):
    """步骤1 & 2：发送请求并获取网页HTML内容"""
    response = requests.get(url)
    # 可以在这里添加headers以更好地伪装，但百度贴吧对此要求不严格
    # headers = {‘User-Agent‘: ‘...‘}
    # response = requests.get(url， headers=headers)
    return response.text

def parse_image_urls(html):
    """步骤3：使用正则表达式解析图片链接"""
    # 根据目标网页的HTML结构编写正则表达式
    # 示例模式，实际需要根据具体网页调整
    pattern = re.compile(r‘<img.*?src=“(https?://.*?\.(?:jpg|png|gif))“. *?>‘， re.S)
    image_urls = pattern.findall(html)
    return image_urls

![](img/72056fc12e4b4b9ec432e19d7ecc3348_58.png)

def download_image(img_url， save_path):
    """下载单张图片并保存"""
    try:
        img_data = requests.get(img_url).content
        with open(save_path， ‘wb‘) as f:
            f.write(img_data)
        print(f“图片已保存： {save_path}“)
    except Exception as e:
        print(f“下载失败 {img_url}: {e}“)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_60.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_62.png)

def main():
    target_url = ‘https://tieba.baidu.com/p/xxxxxxx‘  # 替换为目标贴吧帖子地址
    save_dir = ‘./tieba_images‘
    os.makedirs(save_dir， exist_ok=True)  # 创建保存目录

    print(“正在获取网页内容...“)
    html = get_page_html(target_url)

    print(“正在解析图片链接...“)
    img_urls = parse_image_urls(html)
    print(f“找到 {len(img_urls)} 张图片。“)

    print(“开始下载图片...“)
    for i， url in enumerate(img_urls):
        # 生成文件名，例如 image_0.jpg
        file_name = f“image_{i}.{url.split(‘.‘)[-1]}“
        file_path = os.path.join(save_dir， file_name)
        download_image(url， file_path)

    print(“所有图片下载完成！“)

if __name__ == ‘__main__‘:
    main()
```

![](img/72056fc12e4b4b9ec432e19d7ecc3348_64.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_66.png)

**注意**：实际使用时，需要根据目标网页（百度贴吧）实际的HTML结构来调整 `parse_image_urls` 函数中的正则表达式模式。可以通过浏览器的开发者工具（F12）查看图片标签的具体格式。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_68.png)

## 总结 📚

![](img/72056fc12e4b4b9ec432e19d7ecc3348_70.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_72.png)

本节课中，我们一起学习了典型爬虫程序的实现。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_74.png)

1.  我们首先理解了爬虫是模拟浏览器进行请求和响应的程序。
2.  然后，我们拆解了爬虫的四个核心步骤：发起请求、获取响应、解析内容和保存数据。
3.  接着，我们深入学习了使用正则表达式这一强大工具来解析复杂的HTML文本，提取目标数据。
4.  最后，我们介绍了Pandas模块，它可以帮助我们将爬取到的结构化数据进行高效的存储、处理和分析，并为后续的数据可视化或量化分析打下基础。

![](img/72056fc12e4b4b9ec432e19d7ecc3348_76.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_77.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_78.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_79.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_80.png)

![](img/72056fc12e4b4b9ec432e19d7ecc3348_81.png)

通过结合 `requests`、`re` 和 `pandas` 这些工具，你已经掌握了构建一个基础但功能完整的网络爬虫所需的核心技能。