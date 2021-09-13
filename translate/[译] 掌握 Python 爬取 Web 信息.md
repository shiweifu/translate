翻译自：[Mastering Web Scraping in Python: Crawling from Scratch - ZenRows](https://www.zenrows.com/blog/mastering-web-scraping-in-python-crawling-from-scratch)



你是否尝试爬取数千个页面？有没有考虑过伸缩性？以及从系统错误中恢复？



在了解了如何从网站中抓取内容，以及如何避免被屏蔽后，我们将看一下爬取的过程。如果想要获取大量数据，逐个 URL 获取信息，并不现实。我们需要使用自动化的系统，来不断发现新页面，并访问他们。



>  免责声明：对于实际使用，请寻找合适的软件。下面有更多信息。本教程假装是对爬虫工作原理和基础知识的介绍。但是有一大堆细节需要解决。



#### 预先准备



开干之前，你需要确保 Python 3 已经安装。一些系统已经自带了 Python 3。安装了 Python 3 之后，安装一些依赖库就很简单了，通过 `pip install` 安装。



```
pip install requests beautifulsoup4
```



#### 如何获取页面中的所有链接



从本系列的第一篇文章中，我们知道使用 requests.get 和 BeautifulSoup 从网页获取数据很容易。 我们将首先在准备测试抓取的假商店中找到链接。



获取内容的基础是相同的。 然后我们获取分页器上的所有链接并将这些链接添加到一个集合中。 我们选择 set 以避免重复。 如您所见，我们对链接的选择器进行了硬编码，这意味着它不是一个通用的解决方案。 目前，我们将专注于手头的页面。



```
import requests 
from bs4 import BeautifulSoup 
 
to_visit = set() 
response = requests.get('https://scrapeme.live/shop/page/1/') 
soup = BeautifulSoup(response.content, 'html.parser') 
for a in soup.select('a.page-numbers'): 
	to_visit.add(a.get('href')) 
 
print(to_visit) 
# {'https://scrapeme.live/shop/page/2/', '.../3/', '.../46/', '.../48/', '.../4/', '.../47/'} 
```



### 一次一个 URL，顺序



现在我们有几个链接，但无法全部访问它们。 我们需要某种循环来为每个可用的 URL 执行提取部分来解决这个问题。 也许最直接的方法，虽然不是可扩展的，但是使用相同的循环。 但在此之前，还有一个缺失的部分：避免对同一页面进行两次抓取。



我们将跟踪另一组中已经访问过的链接，并通过在每次请求之前检查它们来避免重复。 在这种情况下， to_visit 没有被使用，只是为了演示目的而维护。 为了防止访问每个页面，我们还将添加一个 max_visits 变量。 现在，我们忽略 robots.txt 文件，但我们必须保持礼貌和友善。



