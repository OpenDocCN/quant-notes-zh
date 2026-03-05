# Python金融量化：P17：05 爬虫案例详解 🕷️

![](img/8cb08ae31193d57b367add58bc6536da_0.png)

在本节课中，我们将通过一个具体的爬虫案例，综合运用之前学过的知识，学习如何爬取一个具有多级目录结构的网站数据。我们将重点理解并实践广度优先的爬取策略，并完成从发送请求到数据提取的全过程。

## 回顾爬虫核心步骤

![](img/8cb08ae31193d57b367add58bc6536da_2.png)

![](img/8cb08ae31193d57b367add58bc6536da_4.png)

![](img/8cb08ae31193d57b367add58bc6536da_6.png)

![](img/8cb08ae31193d57b367add58bc6536da_8.png)

在开始新案例之前，我们先来系统地回顾一下实现爬虫的四个基本步骤，这有助于我们理解本次案例的实现逻辑。

1.  **发送请求**：这是爬虫程序的第一步，即向目标服务器发送请求。我们主要使用 `requests` 模块的 `get` 方法，并通常会带上 `headers` 参数（特别是 `User-Agent`）来模拟浏览器访问。
    ```python
    import requests
    response = requests.get(url, headers=headers)
    ```

2.  **获取响应**：服务器会返回一个 `Response` 对象。我们常用的属性有：
    *   `response.status_code`：状态码（如200表示成功）。
    *   `response.text`：获取HTML页面的文本内容。
    *   `response.content`：获取二进制数据（如下载图片时使用）。

![](img/8cb08ae31193d57b367add58bc6536da_10.png)

3.  **数据提取**：从响应的HTML中提取所需数据。我们介绍过多种工具：
    *   正则表达式：功能强大但编写复杂。
    *   Beautiful Soup：使用简单方便，是我们重点学习的工具。
    *   lxml 和 XPath：效率高，适合处理复杂结构。
    在实际项目中，可以根据场景和个人喜好混合使用这些工具。

![](img/8cb08ae31193d57b367add58bc6536da_12.png)

![](img/8cb08ae31193d57b367add58bc6536da_14.png)

![](img/8cb08ae31193d57b367add58bc6536da_16.png)

4.  **数据存储**：将提取的数据保存下来。简单的方式是写入本地TXT文件，更结构化的方式可以使用 `pandas` 保存为Excel或CSV文件，对于大量数据则推荐使用数据库。

![](img/8cb08ae31193d57b367add58bc6536da_18.png)

![](img/8cb08ae31193d57b367add58bc6536da_20.png)

![](img/8cb08ae31193d57b367add58bc6536da_22.png)

![](img/8cb08ae31193d57b367add58bc6536da_24.png)

## 案例场景与策略选择

![](img/8cb08ae31193d57b367add58bc6536da_26.png)

![](img/8cb08ae31193d57b367add58bc6536da_28.png)

![](img/8cb08ae31193d57b367add58bc6536da_30.png)

![](img/8cb08ae31193d57b367add58bc6536da_32.png)

![](img/8cb08ae31193d57b367add58bc6536da_34.png)

本节我们要爬取的网站是一个包含多级行政区域数据的站点（例如：国家->省->市->区->街道）。数据是层层嵌套的，共有五级目录。

![](img/8cb08ae31193d57b367add58bc6536da_36.png)

面对这种层级结构，我们通常有两种遍历策略：

*   **深度优先**：沿着一条链接路径深入到底，再返回上一层继续深入。虽然直观，但嵌套循环或递归实现可能导致代码复杂且效率问题。
*   **广度优先**：先获取同一层级的所有链接，然后逐层向下处理。条理更清晰，易于管理和控制。

![](img/8cb08ae31193d57b367add58bc6536da_38.png)

![](img/8cb08ae31193d57b367add58bc6536da_40.png)

![](img/8cb08ae31193d57b367add58bc6536da_42.png)

![](img/8cb08ae31193d57b367add58bc6536da_44.png)

在本案例中，我们将采用**广度优先**的策略来实现爬虫，这样代码结构会更清晰，也更容易处理中途可能出现的异常。

## 代码实现详解

我们将按照爬虫步骤，一步步构建我们的爬虫程序。

### 第一步：发送请求与获取响应

![](img/8cb08ae31193d57b367add58bc6536da_46.png)

![](img/8cb08ae31193d57b367add58bc6536da_48.png)

首先，我们导入必要的库并定义基础常量和函数。

![](img/8cb08ae31193d57b367add58bc6536da_50.png)

![](img/8cb08ae31193d57b367add58bc6536da_52.png)

![](img/8cb08ae31193d57b367add58bc6536da_54.png)

