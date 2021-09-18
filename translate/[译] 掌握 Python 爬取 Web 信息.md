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





```
visited = set() 
to_visit = set() 
max_visits = 3 
 
def crawl(url): 
	print('Crawl: ', url) 
	response = requests.get(url) 
	soup = BeautifulSoup(response.content, 'html.parser') 
	visited.add(url) 
	for a in soup.select('a.page-numbers'): 
		link = a.get('href') 
		to_visit.add(link) 
		if link not in visited and len(visited) < max_visits: 
			crawl(link) 
 
crawl('https://scrapeme.live/shop/page/1/') 
 
print(visited) # {'.../3/', '.../1/', '.../2/'} 
print(to_visit) # { ... new ones added, such as pages 5 and 6 ... } 
```



它是一个递归函数，有两个退出条件：没有更多的链接可以访问，或者我们达到了最大访问量。 无论哪种情况，它都会退出并打印访问过的链接和待处理的链接。



需要注意的是，同一个链接可以添加多次，但只会被抓取一次。 在一个大项目中，想法是设置一个计时器并且仅在几天后请求每个 URL。



### 关注点分离



我们说过这不是关于提取或解析内容，而是我们需要在它变得纠缠之前分离关注点。 为此，我们将创建三个辅助函数：获取 HTML、提取链接和提取内容。 正如他们的名字所暗示的那样，他们每个人都将执行网络抓取的主要任务之一。



第一点，我们从 URL 获取 HTML，使用相同的库，但是做了一层封装，使用 `try` 来确保调用的安全。



```
def get_html(url): 
	try: 
		return requests.get(url).content 
	except Exception as e: 
		print(e) 
		return '' 
```



第二点，展开链接，和之前的工作一样。



```
def extract_links(soup): 
	return [a.get('href') for a in soup.select('a.page-numbers') 
		if a.get('href') not in visited] 
```



最后，在我们想要展开的内容处，放上占位符。我们完成这部分简化之后，将会返回页面的基本信息，而不再需要进入详情页面。要展示我们展开的部分内容，我们可以打印这些标题（Pokémon name）。



```
def extract_content(soup): 
	for product in soup.select('.product'): 
		print(product.find('h2').text) 
 # Bulbasaur, Ivysaur, ... 
```



将这些内容组装在一起。



```
def crawl(url): 
	if not url or url in visited: 
		return 
	print('Crawl: ', url) 
	visited.add(url) 
	html = get_html(url) 
	soup = BeautifulSoup(html, 'html.parser') 
	extract_content(soup) 
	links = extract_links(soup) 
	to_visit.update(links) 
```



注意到哪里不一样了么？爬取的逻辑没有依附于链接展开的部分。每个帮助方法处理一部分。`crawl` 方法通过调用它们，并应用结果来充当协调器。



随着项目的发展，所有这些部分都可以移动到文件中或作为参数/回调传递。 如果核心独立于所选页面和内容，我们可以概括用例。



我们错过什么了吗？



我们需要添加第一个 URL，并调用爬取方法。由于 `crawl` 不再是递归的，我们将在一个单独的循环中处理他们。



```
to_visit.add('https://scrapeme.live/shop/page/1/') 
 
while (len(to_visit) > 0 and len(visited) < max_visits): 
	crawl(to_visit.pop())
```



### 并发请求



缺少一个重要的部分：并行性。 HTTP 请求处理程序大部分时间都处于空闲状态，等待响应返回。 这意味着我们可以同时发送几个而不会使机器过载。 然后在他们回来时处理它们。



需要注意的是，这种方法仅在命令不是必需的情况下才有效。 但是我们已经在使用集合，根据 Python 的定义，“集合是没有重复元素的无序集合”。 这意味着我们的流程从一开始就是无序的。



在深入研究并行请求之前，我们必须了解几个概念：同步和队列。



### 同步队列



线程或并行计算存在巨大风险：修改来自不同线程的相同变量或数据结构。 这意味着我们的两个请求将向集合添加新链接（即 to_visit）。 由于数据结构不受保护，因此两者都可以像这样读取和写入：



1. 两者都读取其内容，即 (1, 2, 3) （简化）

2. 线程一添加指向第 4、5 页的链接：（1、2、3、4、5）

3. 线程二添加指向第 6、7 页的链接：(1, 2, 3, 6, 7)



这怎么发生的？ 当线程 2 编写新链接时，它将它们添加到只有三个元素的集合中。



这是一个非常简化的版本； 检查链接以获取更多信息。



我们可以做些什么来避免这些冲突？ 同步或锁定。 来自文档：“队列使用锁来临时阻止竞争线程。” 这意味着线程一会在集合上获取一个锁，读写没有任何问题，然后自动释放锁。 同时，线程 2 必须等到锁可用。 只有这样才能读和写。



```
import queue 
 
q = queue.Queue() 
q.put('https://scrapeme.live/shop/page/1/') 
 
def crawl(url): 
	... 
	links = extract_links(soup) 
	for link in links: 
		if link not in visited: 
			q.put(link)
```



目前，它不起作用。 不要担心。 现有代码中的更改最少：我们用队列替换了 to_visit。 但是队列需要处理程序或工作程序来处理它们的内容。 有了上面的内容，我们创建了一个队列并添加了一个项目（原始项目）。 我们还修改了爬网功能，将链接放入队列中，而不是更新之前的集合。











