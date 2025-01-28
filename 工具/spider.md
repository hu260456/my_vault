### urllib

```Python
import urllib.request as ur
url = '要爬取的url'
header = {
    'User-Agent': '<用户代理>', ...
}
# 包装一个请求
request = ur.Request(url, headers=header)
# 发送请求
response = ur.urlopen(request)
# 获取数据
print(response.read().decode())
# 拉回页面源代码
urllib.request.urlretrieve(url, <file_name>)
##### POST请求使用parse解析地址 #####
import json
import urllib.parse
import urllib.request as ur
data = urllib.parse.urlencode({
    '<key>': '<value>'
})
request = ur.Request(<url>, data.encode('utf-8'), <header>) # 定制请求对象
response = ur.urlopen(request) # 请求
content = response.read().decode('utf-8') # 结果，是字符串
obj = json.loads(content) # 返回字典？
##### GET请求 #####
url = 'https://www.google.com/s?kw='
url += urllib.parse.quote('今天吃什么') # 转化为url编码
request = ur.Request(url, headers=header)
##### 异常处理 #####
import urllib.error as ue
try:
    response = ur.urlopen(request)
    content = response().read().decode('utf-8')
except ue.HTTPError:
    print('HTTP错误')
except ue.URLError:
    print('URL错误')
##### handler，搭配代理使用 #####
handler = ur.HTTPHandler() # get handler
opener = ur.build_opener() # get build_opener
response = opener.open(request)
content = response.read().decode('utf-8)
##### 代理 #####
import random
import urllib.request as ur
request = ur.Request(url, headers=header)
proxy = {'http': 'ip address'} # 单代理
proxies_pool = [{'http': '<ip>'}, ...] # 代理池
rand_proxy = rando.choice(proxies) # 如果用代理池，随机抽取代理
handler = ur.ProxyHandler(proxies=proxies) # 使用代理处理器
opener = ur.build_opener(handler)
response = opener.open(request)
```
### requests

```Python
import requests
from bs4 import BeautifulSoup
url = '<url>'
header = {
    'User-Agent': ...
}
response = requests.get(url, headers=header) # 发送请求
soup = BeautifulSoup(response.text, 'lxml')
try:
    print(soup.select(<css_selector>)[0].attrs.get('value'))
except IndexError:
    print(f'<css_selector>[{num}]不存在')
##### 获取验证码等问题，存在一个问题：获取验证码的请求与登录的请求不是用一个
##### 会导致登陆失败，此时就要使用：requests.session()
session = requests.session()
content = session.get(<url>).content
# 注意：写的是图片，二进制
with open('code.jpg', 'wb') as f:
    f.write(content)
##### response的类型和属性 #####
response = requests.get(url)
type(response) # 类型是Response
response.encoding # 响应的解码格式
response.text # 响应的源码
response.content # 二进制形式的响应
response.url # 响应的url
response.status_code # 响应状态码
response.headers # 响应的header
##### 在requests中设置代理 #####
proxy = {...}
params = {...}
response = requests.get(url, params, headers=header, proxies=proxy)
```
### 数据抽取

```Python
########## lxml ##########
import requests
from lxml import etree
res = requests.get(url)
html = res.HTML(res.text)
result = html.xpath('xpath路径')
########## BeatifulSoup ##########
from bs4 import BeautifulSoup
soup = BeautifulSoup(open('./test.html', encoding='utf-8'), 'lxml')
print(soup.select(<css_selector>)[0].get_text()) # 获取值
print(soup.select(<css_selector>)[0].attrs) # 获取包含节点属性的字典
```
### Scrapy

- 命令
```Shell
# 创建一个项目
scrapy startproject name
# 生成一个蜘蛛
scrapy genspider spider_name domain_to_grasp
# 运行一个蜘蛛，不打印日志，将成果保存到文件
scrapy crawl spider_name --nolog -o demo.csv
```

- spider.py
```Python
import scrapy
from scrapy import Selector, Request
from ..items import xItem
def start_requests(self):
    for page in range(10):
        yield Request(url=f'https://xxx.com/x{page*25}x')
def parse(self, response, **kwargs):
    parse_html = Selector(response)
    items = parse_html.css('css选择器')  # 还可用xpath、re
    for item in items:
        xitem = xItem()
        target = item.css('进行二次选择')
        # 提取第一个对象的值，如果只有一个，可以使用extract()
        xitem['title | rank | subject'] = target.extract_first()
        yield xitem
```

- items.py
```Python
class DemoItem(scrapy.Item):
    # 定义想要爬取的字段
    title = scrapy.Field()
    rank = scrapy.Field()
    subject = scrapy.Field()
```

- pipelines.py
```Python
# 记得在settings.ps中开启管道，数值越小管道越优先
ITEM_PIPELINES = {
    "demo.pipelines.DemoPipeline": 300,
}
import openpyxl  # 用于写入excel
import pymysql  # 用于写入mysql

##### 用于保存到DB的Pipeline #####
class DbPipeline:
    def __init__(self):
        self.conn = pymysql.connect(
            host = 'localhost',
            port=3306,
            user="root",
            password="666666",
            database="playground",
            charset="utf8mb4"
        )
        # 用于执行SQL
        self.cursor = self.conn.cursor()
        
    def close_spider(self, _spider):
        # 记得提交，数据才会保存到mysql
        self.conn.commit()
        self.conn.close()
        
    def process_item(self, item, _spider):
        title = item.get("title") or ""
        rank = item.get("rank", 0)
        subject = item.get("subject", "")
        # 执行SQl
        # 可以修改代码，使用executemany()来提升效率
        self.cursor.execute(
            "insert into tb_top_movie (title, rating, subject) values (%s, %s, %s)",
            (title, rank, subject),
        )
        return item
##### 用于保存到exel的Pipeline #####
class DemoPipeline:
    # 数据保存到excel：在这建好工作簿
    def __init__(self):
        # 创建工作簿
        self.wb = openpyxl.Workbook()
        # 创建一张工作表
        # self.wb.create_sheet("表名")
        # 获取活动工作表
        self.ws = self.wb.active
        # 修改ws的表名
        self.ws.title = "Top250"
        # 添加一项（表头）
        self.ws.append(("标题", "评分", "中心思想"))

    # 爬虫开始时调用
    def open_spider(self, spider):
        pass

    # 爬虫结束时，保存工作簿
    def close_spider(self, spider):
        self.wb.save("电影数据.xlsx")

    # 处理数据
    # 每次拿到item都会调用
    def process_item(self, item, spider):
        # 如果取不到会报异常
        # self.ws.append(item["title"], item["rank"], item["subject"])
        # 所以：
        # 无值就返回空串，写法一
        # title = item.get("title", "")
        # 写法二
        title = item.get("title") or ""
        rank = item.get("rank", 0)
        subject = item.get("subject", "")
        self.ws.append((title, rank, subject))
        return item
```