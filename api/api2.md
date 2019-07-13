### 响应
Response对象的属性。

#### Response.url
最终的URL。

#### Response.text
响应内容，以unicode为单位。

如果Response.encoding是None且chardet模块可用，则将猜测内容的编码。

#### Response.content
响应内容，以字节为单位。

#### Response.doc
一个PyQuery响应的内容的对象。链接默认为绝对链接。

请参阅PyQuery的文档：https：//pythonhosted.org/pyquery/

重要的是我要重复一遍，参考PyQuery的文档：https：//pythonhosted.org/pyquery/

#### Response.etree
一个LXML响应的内容的对象。

#### Response.json
响应的JSON编码内容（如果有）。

#### Response.status_code
#### Response.orig_url
如果在请求期间有任何重定向，这里是您刚刚提交的网址self.crawl。

#### Response.headers
不区分大小写的dict包含响应标头。

#### Response.cookies
#### Response.error
获取错误时的消息

#### 响应时间
提取期间使用的时间。

#### Response.ok
如果status_code是200且没有错误，则为真。

#### Response.encoding
Response.content的编码。

如果Response.encoding为None，则将通过标头或内容或chardet（如果可用）猜测编码。

手动设置内容编码将覆盖猜测的编码。

#### Response.save
self.crawlAPI 保存的对象

#### Response.js_script_result
JS脚本返回的内容

#### Response.raise_for_status（）
如果状态代码不是200或Response.error存在，则引发HTTPError 。