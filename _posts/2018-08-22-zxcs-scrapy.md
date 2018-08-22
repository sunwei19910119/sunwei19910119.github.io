---
layout: post
title:  "NovelSpider小说爬虫简易教程"
date:   2018-08-20
desc: "使用Scrapy爬取，知轩藏书，全本小说"
keywords: "scrapy,python,novel"
categories: [Others]
tags: [python,Scrapy]
icon: icon-html
---

## 1.简介
写了个简易爬虫，爬取全本小说。感谢[知轩藏书](http://http://www.zxcs8.com/)提供了大量精校全本小说。使用爬虫的朋友请友好爬取，避免给网站造成不良影响。

网站类目：
* 都市：http://www.zxcs8.com/sort/23
* 武侠：http://www.zxcs8.com/sort/36
* 仙侠：http://www.zxcs8.com/sort/37
* 奇幻：http://www.zxcs8.com/sort/38
* 玄幻：http://www.zxcs8.com/sort/39
* 科幻：http://www.zxcs8.com/sort/40
* 灵异：http://www.zxcs8.com/sort/41
* 历史：http://www.zxcs8.com/sort/42
* 军事：http://www.zxcs8.com/sort/43
* 实体：http://www.zxcs8.com/sort/46   
。。。
并没有全部放进start_urls，请根据个人需要选择

网站分页：分类+/page/1

## 2.创建项目     
```
scrape startproject NovelSpider 
scrapy genspider zxcs www.zxcs8.com
```
## 3.创建调试工具类（Pycharm的Debug） 
在项目根目录里创建`main.py`作为调试工具文件
```
from scrapy.cmdline import execute
import sys
import os

#将系统当前目录设置为项目根目录
sys.path.append(os.path.dirname(os.path.abspath(__file__)))
#执行命令，相当于在控制台cmd输入改名了
execute(["scrapy", "crawl" , "jobbole"])
```
上面的代码，就是在pycharm中的run/Edit Con…菜单，将当前项目路径加入到 path 中，然后通过调用scrapy 命令行来启动我们的工程。然后，通过设置断点调试，一步一步查看我们的提取的变量的值是否正确。
settings.py的设置不遵守reboots协议
```
ROBOTSTXT_OBEY = False
```
## 4.解析URL爬取
一句话概括：层层解析，结果传入Itemload。
```python
    def parse(self, response):
        # 提取出html页面中的所有url，并跟踪这些url进一步爬取。
        # 如果提取的url中格式为/post/xxx 就下载之后直接进入解析函数
        all_urls = response.xpath('//dl[@id="plist"]/dt[1]/a/@href').extract()
        all_urls = [parse.urljoin(response.url, url) for url in all_urls]

        for url in all_urls:
            match_obj = re.match("(.*zxcs8.com/post/(\d+))(/|$).*", url)
            if match_obj:
                # 如果提取到小说相关的页面则下载后交由提取函数进行提取
                request_url = match_obj.group(1)
                yield scrapy.Request(request_url, callback=self.parse_novel)
            else:
                # 注释这里方便调试
                pass
        # 提取下一页并交给scrapy进行下载
        next_url = response.xpath('//div[@id="pagenavi"]/span/following-sibling::a[1]/@href').extract_first("")

        if next_url:
            # 如果还有next url 就调用下载下一页，回调parse函数找出下一页的url。
            yield Request(url=next_url, callback=self.parse)




    def parse_novel(self,response):
        # 在小说详情页面进一步解析
        title = response.xpath('//div[@id="content"]/h1/text()').extract_first("")
        download_page = response.xpath('//div[@class="down_2"]/a/@href').extract_first("")
        #请求下载页
        yield scrapy.Request(download_page, callback=self.download)

    def download(self,response):
        # 实例化
        item_loader = ItemLoader(item=ZxcsItem(), response=response)
        item_loader.add_xpath('file_urls','//span[@class="downfile"][1]/a/@href')
        zxcs_item = item_loader.load_item()

        # 已经填充好了值调用yield传输至pipeline
        yield zxcs_item

```

## 5.运行爬虫下载 
设置下载：
```
#控制速度
DOWNLOAD_DELAY = 0.5

# 使用管道
ITEM_PIPELINES = {
     'scrapy.pipelines.files.FilesPipeline': 1,
                 }
# 文件存储路径
project_dir = os.path.abspath(os.path.dirname(__file__))
FILES_STORE = os.path.join(project_dir, 'download')
```
运行爬取
```
scrapy crawl zxcs
```
## 6.进阶方案
### a.自定义下载路径和文件名
修改pipelines:
```python
from scrapy.pipelines.files import FilesPipeline
from urllib.parse import urlparse
from os.path import basename,dirname,join

class MyFilePipeline(FilesPipeline):

    def file_path(self, request, response=None, info=None):
        path = urlparse(request.url).path

        return basename(path)
```
修改settings：
```
# 使用管道
ITEM_PIPELINES = {
    # 'scrapy.pipelines.files.FilesPipeline': 1,
    'NovelSpider.pipelines.MyFilePipeline': 1,
                 }
```

### b.数据异步写入数据库
```
pip install mysqlclient
```
保存到数据库的(异步Twisted)编写
Scrapy使用了Twisted作为框架，Twisted有些特殊的地方是它是事件驱动的，并且比较适合异步的代码。在任何情况下，都不要写阻塞的代码。阻塞的代码包括:

* 访问文件、数据库或者Web
* 产生新的进程并需要处理新进程的输出，如运行shell命令
* 执行系统层次操作的代码，如等待系统队列

seeting.py设置
```
MYSQL_HOST = "127.0.0.1"
MYSQL_DBNAME = "spider"
MYSQL_USER = "root"
MYSQL_PASSWORD = "123456"
```
修改pipelines,添加MysqlTwistedPipline方法


