### self.send_message
#####self.send_message（project，msg，[url]）
将消息发送到其他项目。可以通过def on_message(self, project, message)回调接收。

* project - 其他项目名称
* msg - 任何json-able对象
* url- 如果相同，结果将被覆盖taskid。默认情况下send_message共享相同taskid。更改此选项可通过一个响应返回多个结果。
```
def detail_page(self, response):
    for i, each in enumerate(response.json['products']):
        self.send_message(self.project_name, {
                "name": each['name'],
                'price': each['prices'],
             }, url="%s#%s" % (response.url, i))

def on_message(self, project, msg):
    return msg
```
##### pyspider send_message [OPTIONS] PROJECT MESSAGE
您也可以从命令行发送消息。

```
Usage: pyspider send_message [OPTIONS] PROJECT MESSAGE

  Send Message to project from command line

Options:
  --scheduler-rpc TEXT  xmlrpc path of scheduler
  --help                Show this message and exit.
```
##### def on_message（自我，项目，消息）
从其他项目接收消息