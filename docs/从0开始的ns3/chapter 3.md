这一章的主要内容在second.cc中,要构建一个总线网络拓扑结构.这里需要一个带冲突检测的CSMA,来串起几个node.  
!!! Note
	这里的CSMA,即一种载波监听多路访问协议,要求站的硬件必须在传输时监听信道,而且它读取的信号如果不同于放出的信号,那么它就知道了冲突,是对ALOHA的改进协议.

```text
// Default Network Topology
//
//       10.1.1.0
// n0 -------------- n1   n2   n3   n4
//    point-to-point  |    |    |    |
//                    ================
//                      LAN 10.1.2.0
```
对于n0和n1我们还是老样子,通过Pointtopoint方式连接,不同的是,我们还要将n1到nCsma个node通过csma连接,同样我们需要CsmaHelper,设置它的DateRate和Delay