![](img/8cb08ae31193d57b367add58bc6536da_56.png)

```python
import requests
from bs4 import BeautifulSoup
import re

![](img/8cb08ae31193d57b367add58bc6536da_58.png)

# 定义常量
BASE_URL = "http://www.stats.gov.cn/tjsj/tjbz/tjyqhdmhcxhfdm/2018/"
HEADERS = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36'
}

![](img/8cb08ae31193d57b367add58bc6536da_60.png)

def get_html(url, addr=''):
    """
    发送请求并获取响应文本
    :param url: 要请求的链接
    :param addr: 上一级地址（用于拼接完整地址）
    :return: 响应的HTML文本
    """
    response = requests.get(url, headers=HEADERS)
    response.raise_for_status()  # 检查请求是否成功
    response.encoding = 'gb2312'  # 该网站使用gb2312编码
    return response.text
```
我们定义了一个 `get_html` 函数来封装请求过程，提高代码的复用性。注意我们指定了编码为 `gb2312` 以正确解析中文。

### 第二步：数据提取函数

![](img/8cb08ae31193d57b367add58bc6536da_62.png)

![](img/8cb08ae31193d57b367add58bc6536da_64.png)

接下来，我们定义数据提取函数。我们将使用 Beautiful Soup 来解析HTML。

![](img/8cb08ae31193d57b367add58bc6536da_66.png)

![](img/8cb08ae31193d57b367add58bc6536da_68.png)

![](img/8cb08ae31193d57b367add58bc6536da_70.png)

```python
def parse_html_with_bs(html, addr=''):
    """
    使用Beautiful Soup解析HTML，提取当前页面的下级链接和地区名
    :param html: HTML文本
    :param addr: 上一级地址
    :return: 列表，每个元素是 (地区完整地址, 下级链接) 的元组
    """
    soup = BeautifulSoup(html, 'html.parser')
    link_list = []
    # 找到所有的<a>标签
    for a_tag in soup.find_all('a'):
        # 获取链接和文本
        href = a_tag.get('href')
        text = a_tag.get_text()
        # 拼接完整的URL
        full_url = BASE_URL + href
        # 拼接完整的地址
        full_addr = addr + text
        # 将(地址，链接)存入列表
        link_list.append((full_addr, full_url))
    return link_list
```
这个函数会解析页面，找到所有 `<a>` 标签，提取其链接和文本，并与上一级地址拼接，形成完整的路径和URL。

![](img/8cb08ae31193d57b367add58bc6536da_72.png)

![](img/8cb08ae31193d57b367add58bc6536da_74.png)

![](img/8cb08ae31193d57b367add58bc6536da_76.png)

### 第三步：实施广度优先爬取

![](img/8cb08ae31193d57b367add58bc6536da_78.png)

![](img/8cb08ae31193d57b367add58bc6536da_80.png)

![](img/8cb08ae31193d57b367add58bc6536da_82.png)

![](img/8cb08ae31193d57b367add58bc6536da_84.png)

现在，我们开始实施广度优先爬取。我们从第一级（省份）开始，逐层向下。

![](img/8cb08ae31193d57b367add58bc6536da_86.png)

![](img/8cb08ae31193d57b367add58bc6536da_88.png)

```python
# 1. 获取第一级数据（省份/直辖市）
province_html = get_html(BASE_URL)
province_links = parse_html_with_bs(province_html)
print(f"第一级找到 {len(province_links)} 个链接")

![](img/8cb08ae31193d57b367add58bc6536da_90.png)

![](img/8cb08ae31193d57b367add58bc6536da_92.png)

![](img/8cb08ae31193d57b367add58bc6536da_94.png)

![](img/8cb08ae31193d57b367add58bc6536da_96.png)

![](img/8cb08ae31193d57b367add58bc6536da_98.png)

![](img/8cb08ae31193d57b367add58bc6536da_100.png)

![](img/8cb08ae31193d57b367add58bc6536da_102.png)

![](img/8cb08ae31193d57b367add58bc6536da_104.png)

# 为了演示，我们只处理前几个省份
for province_addr, province_url in province_links[:3]:
    print(f"\n处理省份: {province_addr}")
    
    # 2. 获取第二级数据（市）
    city_html = get_html(province_url, province_addr)
    city_links = parse_html_with_bs(city_html, province_addr)
    
    for city_addr, city_url in city_links[:2]:  # 每个省只处理前两个市
        print(f"  处理城市: {city_addr}")
        
        # 3. 获取第三级数据（区/县）
        district_html = get_html(city_url, city_addr)
        district_links = parse_html_with_bs(district_html, city_addr)
        
        for district_addr, district_url in district_links[:2]:  # 每个市只处理前两个区
            print(f"    处理区县: {district_addr}")
            
            # 4. 获取第四级数据（街道/镇）
            street_html = get_html(district_url, district_addr)
            street_links = parse_html_with_bs(street_html, district_addr)
            
            for street_addr, street_url in street_links[:2]:  # 每个区只处理前两个街道
                print(f"      处理街道: {street_addr}")
                
                # 5. 获取第五级数据（社区/村）-- 最终数据
                # 注意：最后一页结构可能不同，需要单独解析
                community_html = get_html(street_url, street_addr)
                # 这里需要根据最后一页的HTML结构编写新的解析函数
                # final_data = parse_final_page(community_html, street_addr)
                # print(f"        获取到最终数据: {final_data}")
                # 将 final_data 保存到文件或列表中
```
以上代码展示了广度优先的核心逻辑：处理完当前层级的所有条目后，再进入下一层级。为了控制数据量和演示速度，我们使用切片 `[:3]`、`[:2]` 来限制每层处理的数量。

