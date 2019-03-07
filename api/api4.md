### @catch_status_code_error
非200响应将被视为提取失败，不会传递给回调。使用此装饰器覆盖此功能。

```
def on_start(self):
    self.crawl('http://httpbin.org/status/404', self.callback)

@catch_status_code_error  
def callback(self, response):
    ...
```
将callback作为请求失败（具有状态码404）将不被执行。使用@catch_status_code_errordecorater，callback即使请求失败也会执行。