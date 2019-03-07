## pyspider 
一个强大的python爬虫(Web Crawler) .尝试它!
****
* 用python写脚本
* 强大的网页脚本编辑界面，任务监控器，项目管理器和结构查看器
* MySQL, MongoDB, Redis, SQLite, Elasticsearch各种数据库引擎; PostgreSQL数据库系统 和 SQLAlchemy数据库ORM模型 作为数据库的支持
* RabbitMQ, Beanstalk, Redis and Kombu 作为信息队列
* 任务优先级, 失败重试, 定期的, 周期性的重复采集 等等...
* 分布式架构, 采集 Javascript 页面, Python 2&3, 等等...


教程: [http://docs.pyspider.org/en/latest/tutorial/](http://docs.pyspider.org/en/latest/tutorial/)  
文档: [http://docs.pyspider.org/](http://docs.pyspider.org/)   
发布日志: [https://github.com/binux/pyspider/releases](https://github.com/binux/pyspider/releases)

#### 简单的演示
    from pyspider.libs.base_handler import *
    class Handler(BaseHandler):
        crawl_config = {
        }
    
        @every(minutes=24 * 60)
        def on_start(self):
            self.crawl('http://scrapy.org/', callback=self.index_page)
    
        @config(age=10 * 24 * 60 * 60)
        def index_page(self, response):
            for each in response.doc('a[href^="http"]').items():
                self.crawl(each.attr.href, callback=self.detail_page)
    
        def detail_page(self, response):
            return {
                "url": response.url,
                "title": response.doc('title').text(),
            }
#### 例子

安装
`pip install pyspider`   
命令行运行 `pyspider`    
浏览器查看 `http://localhost:5000/`   
快速入门: `http://docs.pyspider.org/en/latest/Quickstart/`