![](img/8cb08ae31193d57b367add58bc6536da_106.png)

![](img/8cb08ae31193d57b367add58bc6536da_108.png)

![](img/8cb08ae31193d57b367add58bc6536da_110.png)

**注意**：在实际爬取中，最后一层（如社区/村）的页面结构可能与前几层不同，链接可能不再是 `<a>` 标签，而是直接显示为文本。因此，我们需要针对最后一页编写特定的解析函数 `parse_final_page`，例如使用更精确的CSS选择器或处理表格数据来提取最终的地名。

![](img/8cb08ae31193d57b367add58bc6536da_112.png)

![](img/8cb08ae31193d57b367add58bc6536da_114.png)

![](img/8cb08ae31193d57b367add58bc6536da_116.png)

![](img/8cb08ae31193d57b367add58bc6536da_118.png)

![](img/8cb08ae31193d57b367add58bc6536da_120.png)

### 第四步：数据存储

![](img/8cb08ae31193d57b367add58bc6536da_122.png)

![](img/8cb08ae31193d57b367add58bc6536da_124.png)

数据提取后，我们需要将其保存。这里以追加写入TXT文件为例：

![](img/8cb08ae31193d57b367add58bc6536da_126.png)

![](img/8cb08ae31193d57b367add58bc6536da_128.png)

![](img/8cb08ae31193d57b367add58bc6536da_130.png)

![](img/8cb08ae31193d57b367add58bc6536da_132.png)

![](img/8cb08ae31193d57b367add58bc6536da_134.png)

```python
def save_to_txt(data, filename='address_data.txt'):
    """
    将数据保存到TXT文件
    :param data: 要保存的字符串数据
    :param filename: 文件名
    """
    with open(filename, 'a', encoding='utf-8') as f:
        f.write(data + '\n')

![](img/8cb08ae31193d57b367add58bc6536da_136.png)

![](img/8cb08ae31193d57b367add58bc6536da_138.png)

# 在爬取循环中，当获得最终数据 final_data 后，调用保存函数
# save_to_txt(final_data)
```
你也可以使用 `pandas` 将收集到的所有数据一次性保存为Excel文件，这样更利于后续分析。

![](img/8cb08ae31193d57b367add58bc6536da_140.png)

![](img/8cb08ae31193d57b367add58bc6536da_142.png)

![](img/8cb08ae31193d57b367add58bc6536da_144.png)

![](img/8cb08ae31193d57b367add58bc6536da_145.png)

## 总结与注意事项

![](img/8cb08ae31193d57b367add58bc6536da_147.png)

![](img/8cb08ae31193d57b367add58bc6536da_149.png)

本节课中，我们一起学习并实践了一个多级目录网站的爬虫案例。我们回顾了爬虫的四大步骤，重点采用了**广度优先**的策略来遍历层级数据，并编写了相应的请求和解析函数。

![](img/8cb08ae31193d57b367add58bc6536da_151.png)

总结关键点：
1.  **策略选择**：对于树状或层级结构的数据，广度优先爬取通常比深度优先更易于控制和实现。
2.  **代码复用**：将请求、解析等功能封装成函数，能极大提高代码的清晰度和可维护性。
3.  **结构分析**：不同层级的页面结构可能发生变化，需要仔细分析并可能编写不同的解析函数。
4.  **伦理与合规**：在实际项目中，务必遵守网站的 `robots.txt` 协议，并设置合理的请求间隔，避免对目标服务器造成压力。

![](img/8cb08ae31193d57b367add58bc6536da_153.png)

这个案例提供了一个处理分层数据的通用框架。你可以在此基础上，修改解析逻辑以适应不同的网站结构，并完善数据存储部分，从而爬取更多有价值的信息。