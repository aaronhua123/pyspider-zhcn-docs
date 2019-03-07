### 3级：使用PhantomJS渲染
有时网页太复杂，无法找到API请求。现在是时候满足PhantomJS的力量了。

要使用PhantomJS，你应该有PhantomJS 安装。如果你正在使用all模式运行pyspider，那么如果可以在中删除，则启用PhantomJS PATH。

确保phantomjs正在运行

    $ pyspider phantomjs
如果输出是，继续本教程的其余部分

    Web server running on port 25555
#### 使用PhantomJS
当连接PhantomJS的pyspider时，您可以通过添加参数fetch_type='js'来启用此功能self.crawl。我们使用PhantomJS来抓取http://www.twitch.tv/directory/game/Dota%202的频道列表，该列表 加载了我们在第2级中讨论过的AJAX ：

    class Handler(BaseHandler):
        def on_start(self):
            self.crawl('http://www.twitch.tv/directory/game/Dota%202',
                       fetch_type='js', callback=self.index_page)
    
        def index_page(self, response):
            return {
                "url": response.url,
                "channels": [{
                    "title": x('.title').text(),
                    "viewers": x('.info').contents()[2],
                    "name": x('.info a').text(),
                } for x in response.doc('.stream.item').items()]
            }
我使用了一些API来处理流列表。您可以从PyQuery完整API中找到完整的API参考

#### 在页面上运行JavaScript
我们将尝试从本节中的http://www.pinterest.com/categories/popular/中删除图像。开头只显示25张图像，滚动到页面底部时会加载更多图像。

要抓取尽可能多的图像，我们可以使用js_script参数来设置一些函数包装的JavaScript代码来模拟滚动操作：

    class Handler(BaseHandler):
        def on_start(self):
            self.crawl('http://www.pinterest.com/categories/popular/',
                       fetch_type='js', js_script="""
                       function() {
                           window.scrollTo(0,document.body.scrollHeight);
                       }
                       """, callback=self.index_page)
    
        def index_page(self, response):
            return {
                "url": response.url,
                "images": [{
                    "title": x('.richPinGridTitle').text(),
                    "img": x('.pinImg').attr('src'),
                    "author": x('.creditName').text(),
                } for x in response.doc('.item').items() if x('.pinImg')]
            }
页面加载后可以执行脚本（可以通过js_run_at参数更改）
我们在页面加载后滚动一次，您可以使用滚动多次setTimeout。在超时到达之前，PhantomJS将获取尽可能多的项目。


在线演示：http：//demo.pyspider.org/debug/tutorial_pinterest