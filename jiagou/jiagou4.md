### 脚本环境
#### 变量
* self.project_name
* self.project 有关当前项目的信息
* self.response
* self.task
#### 关于脚本
* 名称Handler并不重要，但您至少需要继承一个类BaseHandler
* 可以设置第三个参数来获取任务对象： def callback(self, response, task)
* 默认情况下，非200响应不会提交回调。使用@catch_status_code_error
#### 关于环境
* logging，print并将捕获例外情况。
* 您可以将其他项目作为模块导入 from projects import some_project
#### 网络视图
* 以浏览器呈现的方式查看页面（近似）
#### HTML视图
* 查看当前回调的HTML（index_page，detail_page等）
#### 遵循观点
* 查看可以从当前回调中进行的回调
* index_page跟随视图将显示可以执行的detail_page回调。
#### 消息视图
* 显示self.send_messageAPI 发送的消息。
#### 启用CSS Selector Helper
* 启用Web视图的CSS Selector Helper。它获取您单击的元素的CSS Selector，然后将其添加到您的脚本中。