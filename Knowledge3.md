# 简介 #

本文前提是你已知道如何[用G2Proxy布署多个本地代理](http://code.google.com/p/g2proxy/wiki/Knowledge2).

由于受到GAE配额(Quota)的限制, 当一个本地代理A连续使用一段时间后, A所对应的
Fetch server应用程序的配额(Quota)可能受到限制, 换句话说是你的下载量可能超过了GAE限制, 而出现Service Unavailable 的错误,
造成本地代理A不能继续使用, 因此, 你必须手工切换到下一个已设置好的本地代理B.

那有没有办法, 让应用程序配额即将耗完前就自动切换到下一个已设置好的本地代理B呢? 答案是肯定的, 下面我将介绍如何仅布署一个高性能的单个本地代理, 去托管你所拥有多个GAE应用程序, 并且那些应用程序应该已经预先布署好了[Fetch Server](http://code.google.com/p/gappproxy/wiki/FAQ).


# Let's go! #

用写字板打开Proxy.py文件, 找到下面这些代码:

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
```

我假定你已经通过google\_apps代码块设置好了多个本地代理端口和相对应的多个应有程序ID.appspot.com/fetch.py

现在将`_USE_MORE_PROXIES`配置成False(注意F大写!), 意思是你现在不用多个本地代理, 而改成用单个本地代理模式, 并启用自动切换"多个代理"模式, 这里所说的"自动切换"并不是切换实际已运行的多个本地代理, 而是启动的单个本地代理将自动托管使用多个Fetch server(一个Fetch Server对应一个GAE应用程序).

将`_MAX_QUOTA_OF_REMOTE_SERVER`改成你自定义的最大配额(Quota)数量, 默认是`5MB = 5*1024*1024`, 例如, 当前本地代理托管了Fetch Server A, 如果使用Fetch Server A的配额超过了5MB , 然后, 本地代理就自动切换并托管到下一个Fetch Server B(如果可能, 否则, 继续找从已有配置google\_apps中).

通常, 一个Fetch Server(或者说是一个GAE应用程序)有200MB的最大引出(Outgoing)流量配额(1小时内), 如果超出了这个配额, 该Fetch Server就不能用了, 设定`_MAX_QUOTA_OF_REMOTE_SERVER`不需要真的设置到200MB, 现在是5MB, 也就意味着当前托管的Fetch Server如果下载流量超了5MB, 本地代理将自动切换到另一个Fetch Server, 这样就提高了多个Fetch Server(多个GAE应用程序)的利用率, 并防止某个Fetch Server长时间连续有过高的下载量, 而导致该Fetch Server不可用的状态.

设置`_MAX_QUOTA_OF_REMOTE_SERVER` 如果小于等于0, 表示该参数失效, 因此, 当某个Fetch Server真的达到GAE的配额限制后才自动切换到下一个Fetch Server.

值得一提的是, 另一个参数:

`_RETRY_TIMEOUT_OF_REMOTE_SERVER`

它的意思是如果本地代理正在托管的Fetch Server不可用时, 会将该Fetch Server先记忆起来, 真到超时X秒后, 才能再次"可用"(重试成功后才是真可用).