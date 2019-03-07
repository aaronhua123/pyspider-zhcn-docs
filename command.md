### 命令行命令

#### 全局配置
你可以获得命令行帮助通过`pyspider --help`和`pyspider all --help`来获得帮助。
全局的选项适用于所有的子命令

    ```
    Usage: pyspider [OPTIONS] COMMAND [ARGS]...
    
      A powerful spider system in python.
    
    Options:
      -c, --config FILENAME    a json file with default values for subcommands.
                               {“webui”: {“port”:5001}}
      --logging-config TEXT    logging config file for built-in python logging
                               module  [default: pyspider/pyspider/logging.conf]
      --debug                  debug mode
      --queue-maxsize INTEGER  maxsize of queue
      --taskdb TEXT            database url for taskdb, default: sqlite
      --projectdb TEXT         database url for projectdb, default: sqlite
      --resultdb TEXT          database url for resultdb, default: sqlite
      --message-queue TEXT     connection url to message queue, default: builtin
                               multiprocessing.Queue
      --amqp-url TEXT          [deprecated] amqp url for rabbitmq. please use
                               --message-queue instead.
      --beanstalk TEXT         [deprecated] beanstalk config for beanstalk queue.
                               please use --message-queue instead.
      --phantomjs-proxy TEXT   phantomjs proxy ip:port
      --data-path TEXT         data dir path
      --version                Show the version and exit.
      --help                   Show this message and exit.
    ```
      
##### --config
配置文件是一个（带有（全局或者子命令）的配置值）JSON文件

```
    {
      "taskdb": "mysql+taskdb://username:password@host:port/taskdb",
      "projectdb": "mysql+projectdb://username:password@host:port/projectdb",
      "resultdb": "mysql+resultdb://username:password@host:port/resultdb",
      "message_queue": "amqp://username:password@host:port/%2F",
      "webui": {
        "username": "some_name",
        "password": "some_passwd",
        "need-auth": true
      }
    }
    
```
##### --queue-maxsize
队列大小限制，0就是没有限制

##### --taskdb, --projectdb, --resultdb
      ```
        mysql:
        mysql+type://user:passwd@host:port/database
    sqlite:
        # relative path
        sqlite+type:///path/to/database.db
        # absolute path
        sqlite+type:////path/to/database.db
        # memory database
        sqlite+type://
    mongodb:
        mongodb+type://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
        more: http://docs.mongodb.org/manual/reference/connection-string/
    sqlalchemy:
        sqlalchemy+postgresql+type://user:passwd@host:port/database
        sqlalchemy+mysql+mysqlconnector+type://user:passwd@host:port/database
        more: http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html
    local:
        local+projectdb://filepath,filepath
    
    type:
        should be one of `taskdb`, `projectdb`, `resultdb`.
      ```
        
        
##### --phantomjs-proxy
phantomjs代理地址，你需要一个安装包和运行phantomjs代理用命令：`pyspider phantomjs`

##### --data-path
SQLite数据库 和 计算器转储文件 的保存路径

#### all
    ```
    Usage: pyspider all [OPTIONS]
    
      Run all the components in subprocess or thread
    
    Options:
      --fetcher-num INTEGER         instance num of fetcher
      --processor-num INTEGER       instance num of processor
      --result-worker-num INTEGER   instance num of result worker
      --run-in [subprocess|thread]  run each components in thread or subprocess.
                                    always using thread for windows.
      --help                        Show this message and exit.
    ```
      
#### one
    ```
    Usage: pyspider one [OPTIONS] [SCRIPTS]...
    
      One mode not only means all-in-one, it runs every thing in one process
      over tornado.ioloop, for debug purpose
    
    Options:
      -i, --interactive  enable interactive mode, you can choose crawl url.
      --phantomjs        enable phantomjs, will spawn a subprocess for phantomjs
      --help             Show this message and exit.
    ```
**注意：WebUI不是在one模式下运行的**

在one模式下，结果默认是写在标准输出上。你可以捕捉他们通过pyspider one > result.txt

#### [SCRIPTS]
项目的脚本文件路径。当项目在运行的时候，比率和并发可以通过脚本命令去设置。

    # rate: 1.0
    # burst: 3
当脚本设置好了，默认任务数据库和结构数据库将使用一个内存数据库（可以通过全局的配置`--taskdb``--resltdb`虚拟出来）。on_start回调将会触发在启动时候。

