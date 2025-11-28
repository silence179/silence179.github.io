## Logging 的层级
- LOG_ERROR — Log error messages (associated macro: NS_LOG_ERROR);  
- LOG_WARN — Log warning messages (associated macro: NS_LOG_WARN);  
- LOG_DEBUG — Log relatively rare, ad-hoc debugging messages (associated macro: NS_LOG_DEBUG);   
- LOG_INFO — Log informational messages about program progress (associated macro: NS_LOG_INFO);   
- LOG_FUNCTION — Log a message describing each function called (two associated macros: NS_LOG_FUNCTION, used for member functions, and NS_LOG_FUNCTION_NOARGS, used for static functions);   
- LOG_LOGIC – Log messages describing logical flow within a function (associated macro: NS_LOG_LOGIC);   
- LOG_ALL — Log everything mentioned above (no associated macro).  
!!! Note
	高等级的Log层级是覆盖低等级的层级的  
不仅仅可以在程序中修改参数,在命令行中修改环境变量:  
这里用''包裹是为了避免|被解释成管道运算符
```bash
$ export 'NS_LOG=UdpEchoClientApplication=level_all|prefix_func:
               UdpEchoServerApplication=level_all|prefix_func'
```
我们程序运行同样会打印出这样的效果,参数`prefix_func`可用于溯源某些事件是由哪些函数触发的,类似的还有time,node,level,all等.在程序中都是大写的宏,而在环境变量中都是小写的.   
## 自定义log
自定义log是由`NS_LOG_COMPONENT_DEFINE("FirstScriptExample");`先定义出自定义的宏来,然后通过`LogComponentEnable`来启用文件中的相关log信息.  
当然只需要将NS_LOG export成一个空字符串就可以取消之前的设置  
同样的,自定义的log信息也有多个信息:   

- NS_LOG_ERROR(msg) NS_LOG(ns3::LOG_ERROR, msg)  
- NS_LOG_WARN(msg) NS_LOG(ns3::LOG_WARN, msg)  
- NS_LOG_DEBUG(msg) NS_LOG(ns3::LOG_DEBUG, msg)  
- NS_LOG_INFO(msg) NS_LOG(ns3::LOG_INFO, msg)  
- NS_LOG_LOGIC(msg) NS_LOG(ns3::LOG_LOGIC, msg)  

同样的环境变量也可以启用  
```bash
$ export NS_LOG=FirstScriptExample=info
```
通过像上方的指令可以启用自定义的log信息.
在enable的时候通过不同的参数来启用不同的log信息.  
## 命令行参数
我们的main函数中有如下的两行:
```
  CommandLine cmd;
  cmd.Parse(argc, argv);
```
这两行和命令行的参数传递有关系,我们可以让系统打印出一些信息.  
```bash
General Arguments:
  --PrintGlobals:              Print the list of globals.
  --PrintGroups:               Print the list of groups.
  --PrintGroup=[group]:        Print all TypeIds of group.
  --PrintTypeIds:              Print all TypeIds.
  --PrintAttributes=[typeid]:  Print all attributes of typeid.
  --PrintVersion:              Print the ns-3 version.
  --PrintHelp:                 Print this help message.
```
这些是print的参数,当我们输入形如这样的参数:
```bash
$ ./ns3 run "scratch/myfirst --PrintAttributes=ns3::PointToPointNetDevice"
```

我们的PointToPointNetDevice参数会被打印出来:
```bash
Attributes for TypeId ns3::PointToPointNetDevice
    --ns3::PointToPointNetDevice::Address=[ff:ff:ff:ff:ff:ff]
        The MAC address of this device.
    --ns3::PointToPointNetDevice::DataRate=[32768bps]
        The default data rate for point to point links
    --ns3::PointToPointNetDevice::InterframeGap=[+0ns]
        The time to wait between packet (frame) transmissions
    --ns3::PointToPointNetDevice::Mtu=[1500]
        The MAC-level Maximum Transmission Unit
    --ns3::PointToPointNetDevice::ReceiveErrorModel=[0]
        The receiver error model used to simulate packet loss
    --ns3::PointToPointNetDevice::TxQueue=[0]
        A queue to use as the transmit queue in the device.

```
这些都是未被修改的默认参数.  
我们甚至可以直接通过命令行参数来修改各种运行时参数的值.
```bash
$ ./ns3 run "scratch/myfirst
  --ns3::PointToPointNetDevice::DataRate=5Mbps
  --ns3::PointToPointChannel::Delay=2ms
  --ns3::UdpEchoClient::MaxPackets=2"
```
这里没有通过调用Public函数,而是修改具体的类中的属性,不过如果不熟悉各种typeid,我们可以设置我们自己的变量在程序中.这里的第一个参数是变量的表示名,第二个是帮助信息,第三个就是变量了.
```cpp
uint32_t nPackets = 1;
cmd.AddValue("nPackets", "Number of packets to echo", nPackets);
```
这样我们可以在命令行中通过自己的参数名来改变变量值.
```bash
$ ./ns3 run "scratch/myfirst --nPackets=2"
```
这样我们echo了两个包:
```bash
./ns3 run "scratch/myfirst --nPackets=2"
[0/2] Re-checking globbed directories...
[2/3] Linking CXX executable /home/silence/myfiles/ns-3.46.1/build/scratch/ns3.46.1-myfirst-debug
[0/2] Re-checking globbed directories...
ninja: no work to do.
At time +2s client sent 1024 bytes to 10.1.1.2 port 9
At time +2.00369s server received 1024 bytes from 10.1.1.1 port 49153
At time +2.00369s server sent 1024 bytes to 10.1.1.1 port 49153
At time +2.00737s client received 1024 bytes from 10.1.1.2 port 9
At time +3s client sent 1024 bytes to 10.1.1.2 port 9
At time +3.00369s server received 1024 bytes from 10.1.1.1 port 49153
At time +3.00369s server sent 1024 bytes to 10.1.1.1 port 49153
At time +3.00737s client received 1024 bytes from 10.1.1.2 port 9
```

## Use tracing
这里主要是用一些内置的trace,还是通过enable的方式来启用trace.  
同样的,我们需要创建helper类来创建对象.
```cpp
AsciiTraceHelper ascii;
pointToPoint.EnableAsciiAll(ascii.CreateFileStream("myfirst.tr"));
```
这样会启用trace,所有的细节信息会被重定向到一个名为myfirst.tr的文件中,这里的默认路径是根目录.可以通过 --cwd参数来放到当前工作目录来.