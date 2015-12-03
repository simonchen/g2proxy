<a href='http://code.google.com/p/gappproxy/'>GAppProxy</a> is a great local proxy engine created on GAE(google application engine).
<strong>G2Proxy</strong> aims to extend more functions from <a href='http://code.google.com/p/gappproxy/'>GAppProxy</a>. Currently, I'm focus on improve the client proxy program and make it easily to serve for multiple google application instances.

<strong>Installation instructions:</strong>

when you get the enhanced client proxy program from here, you also need to get <a href='http://code.google.com/p/gappproxy/'>GAppProxy</a>, then you simply substitute the old proxy.py in 'local\_proxy\_x.x.x' folder.

G2Proxy是基于GAppProxy的， 目前， 只改写了其客户端proxy.py， 可以配置支持多个代理托管多个Fetch Server(GAE 应用程序），也可以配置单个代理托管多个Fetch Server, 达到高效利用每个Fetch Server的配额(流量），举例，好处是，你看网上比较大的视频时不会再断断续续了，目前还在进一步改进中。

More details see
### [为什么需要多个本地代理服务器](http://code.google.com/p/g2proxy/wiki/Knowledge1) ###

### [怎样用G2Proxy布署多个本地代理 ](http://code.google.com/p/g2proxy/wiki/Knowledge2) ###

### [布署高性能的单个本地代理, 托管多个GAE应用程序](http://code.google.com/p/g2proxy/wiki/Knowledge3) ###

<strong>Key features:</strong>

1. Multiple local proxies corresponds to multiple app. instances deployed on GAE.

2. One local proxy corresponds to multiple app. instances deployed on GAE.