#### -i,--interactive
使用交互式的模型，pyspider将会启动一个交互式的控制台请求，那个将要在下一个进程循环中做的。在控制台，你可以使用：

        ```
        crawl(url, project=None, **kwargs)
        Crawl given url, same parameters as BaseHandler.crawl
        
        url - url or taskid, parameters will be used if in taskdb
        project - can be omitted if only one project exists.
        
        quit_interactive()
        Quit interactive mode
        
        quit_pyspider()
        Close pyspider
        ```
你可以使用`pyspider.libs.utils.python_console()`去打开一个交互式控制台，在你的脚本中。

#### bench
    ```
    Usage: pyspider bench [OPTIONS]
    
      Run Benchmark test. In bench mode, in-memory sqlite database is used
      instead of on-disk sqlite database.
    
    Options:
      --fetcher-num INTEGER         instance num of fetcher
      --processor-num INTEGER       instance num of processor
      --result-worker-num INTEGER   instance num of result worker
      --run-in [subprocess|thread]  run each components in thread or subprocess.
                                    always using thread for windows.
      --total INTEGER               total url in test page
      --show INTEGER                show how many urls in a page
      --help                        Show this message and exit.
    ```
#### scheduler
    ```
    Usage: pyspider scheduler [OPTIONS]

      Run Scheduler, only one scheduler is allowed.
    
    Options:
      --xmlrpc / --no-xmlrpc
      --xmlrpc-host TEXT
      --xmlrpc-port INTEGER
      --inqueue-limit INTEGER  size limit of task queue for each project, tasks
                               will been ignored when overflow
      --delete-time INTEGER    delete time before marked as delete
      --active-tasks INTEGER   active log size
      --loop-limit INTEGER     maximum number of tasks due with in a loop
      --scheduler-cls TEXT     scheduler class to be used.
      --help                   Show this message and exit.  
    ```      
#### --scheduler-cls
设置选项去使用自定义的调度类

#### phantomjs
    ```
    Usage: run.py phantomjs [OPTIONS] [ARGS]...

      Run phantomjs fetcher if phantomjs is installed.
    
    Options:
      --phantomjs-path TEXT  phantomjs path
      --port INTEGER         phantomjs port
      --auto-restart TEXT    auto restart phantomjs if crashed
      --help                 Show this message and exit.
    ```
#### ARGS
添加args到phantomjs命令行

#### fetcher
    ```
    Usage: pyspider fetcher [OPTIONS]

      Run Fetcher.
    
    Options:
      --xmlrpc / --no-xmlrpc
      --xmlrpc-host TEXT
      --xmlrpc-port INTEGER
      --poolsize INTEGER      max simultaneous fetches
      --proxy TEXT            proxy host:port
      --user-agent TEXT       user agent
      --timeout TEXT          default fetch timeout
      --fetcher-cls TEXT      Fetcher class to be used.
      --help                  Show this message and exit.
    ```
    
    
      
##### --proxy
默认的代理使用fetcher，选项可以被self.crawl重写。

####  processor
   ```
    Usage: pyspider processor [OPTIONS]

      Run Processor.
    
    Options:
      --processor-cls TEXT  Processor class to be used.
      --help                Show this message and exit.
   ```
      
      
#### result_worker
    Usage: pyspider result_worker [OPTIONS]
    
      Run result worker.
    
    Options:
      --result-cls TEXT  ResultWorker class to be used.
      --help             Show this message and exit.
      
#### webui

    ```
    Usage: pyspider webui [OPTIONS]
    
      Run WebUI
    
    Options:
      --host TEXT            webui bind to host
      --port INTEGER         webui bind to host
      --cdn TEXT             js/css cdn server
      --scheduler-rpc TEXT   xmlrpc path of scheduler
      --fetcher-rpc TEXT     xmlrpc path of fetcher
      --max-rate FLOAT       max rate for each project
      --max-burst FLOAT      max burst for each project
      --username TEXT        username of lock -ed projects
      --password TEXT        password of lock -ed projects
      --need-auth            need username and password
      --webui-instance TEXT  webui Flask Application instance to be used.
      --help                 Show this message and exit.
      ```
      
      
##### --cdn
JS/CSS 基于 CDN 服务的。URL必须兼容[cdnjs](https://cdnjs.com/)

##### --fetcher-rpc
fetcher XMLRPC 服务器 的 XML-RPC 的路径 URI。如果不设置,使用使用 Fetcher 实例.

#### --need-auth
如果为真，通过`--username``--password`，所有页面的请求都将被指定。
