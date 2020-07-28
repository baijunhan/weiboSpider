## 常见问题

### 1.程序运行出错，错误提示中包含“ImportError: cannot import name 'config_util' from '__main__'”，如何解决？
出现这种错误，说明使用者很可能是直接运行的.py文件，程序正确的运行方式是在weiboSpider目录下，运行如下命令：
```
python3 -m weibo_spider
```

### 2.程序运行出错，错误提示中包含“'NoneType' object”字样，如何解决？
这是最常见的问题之一。出错原因是爬取速度太快，被暂时限制了，限制可能包含爬虫账号限制和ip限制。一般情况下，一段时间后限制会自动解除。可通过降低爬取速度避免被限制，具体修改config.json文件中的如下代码：
```
    "random_wait_pages": [1, 5],
    "random_wait_seconds": [6, 10],
```
上面的意思是每爬取1到5页，随机等待6到10秒。可以通过加快暂停频率（减小random_wait_pages内的值）或增加等待时间（加大random_wait_seconds内的值）避免被限制。

### 3.如何获取微博评论？
因为限制，只能获取一部分评论，无法获取全部，因此暂时没有添加获取评论功能的计划。

### 4.有的长微博正文只能获取一部分内容，如何解决？
程序是可以获取长微博全文的。程序首先在微博列表页获取微博，如果发现长微博（正文没有显示完整，以“全文”代替部分内容的微博），会先保存这个不全的内容，然后去该长微博的详情页尝试获取全文，如果获取成功，获取的内容就是微博文本；如果获取失败，等待若干秒重新获取；如果连续尝试5次都失败，就用上面不全的内容代替。这样做的原因是避免因部分长微博获取失败而卡住。如果想尝试更多次，可以修改comment_parser.py文件get_long_weibo方法内for循环的次数。

### 5.如何按指定关键词获取微博？
请使用[weibo-search](https://github.com/dataabc/weibo-search)。该程序可以连续获取一个或多个微博关键词搜索结果，并将结果写入文件（可选）、数据库（可选）等。所谓微博关键词搜索即：搜索正文中包含指定关键词的微博，可以指定搜索的时间范围。对于非常热门的关键词，一天的时间范围，可以获得1000万以上的搜索结果，N天的时间范围就可以获得1000万 X N搜索结果。对于大多数关键词，一天产生的相应微博数量应该在1000万条以下，因此可以说该程序可以获得大部分关键词的全部或近似全部的搜索结果。而且该程序可以获得搜索结果的所有信息，本程序获得的微博信息该程序都能获得。

### 6.如何获取微博用户关注列表中用户的user_id？
请使用[weibo-follow](https://github.com/dataabc/weibo-follow)。该程序可以利用一个user_id，获取该user_id微博用户关注人的user_id，一个user_id最多可以获得200个user_id，并写入user_id_list.txt文件。程序支持读文件，利用这200个user_id，可以获得最多200X200=40000个user_id。再利用这40000个user_id可以得到40000X200=8000000个user_id，如此反复，以此类推，可以获得大量user_id。本项目也支持读文件，将上述程序的结果文件user_id_list.txt路径赋值给本项目config.json的user_id_list参数，就可以获得这些user_id用户所发布的大量微博。

### 7.如何获取自己的微博？
修改info_parser.py和page_parser.py中__init__方法，将前者的self.url修改为：
```
        self.url = "https://weibo.cn/%s/profile" % (user_id)
```
后者的self.url修改为：
```
        self.url = "https://weibo.cn/%s/profile?page=%d" % (user_uri, page)
```