对于GAE(Google Application Engine)的免费用户, 由于GAE对于配额(Quota)的限制, 在某段时间如果你的引用(Incoming)和引出(Outgoing)流量太大, 你所设定好的单个本地[GAppProxy](http://code.google.com/p/gappproxy)代理可能会失效1到2小时,然后,才能继续再使用!
因此, 我们现在需要设置多个本地GAppProxy代理, 当某一个代理不能使用时, 我们可以手工或自动地切换到用下一个代理.