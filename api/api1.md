#### self.crawl
#### self.crawl（url，** kwargs）
`self.crawl` 是告诉`pyspider`应该抓取哪个url的主界面。

#### 参数：
#### 网址
要抓取的网址或网址列表。

#### 打回来
解析响应的方法。_default：`__call___`

```
def on_start(self):
    self.crawl('http://scrapy.org/', callback=self.index_page)

```
以下参数是可选的

#### 年龄
任务的有效期。在此期间，该页面将被视为未修改。默认值：-1（从不重新抓取）

```
@config(age=10 * 24 * 60 * 60)
def index_page(self, response):
    ...
```
回调解析的每个页面index_page都将被视为在10天内未被更改。如果您在上次抓取后的10天内提交任务，则会将其丢弃。

优先
任务安排的优先级越高越好。默认值：0

    ```
    def index_page(self):
    self.crawl('http://www.example.org/page2.html', callback=self.index_page)
    self.crawl('http://www.example.org/233.html', callback=self.detail_page,
               priority=1)
    ```
该页面233.html之前将被抓取page2.html。使用此参数可以执行BFS并减少队列中的任务数（这可能会花费更多的内存资源）。

#### exetime
unix时间戳中任务的执行时间。默认值：0（立即）

    ```
    import time
    def on_start(self):
        self.crawl('http://www.example.org/', callback=self.callback,
                   exetime=time.time()+30*60)
    ```
该页面将在30分钟后被抓取。

#### 重试
失败时重试次数。默认值：3

#### ITAG
来自前沿页面的标记，以显示任务的潜在修改。它将与其最后一个值进行比较，在更改时重新抓取。默认值：无

    ```
    def index_page(self, response):
    for item in response.doc('.item').items():
        self.crawl(item.find('a').attr.url, callback=self.detail_page,
                   itag=item.find('.update-time').text())

    ```
在样本中，.update-time用作itag。如果它没有改变，请求将被丢弃。

或者，如果要重新启动所有任务，可以使用itagwith Handler.crawl_config指定脚本版本。

```
class Handler(BaseHandler):
    crawl_config = {
        'itag': 'v223'
    }
```
修改脚本后再更改itag的值，然后再次单击“运行”按钮。如果没有设置之前没关系。

#### auto_recrawl
启用后，每次都会重新绘制任务age。默认值：False

```
def on_start(self):
    self.crawl('http://www.example.org/', callback=self.callback,
               age=5*60*60, auto_recrawl=True)
```
页面将每age5小时重新启动一次。

#### 方法
要使用的HTTP方法。默认值：GET

PARAMS
要附加到URL的URL参数字典。
```
def on_start(self):
    self.crawl('http://httpbin.org/get', callback=self.callback,
               params={'a': 123, 'b': 'c'})
    self.crawl('http://httpbin.org/get?a=123&b=c', callback=self.callback)

```

这两个请求是一样的。

#### 数据
身体附加到请求。如果提供了字典，则将进行表单编码。

```
def on_start(self):
    self.crawl('http://httpbin.org/post', callback=self.callback,
               method='POST', data={'a': 123, 'b': 'c'})
```
#### 档
`{field: {filename: 'content'}}`分段上传的文件字典

#### 用户代理
请求的User-Agent

#### 头
要发送的标头字典。

#### 饼干
附加到此请求的Cookie字典。

#### connect_timeout
初始连接超时（秒）。默认值：20

#### 超时
获取页面的最长时间（以秒为单位）。默认值：120

#### allow_redirects
按照30x重定向默认值：True

#### validate_cert
对于HTTPS请求，验证服务器的证书？默认值：True

#### 代理
username:password@hostname:port要使用的代理服务器，目前仅支持http代理。

```
class Handler(BaseHandler):
    crawl_config = {
        'proxy': 'localhost:8080'
    }
```
Handler.crawl_config可用于proxy为整个项目设置代理。

#### ETAG
如果页面内容未更改，则使用HTTP Etag机制传递进程。默认值：True

#### 最后修改
如果页面内容未更改，请使用HTTP Last-Modified标头机制来传递进程。默认值：True

#### fetch_type
设置为js启用JavaScript fetcher。默认值：无

#### js_script
在页面加载之前或之后运行的JavaScript，应该被类似的函数包装function() { document.write("binux"); }。

```
def on_start(self):
    self.crawl('http://www.example.org/', callback=self.callback,
               fetch_type='js', js_script='''
               function() {
                   window.scrollTo(0,document.body.scrollHeight);
                   return 123;
               }
               ''')
```
该脚本将页面滚动到底部。函数中返回的值可以通过捕获Response.js_script_result。

#### js_run_at
运行通过js_scriptat document-start或指定的JavaScript document-end。默认：document-end

#### js_viewport_width / js_viewport_height
设置布局过程的JavaScript提取器的视口大小。

#### load_images
启用JavaScript fetcher时加载图像。默认值：False

#### 保存
一个对象传递给回调方法，可以通过访问response.save。

```
def on_start(self):
    self.crawl('http://www.example.org/', callback=self.callback,
               save={'a': 123})

def callback(self, response):
    return response.save['a']
```
123 将被退回 callback

#### 任务id
唯一id用于标识任务，默认是URL的MD5检查码，可以通过方法覆盖 def get_taskid(self, task)

```
import json
from pyspider.libs.utils import md5string
def get_taskid(self, task):
    return md5string(task['url']+json.dumps(task['fetch'].get('data', '')))

```
默认情况下，只有url是md5 -ed作为taskid，上面的代码作为taskid的data一部分添加了POST请求。

#### 强制性升级
即使任务处于ACTIVE状态，也强制更新任务参数。

#### 取消
取消任务，应该用于force_update取消活动任务。要取消auto_recrawl任务，您也应该设置auto_recrawl=False。

#### cURL命令
`self.crawl(curl_command)`

cURL是一个用于发出HTTP请求的命令行工具。它可以很容易地从Chrome Devtools> Network面板中获得，右键单击请求并“Copy as cURL”。

您可以使用cURL命令作为第一个参数self.crawl。它将解析命令并像curl一样发出HTTP请求。
```

@config（** kwargs）
self.crawl使用装饰方法作为回调时的默认参数。例如：

@config(age=15*60)
def index_page(self, response):
    self.crawl('http://www.example.org/list-1.html', callback=self.index_page)
    self.crawl('http://www.example.org/product-233', callback=self.detail_page)

@config(age=10*24*60*60)
def detail_page(self, response):
    return {...}
```
age的list-1.html是15分钟，而age的product-233.html是10天。因为回调product-233.html是detail_page，意味着它是一个detail_page共享配置detail_page。

Handler.crawl_config = {}
self.crawl整个项目的默认参数。crawl_config调度程序中的参数（priority，retries，exetime，age，itag，force_update，auto_recrawl，cancel）将在创建任务时加入，fetcher和processor的参数将在执行时连接。之后您可以使用此机制更改获取配置（例如cookie）。

```class Handler(BaseHandler):
    crawl_config = {
        'headers': {
            'User-Agent': 'GoogleBot',
        }
    }

    ...
```
crawl_config设置项目级别的用户代理。