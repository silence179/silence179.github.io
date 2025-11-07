在first.cc出现以下新名词  
  
- PointToPointHelper  
- NetDeviceContainer  
- InternetStackHelper  
- Ipv4AddressHelper  
- Ipv4InterfaceContainer  
- UdpEchoServerHelper  
- ApplicationContainer  
!!! 触发名词解释
	Helper的类都不是实例,而是一些属性,方法的集合.而Container是Helper所创建保存实际属性的的容器.  

我们首先需要一个topo结构,最简单的是点对点的网络结构,所以使用点对点的Helper类,并且设置Device的属性和Channel属性.  

$$E=mc^2$$

