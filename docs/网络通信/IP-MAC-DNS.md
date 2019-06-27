### 1、IP地址
* IP地址是每台计算机在网络中的唯一标识。采用“点分十进制”的方式来记忆IP地址。
举例：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;192.168.32.80 
注意：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;计算机在网络中的唯一标识肯定不是这个样子的。 所以，我们要把这个十进制的数据转换为二进制： 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11000000 10101000 00100000 01010000
* IP地址的组成：
网络号+主机号段
A:第一段表示网络号段，后三段表示主机号码。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;网络号码：256*256*256 16777216
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://blog.csdn.net/ben0133/article/details/3860265  IP号分配情况
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第17次中国互联网络发展状况统计报告数据：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;中国大陆地区有 4 个 A 段的 IPv4 地址。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;港澳台地区都没有 A 段的 IPv4 地址。
B:前2段表示网络号段，后2段表示主机号码。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;网络号码：256*256 65536
C:前三段是网络号码，后1段表示主机号码：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;网络号码：256
* IP地址的分类：
A:0.0.0.0--127.255.255.255
B:128.0.0.0--191.255.255.255
C:192.0.0.0--223.255.255.255     ---------最常见的
D:保留
E:保留
* 常用的局域网IP：
192.168.x.x
10.x.x.x
端口号:
有范围的：
0-65535之间。
0-1024之间不要随意使用，因为它被保留或者被系统进程占用。
可以通过360查看端口号。
### 2、MAC地址
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;位于OSI七层网络协议的第二层---数据链路层。通常表示为12个16进制数，每2个16进制数之间用冒号隔开。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;举例：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;08:00:20:0A:8C:6D
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;前6位16进制数08:00:20代表网络硬件制造商的编号，后3位16进制数0A:8C:6D代表该制造商所制造的某个网络产品（如网卡）的系列号。
ipconfig -all 查看计算机物理地址。
### 3、IP地址和MAC地址的区别
* ip地址是服务商给你的，mac地址是你的网卡物理地址；
* ip地址局域网内可以随便更改，但是mac地址一般不能更改；
* 长度不同。IP地址为32位，MAC地址为48位；
* 寻址协议层不同。IP地址应用于OSI第三层，即网络层，而MAC地址应用在OSI第二层，即数据链路层。

### 4、MAC地址在网络通信中的作用：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;IP 间的通信依赖 MAC 地址。在网络上，通信的双方在同一局域网（LAN）内的情况是很少的，通常是经过多台计算机和网络设备中转才能连接到对方。而在进行中转时，会       利用下一站中转设备的 MAC 地址来搜索下一个中转目标。这时，会采用 ARP 协议（Address Resolution Protocol）。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ARP 是一种用以解析地址的协议，根据通信方的 IP 地址就可以反查出对应的 MAC 地址。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;假设网络上要将一个数据包（名为PAC）由北京的一台主机（名称为A，IP地址为IP_A，MAC地址为MAC_A）发送到华盛顿的一台主机（名称为B，       IP地址为IP_B，MAC
地址为MAC_B）。这两台主机之间不可能是直接连接起来的，因而数据包在传递时必然要经过许多中间节点（如路由器，服务器       等等），我们假定在传输过程中要经过C1、
C2、C3（其MAC地址分别为M1，M2，M3）三个节点。A在将PAC发出之前，先发送一个ARP请求，找到其要到达IP_B所必须经历的第一个中间节点C1的MAC地址M1，然后在其数据包中封装（Encapsulation）这些地址：IP_A、IP_B，MAC_A和M1。当PAC传到C1后，再由               ARP根据其目的IP地址IP_B，找到其要经历的第二个中间节点C2的MAC地址M2，然后再将带有M2的数据包传送到C2。如此类推，直到最后找到带有IP地址为IP_B的B主机的地MAC_B，最终传送给主机B。在传输过程中，IP_A、IP_B和MAC_A不变，而中间节点的MAC地址通过ARP在不断改变（M1，M2，M3），直至目的地址MAC_B。
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/IP-MAC.png)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**通俗的理解IP和MAC**：IP相当于小区单元楼里的门牌号，MAC相当于人的身份证号。
### 5、UDP/TCP协议
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;TCP(Transmission Control Protocol 传输控制协议)：是一种面向连接的、可靠的、基于字节流的传输层通信协议，使用三次握手协议建立连接、四次挥手断开连接。面向连接意味着两个使用TCP的应用(通常是一个客户端和一个服务器)在彼此交换数据包之前必须先建立一个TCP连接。在一个TCP连接中，仅有两方进行彼此通信，广播和多播不能用TCP。
UDP(User Datagram Protocol 用户数据报协议)：是OSI参考模型中一种无连接的传输层协议，提供面向事务的简单但是不可靠信息传送服务。

传输层协议：
---UDP(效率)
| --    不建立连接。
| --    数据不可靠。
| --    把数据打包。    
| --    速度快。
| --    数据有限制。64K。
| --       64k = 64 * 1024 byte = 64 * 1024 /2 = 32768 汉字
---TCP(安全)
| --     建立连接通道。三次握手协议。
| --     数据可靠。
| --     数据没有限制。
| --     速度慢。
一般的应用程序既有UDP协议还有TCP协议。
### 6、DNS
* DNS 的全称是 Domain Name System，主要的作用就是将网址 (域名) “翻译”成电脑可以理解的 IP 地址，这个过程叫做 DNS 域名解析。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;比如 http://www.baidu.com  ===》对应的ip地址 202.108.22.5
* 域名结构：
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/域名结构.jpg)

顶级域名是域名的最后一个部分，即是域名最后一点之后的字母，例如在http://example.com这个域名中，顶级域是.com（或.COM），大小写视为相同。
二级域名是域名的倒数第二个部分，例如在http://example.com这个域名中，二级域名是example。以此类推。
主机名.次级域名.顶级域名.根域名
* 域名服务器的分类：
因特网上的DNS服务器按照层次安排，根据不同层次的域名服务器所起的作用，可以把域名服务器分为4种不同的类型。
**（1）根域名服务器：（国家主席）**
最高层次的域名服务器(就好像国家总理一样)，也是最重要的域名服务器。全球有13个根域名服务器名称，a.root-servers.net. ~ m.root-servers.net.
**（2）顶级域名服务器（TLD-- Top Level Domain  省长）**
**（3）权威域名服务器（市长、镇长、乡长）**：权威域名服务器通常是指顶级域名以下的管理二级、三级、四级等域名的服务器。
**（4）本地域名服务器**

DNS工作路流程：
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/DNS.jpg)
