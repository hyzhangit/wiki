
## 官方网站

https://servicecomb.apache.org/

## 开发指南

https://docs.servicecomb.io/java-chassis/zh_CN/

## 本地搭建

### windows

https://blog.csdn.net/zengdongwen/article/details/93486257


## 调用链

### 初始化

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651239387.png)

### 配置

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651076716.png)

### 新增

可以通过自定义Handler来拓展调用流程

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651037709.png)

### 打印Server信息

根据前面吊用链实现可看出来，在lb之后就已经为invocation设置了endpoint新消息，这个时候后续调用是可以获取到服务端信息的，那么便可以通过增加handler放置在lb后面就可以打印服务端信息了。

跟踪调用链初始化的代码，发现最后一个handler是框架指定的，固定不变。

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651474077.png)

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651520581.png)

Consumer侧最后一个Handler是TransportClientHandler，进去一看这不正是我们想要的Provider信息吗，这个可以通过输入debug信息来获取到。

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651570206.png)

![](assets/004/200/002_ServiceComb/001_ServiceComd入门.md-1622651798035.png)