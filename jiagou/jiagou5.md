### 使用结果
从WebUI下载和查看数据很方便，但可能不适合计算机。

#### 使用ResultDB
虽然resultdb仅用于结果预览，但不适合大规模存储。但是如果你想从resultdb中获取数据，有一些使用数据库API的简单片段可以帮助你连接和选择数据。

    ```
    from pyspider.database import connect_database
    resultdb = connect_database("<your resutldb connection url>")
    for project in resultdb.projects:
        for result in resultdb.select(project):
            assert result['taskid']
            assert result['url']
            assert result['result']
    ```
这result['result']是return您的脚本中的语句提交的对象。

#### 使用ResultWorker
在产品环境中，您可能希望将pyspider连接到系统/后处理管道，而不是将其存储到resultdb中。强烈建议覆盖ResultWorker。

    ```
    from pyspider.result import ResultWorker
    
    class MyResultWorker(ResultWorker):
        def on_result(self, task, result):
            assert task['taskid']
            assert task['project']
            assert task['url']
            assert result
            # your processing code goes here
    ```
result是return脚本中语句提交的对象。

您可以将此脚本（例如my_result_worker.py）放在启动pyspider的文件夹中。为result_worker子命令添加参数：

    ```
    pyspider result_worker --result-cls=my_result_worker.MyResultWorker
    ```

要么

    ```
    {
      ...
      "result_worker": {
        "result_cls": "my_result_worker.MyResultWorker"
      }
      ...
    }
    ```
如果您使用配置文件。请参阅部署

#### 设计自己的数据库架构
存储在数据库中的结果被编码为JSON以实现兼容性。强烈建议您设计自己的数据库，并覆盖上述ResultWorker。

#### 关于结果的提示
#### 想要在回调中返回多个结果？
由于resultdb通过taskid（url）重复删除结果，最新的将覆盖以前的结果。

一种解决方法是使用send_messageAPI fake为每个结果创建一个taskid。

    ```
    def detail_page(self, response):
        for li in response.doc('li').items():
            self.send_message(self.project_name, {
                ...
            }, url=response.url+"#"+li('a.product-sku').text())
    
    def on_message(self, project, msg):
        return msg
    ```
另请参见：apis / self.send_message