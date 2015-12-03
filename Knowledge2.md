# 新手必读 #

如果你已经了解(GAE)Google Application Engine的基本特点, 你应该已经知道用一个gmail帐号+绑定一个手机号注册后, 最多可以创建10个应用程序(application)实例, GAppProxy是基于GAE的, 在开始本地代理服务程序proxy.py之前, 我们必须先将一个具有代理特性的fetch.py将先上传(upload)到某一个应用程序, 然后, 才能开始启动本地代理, 基本的流程如下:

**Web Browser** <=======> **Local proxy - proxy.py** <=======>**GAE App. - fetch.py**

在阅读下文前, 我假定你已知道如何[注册GAE并创建GAE应用程序](http://appengine.google.com). 并且知道如何[上传你本地的Fetch server到你的GAE应用程序中](http://code.google.com/p/gappproxy/wiki/FAQ).

目前, GAppProxy只允许布署一个本地代理地址127.0.0.1:8080(8080是端口号)对应一个GAE应用程序, 具体设置在local\_proxy\_x.x.x(x是GAppProxy版本号)目录中proxy.inf中, 用写字板打开proxy.inf见下面代码:

```
# listen_port
listen_port = 8080

# fetch server
#fetch_server = http://your-app-server.appspot.com/fetch.py
```

你需要变化的是指定自已想代理的端口, 例如, 你想用9999, 设置成listen\_port = 9999即可. 最重要的是指定fetch server地址, 在fetch server配置行中替换your-app-server部分, 改成你自已的应用程序ID.

现在如果需要配置多个本地代理, 意味着需要设置许多不同的代理端口, 例如:

listen\_port = 8080
listen\_port = 8081
...

因此, 你的本地代理将有多个:127.0.0.1:8080, 127.0.0.1:8081 .., 并且, 你同样需要改变fetch\_server, 指向多个不同的GAE应用程序ID(你想用多个本地代理指向同一个fetch server?估计可能性为1%, 试想一下自已).
问题现在是, 在启动一个本地代理之前(运行proxy.py), 你需要不断地手工改变listen\_port和fetch\_server, 保存, 再运行多个proxy.py实例, 非常麻烦!


---


**G2Proxy\*提供了新的proxy.py文件, 并且脱离了proxy.inf的依赖, 你只需稍加修改proxy.py文件本身, 然后, 运行proxy.py即可实现多个本地代理!
```
# If Max Quota is <= 0 means unlimited outgoing traffic for each remote server,
# until that traffic is exceeding google's quota limit for that instance(remote server.
# Setting of Max Quota is benefit make your local proxy smoothly to run under ONLY ONE local proxy Mode.
# (Note: you need to first setup _USE_MORE_PROXIES = False for use this function)
# By default, it is 5MB now, however, you shouldn't set up big quota rather than GAE's limits for each instance(remote server).
_MAX_QUOTA_OF_REMOTE_SERVER = 5*1024*1024 

# Retry next remote server see if the remote server has been idling for how many seconds, then trigger up again.
_RETRY_TIMEOUT_OF_REMOTE_SERVER = 120 # in seconds

# Indicates that you want to use more proxies or not.
_USE_MORE_PROXIES = False

local_proxy_ip = "127.0.0.1"
google_apps = {
# substitutes 'your-app-server' that you created one or more..
    9000: 'http://your-app-server.appspot.com/fetch.py',
# replicates above line(including white spaces), and replace {your-app-server} part as yourself app ID.
# you will own multiple proxies now! Owww!
#   9001: 'http://your-app-server2.appspot.com/fetch.py',
#   ...
}
```**

找到google\_apps = { ... }这一部分代码行, 那里已经有一个行

`    9000: 'http://your-app-server.appspot.com/fetch.py',`

你需要改的就是`your-app-server`部分, 变成你自已的应用程序ID, 要设置多个端口对应多个不同的应用程序ID, 你只需要拷贝/粘帖这一行(注意包含前面的空格), 然后改端口号和应用程序ID, 最后, 你需要改一下这行:
`_USE_MORE_PROXIES = False`
把False改成True

注意, 你一定要预先[将本地的fetch server先上传到你所设置不同的应用程序中](http://code.google.com/p/gappproxy/wiki/FAQ).


保存后, 你就可以运行proxy.py, 开始享受用多个代理的乐趣!
最后, 别忘了在你的Firefox浏览器中使用Foxproxy插件把你那些本地代理都设好了.