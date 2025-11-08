!!! Note
	所有内容是基于[ns3的Tutorial](https://www.nsnam.org/docs/release/3.46/tutorial/html/index.html)的总结
## example开始的ns3
在first.cc出现以下新名词  
  
- PointToPointHelper  
- NetDeviceContainer  
- InternetStackHelper  
- Ipv4AddressHelper  
- Ipv4InterfaceContainer  
- UdpEchoServerHelper  
- ApplicationContainer  
- NS_LOG_COMPONENT_DEFINE
- LogComponentEnable

!!! Note
	Helper的类都不是实例,而是一些属性,方法的集合.而Container是Helper所创建保存实际属性的的容器.  
	对于log相关的放在chapter2中

我们首先需要一个topo结构,最简单的是点对点的网络结构,所以使用点对点的Helper类,并且设置Device的属性和Channel属性.  
```cpp
	PointToPointHelper pointToPoint;
    pointToPoint.SetDeviceAttribute("DataRate", StringValue("5Mbps"));
    pointToPoint.SetChannelAttribute("Delay", StringValue("2ms"));
```
然后用install方法,将nodes加载到整个结构中.  
对于每个节点(node),我们当然还需要加载它们的整个网络栈:
```cpp
	InternetStackHelper stack;
    stack.Install(nodes);
```
网络的通信还需要ip地址,而地址的分配关系也是我们要指定的属性之一.而这些我们要创建一个Helper来指定.通常情况下,地址的分配会按顺序一个一个分配,比如10.1.1.0,10.1.1.1.而且如果在复杂情况下如果分配了相同的ip地址,会导致发生fatal error,并且难以debug.
```cpp
	Ipv4AddressHelper address;
    address.SetBase("10.1.1.0", "255.255.255.0");
```
## 设置application
准备工作完成了,接下来是我们要给node设置application,在first.cc中就是最基础的udp的serve-client模型  
但是对于这些application都是需要指定一些基本信息的,所以同样的需要Helper类,这里Server的初始化传入的是端口号,client的初始化是目标的ip以及端口.  
```cpp
    UdpEchoServerHelper echoServer(9);

    ApplicationContainer serverApps = echoServer.Install(nodes.Get(1));
    serverApps.Start(Seconds(1));
    serverApps.Stop(Seconds(10));

    UdpEchoClientHelper echoClient(interfaces.GetAddress(1), 9);
    echoClient.SetAttribute("MaxPackets", UintegerValue(1));
    echoClient.SetAttribute("Interval", TimeValue(Seconds(1)));
    echoClient.SetAttribute("PacketSize", UintegerValue(1024));

    ApplicationContainer clientApps = echoClient.Install(nodes.Get(0));
    clientApps.Start(Seconds(2));
    clientApps.Stop(Seconds(10));

```
## 模拟的开始与资源销毁
```cpp
	Simulator::Run();
    Simulator::Destroy();
	return 0;
```
这一部分比较简单,也容易理解.这里其实可以传入一个`Simulator::Stop(Seconds(11))`之类的,但是事件队列消耗完会自动的结束,所以可以不用显式的写出来.


