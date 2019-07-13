### 关于项目
在大多数情况下，项目是您为一个网站编写的一个脚本。

* 项目是独立的，但您可以将另一个项目作为模块导入 from projects import other_project
* 一期工程有5个状态：TODO，STOP，CHECKING，DEBUG和RUNNING
    * TODO - 创建一个脚本来编写
    * STOP- 您可以将项目标记为STOP您希望它停止（= =）。
    * CHECKING- 修改正在运行的项目时，为防止不完整修改，项目状态将CHECKING自动设置。
    * DEBUG/ RUNNING- 这两种状态对蜘蛛没有区别。但是将它标记为DEBUG第一次运行然后将其更改RUNNING为检查后是很好的。
* 抓取速度被控制，rate并burst用令牌桶算法。
    * rate - 一秒钟内有多少请求
    * burst- 考虑到这种情况，rate/burst = 0.1/3这意味着蜘蛛每10秒抓一页。所有任务都已完成，项目每分钟检查最后更新的项目。假设找到3个新项目，pyspider将“爆发”并抓取3个任务而不等待3 * 10秒。但是，第四项任务需要等待10秒。
* 要删除项目，请设置group为delete和状态STOP，等待24小时。
#### on_finished 打回来
您可以on_finished在项目中覆盖方法，当task_queue变为0时将触发该方法。

示例1：当您启动项目以爬网包含100个页面的网站时，on_finished将在成功爬网100个页面或重试后失败时触发回调。

示例2：具有auto_recrawl任务的项目将永远不会触发on_finished回调，因为当其中存在auto_recrawl任务时，时间队列将永远不会变为0。

示例3：具有@every修饰方法的项目将在on_finished每次新提交的任务完成时触发回调。