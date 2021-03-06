# 开篇词
- 大家经常说的四层、七层，分别指的是什么？
- TCP 三次握手是什么，TIME_WAIT 是怎么发生的？CLOSE_WAIT 又是什么状态？
- Linux 下的 epoll 解决的是什么问题？如何使用 epoll 写出高性能的网络程序？
- 什么是网络事件驱动模型？Reactor 模式又是什么？
- 流量控制和拥塞控制
- 进程、线程、多路复用、异步 I/O

学习高性能网络编程，掌握两个核心要点：第一就是`理解网络协议`，并在这个基础上和操作系统内核配合，感知各种网络 I/O 事件；第二就是学会使用线程处理并发。

要学好网络编程，需要达到以下三个层次：
- 第一个层次，充分理解 TCP/IP 网络模型和协议，梳理 TCP/IP 模型和网络函数接口之间的联系，通过实例展开对套接字，套接字缓冲区，拥塞控制，数据包和数据流，本地套接字（UNIX 域套接字）等的讨论
- 第二个层次，结合对协议的理解，增强对各种异常情况的优雅处理能力。比如对 TCP 数据流的处理，半关闭的连接，TCP 连接有效性的侦测，处理各种异常情况等，这些问题决定了程序的健壮性。
- 第三个层次，写出可以支持大规模高并发的网络处理程序。深入研究 C10K 问题，引入进程、线程、多路复用、非阻塞、异步、事件驱动等现代高性能网络编程所需要的技术。

# TCP/IP和Linux
人类历史上还从来没有一项技术可以像互联网一样深刻地影响人们生活的方方面面。而具体到互联网技术里，有两件事最为重要，一个是 TCP/IP 协议，它是万物互联的事实标准；另一个是 Linux 操作系统，它是推动互联网技术走向繁荣的基石。

## TCP/IP 网络分层模型
![image](https://user-images.githubusercontent.com/34932312/76820045-e5c24380-6844-11ea-944e-c7625497d5e1.png)
TCP/IP 协议总共有四层，就像搭积木一样，每一层需要下层的支撑，同时又支撑着上层，任何一层被抽掉都可能会导致整个协议栈坍塌。注意它的层次顺序是“从下往上”数的，所以第一层就是最下面的一层。
- 第一层叫“链接层”（link layer） 负责在以太网、WiFi 这样的底层网络上发送原始数据包，工作在`网卡`这个层次，使用`MAC地址来标记网络上的设备`，所以有时候也叫 MAC 层。
- 第二层叫“网际层”或者“网络互连层”（internet layer），`IP协议`就处在这一层。因为IP协议定义了“IP 地址”的概念，所以就`可以在“链接层”的基础上，用IP地址取代MAC地址`，把许许多多的局域网、广域网连接成一个虚拟的巨大网络，在这个网络里找设备时只要把 IP 地址再“翻译”成 MAC 地址就可以了。
- 第三层叫“传输层”（transport layer），`这个层次协议的职责是保证数据在 IP 地址标记的两点之间“可靠”地传输`，是 TCP 协议工作的层次，另外还有它的一个“小伙伴”UDP。
    - TCP 是一个有状态的协议，需要先与对方`建立连接`然后才能发送数据，而且`保证数据不丢失不重复`。而 UDP 则比较简单，它`无状态`，`不用事先建立连接`就可以任意发送数据，但`不保证数据一定会发到对方`。两个协议的另一个重要区别在于数据的形式。TCP 的数据是`连续的“字节流”`，`有先后顺序`，而 UDP 则是`分散的小数据包，是顺序发，乱序收`。
- 第四层叫“应用层”（application layer），由于下面的三层把基础打得非常好，所以在这一层就“百花齐放”了，有各种面向具体应用的协议。例如 Telnet、SSH、FTP、SMTP 等等，当然还有我们的 HTTP。    
- MAC 层的传输单位是帧（frame），IP 层的传输单位是包（packet），TCP 层的传输单位是段（segment），HTTP 的传输单位则是消息或报文（message）。但这些名词并没有什么本质的区分，可以统称为数据包。

## OSI 网络分层模型
OSI，全称是“开放式系统互联通信参考模型”（Open System Interconnection Reference Model），由国际标准组织（ISO）提出，OSI 模型分成了七层，部分层次与 TCP/IP 很像，从下到上分别是：
![image](https://user-images.githubusercontent.com/34932312/76820392-f626ee00-6845-11ea-9d59-f8910fe56f40.png)
- 第一层：物理层，网络的物理形式，例如电缆、光纤、网卡、集线器等等
- 第二层：数据链路层，它基本相当于 TCP/IP 的链接层
- 第三层：网络层，相当于 TCP/IP 里的网际层
- 第四层：传输层，相当于 TCP/IP 里的传输层
- 第五层：会话层，维护网络中的连接状态，即保持会话和同步
- 第六层：表示层，把数据转换为合适、可理解的语法和语义
- 第七层：应用层，面向具体的应用传输数据。

## 两个分层模型的映射关系
两个网络分层模型：TCP/IP 和 OSI，一个是四层模型，一个是七层模型
![image](https://user-images.githubusercontent.com/34932312/76833225-e919f700-6865-11ea-961b-137f8d6b73c1.png)
- 第一层：物理层，TCP/IP 里无对应
- 第二层：数据链路层，对应 TCP/IP 的链接层
- 第三层：网络层，对应 TCP/IP 的网际层
- 第四层：传输层，对应 TCP/IP 的传输层
- 第五、六、七层：统一对应到 TCP/IP 的应用层。

## 四层负载均衡和七层负载均衡
- 所谓的“四层负载均衡”就是指工作在传输层上，使用`IP加端口`的方式进行路由转发，实现对后端服务器的负载均衡。
    - 以常见的TCP为例，负载均衡设备在接收到第一个来自客户端的SYN请求时，即通过上述方式选择一个最佳的服务器，并`对报文中目标IP地址进行修改(改为后端服务器IP）`，直接转发给该服务器。TCP的连接建立，即`三次握手是客户端和服务器直接建立的`，`负载均衡设备只是起到一个类似路由器的转发动作`。在某些部署情况下，为保证服务器回包可以正确返回给负载均衡设备，在转发报文的同时可能还会对报文原来的源地址进行修改。

- 所谓的“七层负载均衡”就是指工作在应用层上，看到的是 HTTP 协议，解析 HTTP 报文里的 URI、主机名、资源类型等数据，再用适当的策略转发给后端服务器。
    - 以常见的TCP为例，负载均衡设备如果要根据真正的应用层内容再选择服务器，只能先代理最终的服务器和客户端建立连接(三次握手)后，才可能接受到客户端发送的真正应用层内容的报文，然后再根据该报文中的特定字段，再加上负载均衡设备设置的服务器选择方式，决定最终选择的内部服务器。负载均衡设备在这种情况下，更类似于一个代理服务器。负载均衡和前端的客户端以及后端的服务器会分别建立TCP连接。所以从这个技术原理上来看，七层负载均衡明显的对负载均衡设备的要求更高，处理七层的能力也必然会低于四层模式的部署方式。
      
![image](https://user-images.githubusercontent.com/34932312/76834362-04860180-6868-11ea-8ab7-f17199004d90.png)

参考文章：https://www.cnblogs.com/kevingrace/p/6137881.html

## TCP/IP 协议栈的工作方式
HTTP 协议的传输过程就是这样通过协议栈逐层向下，每一层都添加本层的专有数据，层层打包，然后通过下层发送出去。接收数据则是相反的操作，从下往上穿过协议栈，逐层拆包，每层去掉本层的专有头，上层就会拿到自己的数据。但下层的传输过程对于上层是完全“透明”的，上层也不需要关心下层的具体实现细节，所以就 HTTP 层次来看，它不管下层是不是 TCP/IP 协议，看到的只是一个可靠的传输链路，只要把数据加上自己的头，对方就能原样收到。
![image](https://user-images.githubusercontent.com/34932312/76835055-3c417900-6869-11ea-977d-3957a3cb8d0d.png)

## homework
- 二层转发：设备工作在链路层，帧在经过交换机设备时，检查帧的头部信息，拿到目标mac地址，进行本地转发和广播
- 三层路由：设备工作在ip层，报文经过有路由功能的设备时，设备分析报文中的头部信息，拿到ip地址，根据网段范围，进行本地转发或选择下一个网关
- dns，网络请求的第一步是域名解析，所以工作在应用层
- DNS 属于应用层，其本身并不负责传输、网际的实际操作，只是将域名解析为 IP，方便下一层的使用

## TCP/IP 的成功不是偶然的
TCP/IP 的成功不是偶然的，而是综合了几个因素后的结果：
- TCP/IP 是免费或者是少量收费的，这样就扩大了使用人群
- TCP/IP 搭上了 UNIX 这辆时代快车，很快推出了`基于套接字（socket）的实际编程接口`
- 这是最重要的一点，TCP/IP 来源于实际需求，大家都在翘首盼望出一个统一标准，可是在此之前实际的问题总要解决啊，TCP/IP 解决了实际问题，并且在实际中不断完善。


# 客户端 - 服务器模型
在网络编程中，具体到客户端 - 服务器模型时，我们经常会考虑是使用 TCP 还是 UDP，其实它们二者的区别也很简单：TCP 中连接是谁发起的，在 UDP 中报文是谁发送的。在 TCP 通信中，建立连接是一个非常重要的环节。区别出客户端和服务器，本质上是因为二者编程模型是不同的。

服务器端需要在一开始就监听在一个众所周知的端口上，等待客户端发送请求，一旦有客户端连接建立，服务器端就会消耗一定的计算机资源为它服务，服务器端是需要同时为成千上万的客户端服务的。如何保证服务器端在数据量巨大的客户端访问时依然能维持效率和稳定，这也是我们讲述高性能网络编程的目的。

客户端相对来说更为简单，它向服务器端的监听端口发起连接请求，连接建立之后，通过连接通路和服务器端进行通信。

## IP 和端口
端口号是一个 16 位的整数，最多为 65536

当一个客户端发起连接请求时，`客户端的端口`是`由操作系统内核临时分配`的，称为`临时端口`；服务器端的端口通常是一个众所周知的端口。

一个连接可以通过客户端 - 服务器端的IP和端口唯一确定，这叫做套接字对，按照下面的四元组表示：
```
（clientaddr:clientport, serveraddr: serverport)
```

## 保留网段
一个比较常见的现象是，我们所在的单位或者组织，普遍会使用诸如10.0.x.x 或者 192.168.x.x 这样的 IP 地址，你可能会纳闷，这样的 IP 到底代表了什么呢？不同的组织使用同样的 IP 会不会导致冲突呢? 背后的原因是这样的，`国际标准组织在 IPv4 地址空间里面，专门划出了一些网段，这些网段不会用做公网上的 IP`，而是`仅仅保留做内部使用`，我们把这些地址称作`保留网段`。

下表是三个保留网段，其可以容纳的计算机主机个数分别是 16777216 个、1048576 个和 65536 个。
![image](https://user-images.githubusercontent.com/34932312/76837151-fedeea80-686c-11ea-88ef-3f3ac0b5722c.png)

## 子网掩码
![image](https://user-images.githubusercontent.com/34932312/76845229-83376a80-6879-11ea-8151-adeb1369e228.png)
![image](https://user-images.githubusercontent.com/34932312/76845252-90ecf000-6879-11ea-9f35-887526da9d3a.png)

在网络 IP 划分的时候，我们需要区分两个概念。
- 第一是网络（network）的概念，它表示的是这组 IP 共同的部分，比如在 192.168.1.1~192.168.1.255 这个区间里，它们共同的部分是 192.168.1.0。
- 第二是主机（host）的概念，它表示的是这组 IP 不同的部分，上面的例子中 1~255 就是不同的那些部分，表示有 255 个可用的不同 IP。

例如 IPv4 地址，192.0.2.12，我们可以说`前面三个 bytes 是子网`，`最后一个 byte 是 host`，或者换个方式，我们能说 host 为 8 位，子网掩码为 192.0.2.0/24（255.255.255.0）。

很久很久以前，有子网（subnet）的分类，在这里，`一个 IPv4 地址的第一个，前两个或前三个字节是属于网络的一部分`。如果你很幸运地可以`拥有一个字节的网络，而另外三个字节是 host 地址`，那在你的网络里，你有价值三个字节，也就是 24 个比特的主机地址，这是什么概念呢？ 2 的 24 次方，大约是一千六百万个地址左右。这是一个`“Class A”（A 类）网络`。

![image](https://user-images.githubusercontent.com/34932312/76837151-fedeea80-686c-11ea-88ef-3f3ac0b5722c.png)
我们再来重新看一下这张表格，表格第一行就是这样的一个 A 类网络，10 是对应的网络字节部分，主机的字节是 3，我们将一个字节的子网记作 255.0.0.0。

相对的，“Class B”（B 类）的网络，网络有两个字节，而 host 只有两个字节，也就是说拥有的主机个数为 65536。

“Class C”（C 类）的网络，网络有三个字节，而 host 只有一个字节，也就是说拥有的主机个数为 256。

网络地址位数由子网掩码（Netmask）决定，你可以将`IP地址与子网掩码进行“位与”`操作，就能`得到网络的值`。子网掩码一般看起来像是 255.255.255.0（二进制为 11111111.11111111.11111111.00000000），比如你的 IP 是 192.0.2.12，使用这个子网掩码时，你的网络就会是 192.0.2.12 与 255.255.255.0 所得到的值：192.0.2.0，192.0.2.0 就是这个网络的值。

子网掩码能接受任意个位，而不单纯是上面讨论的 8，16 或 24 个比特而已。所以你可以有一个子网掩码 255.255.255.252（二进制位 11111111.11111111.11111111.11111100），这个子网掩码能切出一个 30 个位的网络以及 2 个位的主机，这个网络最多有四台 host。为什么是 4 台 host 呢？因为变化的部分只有最后两位，所有的可能为 2 的 2 次方，即 4 台 host。

注意，子网掩码的格式永远都是二进制格式：前面是一连串的 1，后面跟着一连串的 0。

不过一大串的数字会有点不好用，比如像 255.192.0.0 这样的子网掩码，人们无法直观地知道有多少个 1，多少个 0，后来人们发明了新的办法，你只需要将一个斜线放在 IP 地址后面，接着用一个十进制的数字用以表示网络的位数，类似这样：192.0.2.12/30, 这样就很容易知道有 30 个 1， 2 个 0，所以主机个数为 4。

## 全球域名系统
![image](https://user-images.githubusercontent.com/34932312/76846249-2b016800-687b-11ea-9287-d6817b079c60.png)

## 数据报和字节流
TCP，又被叫做字节流套接字（Stream Socket），它是网络编程的核心概念。UDP 也有一个类似的叫法, 数据报套接字（Datagram Socket），一般分别以“SOCK_STREAM”与“SOCK_DGRAM”分别来表示 TCP 和 UDP 套接字。

Stream sockets 是可靠的，双向连接的通讯串流。比如以“1-2-3”的顺序将字节流输出到套接字上，它们在另一端一定会以“1-2-3”的顺序抵达，而且不会出错。这种高质量的通信是如何办到的呢？这就是由 TCP（Transmission Control Protocol）协议完成的，TCP 通过诸如`连接管理，拥塞控制，数据流与窗口管理，超时和重传`等一系列精巧而详细的设计，提供了高质量的端到端的通信方式。

UDP 也`可以做到更高的可靠性`，只不过这种可靠性，需要`应用程序进行设计处理`，`比如对报文进行编号，设计 Request-Ack 机制`，再`加上重传`等，`在一定程度上可以达到更为高可靠的UDP程序`。当然，这种可靠性和TCP相比还是有一定的距离，不过也可以弥补实战中UDP的一些不足。

## homework
我们看到保留地址中第二行 172.16.0.0/12 描述为 16 个连续的 B 段，第三行 192.168.0.0/16 描述为 256 个连续的 C 段地址，怎么理解这种描述呢？另外，章节里提到了服务端必须侦听在一个众所周知的端口上，这个端口怎么选择，又是如何让客户端知道的呢？
- 172.16.0.0~172.31.255.255，因为b类网络的host只占最后两个字节，172.16~172.31就代表了16个连续的b类网络可用
- 192.168.0.0~192.168.255.255，因为c类网络的host只占最后一个字节，所以从192.168.0到192.168.255，就有256个连续的c类网络可用
- 服务器可以监听的端口有从0到65535，理论上这台服务器的这个端口只要没被占用，你都可以给服务器绑定。
- 如果是一些默认的服务，服务器绑的也是默认的端口，那么客户端是可以知道的。比如:80是给http服务，443是给https服务，21是给ftp服务等。否则的话，就需要服务器开发者告诉客户端应该连接哪个端口

# 套接字和地址
## socket
![image](https://user-images.githubusercontent.com/34932312/76934659-7f175580-692a-11ea-88c2-3a10330f49b7.png)
这张图表达的其实是网络编程中，客户端和服务器工作的核心逻辑。

我们先从右侧的服务器端开始看，因为在客户端发起连接请求之前，服务器端必须初始化好。右侧的图显示的是服务器端初始化的过程，`首先初始化 socket`，之后服务器端需要执行`bind`函数，将自己的服务能力绑定在一个众所周知的地址和端口上，紧接着，服务器端执行`listen`操作，将原先的socket转化为服务端的socket，服务端最后阻塞在`accept`上等待客户端请求的到来。

此时，服务器端已经准备就绪。客户端需要先初始化 socket，再执行 connect 向服务器端的地址和端口发起连接请求，这里的地址和端口必须是客户端预先知晓的。这个过程，就是著名的`TCP 三次握手（Three-way Handshake`。下一篇文章，我会详细讲到TCP三次握手的原理。

一旦三次握手完成，客户端和服务器端建立连接，就进入了数据传输过程。

具体来说，`客户端进程向操作系统内核发起 write 字节流写操作，内核协议栈将字节流通过网络设备传输到服务器端，服务器端从内核得到信息，将字节流从内核读入到进程中，并开始业务逻辑的处理，完成之后，服务器端再将得到的结果以同样的方式写给客户端。可以看到，一旦连接建立，数据的传输就不再是单向的，而是双向的，这也是 TCP 的一个显著特性`。

当客户端完成和服务器端的交互后，比如执行一次 Telnet 操作，或者一次 HTTP 请求，需要和服务器端断开连接时，就会执行 close 函数，操作系统内核此时会通过原先的连接链路向服务器端发送一个 FIN 包，服务器收到之后执行被动关闭，这时候整个链路处于半关闭状态，此后，服务器端也会执行 close 函数，整个链路才会真正关闭。半关闭的状态下，发起 close 请求的一方在没有收到对方 FIN 包之前都认为连接是正常的；而在全关闭的状态下，双方都感知连接已经关闭。

请注意，以上所有的操作，都是通过 socket 来完成的。无论是客户端的 connect，还是服务端的 accept，或者 read/write 操作等，socket 是我们用来建立连接，传输数据的唯一途径。

## socket 的发展历史
socket 是加州大学伯克利分校的研究人员在 20 世纪 80 年代早期提出的，所以也被叫做伯克利套接字。伯克利的研究者们设想用 socket 的概念，屏蔽掉底层协议栈的差别。第一版实现 socket 的就是 TCP/IP 协议，最早是在 BSD 4.2 Unix 内核上实现了 socket。很快大家就发现这么一个概念带来了网络编程的便利，于是有更多人也接触到了 socket 的概念。Linux 作为 Unix 系统的一个开源实现，很早就从头开发实现了 TCP/IP 协议，伴随着 socket 的成功，Windows 也引入了 socket 的概念。于是在今天的世界里，socket 成为网络互联互通的标准。

## 套接字地址格式
### 通用套接字地址格式
```
/* POSIX.1g 规范规定了地址族为2字节的值.  unsigned short int 无符号短整型  16位  0至65,535 */
typedef unsigned short int sa_family_t;
/* 描述通用套接字地址  */
struct sockaddr{
    sa_family_t sa_family;  /* 地址族.  16-bit*/
    char sa_data[14];   /* 具体的地址值 112-bit */
  }; 
```
第一个字段是地址族，它表示使用什么样的方式对地址进行解释和保存，好比电话簿里的手机格式，或者是固话格式，这两种格式的长度和含义都是不同的。

在 glibc 里的定义非常多，常用的有以下几种：
    - AF_LOCAL：表示的是本地地址，对应的是 Unix 套接字，这种情况一般用于本地 socket 通信，很多情况下也可以写成 AF_UNIX、AF_FILE
    - AF_INET：因特网使用的 IPv4 地址
    - AF_INET6：因特网使用的 IPv6 地址
这里的 AF_ 表示的含义是 Address Family，很多情况下，我们也会看到以`PF_`表示的宏，比如 PF_INET、PF_INET6 等，实际上 PF_ 的意思是`Protocol Family`，也就是协议族的意思。我们用 AF_xxx 这样的值来初始化 socket 地址，用 PF_xxx 这样的值来初始化 socket。我们在头文件中可以清晰地看到，这两个值本身就是一一对应的。
```
/* 各种地址族的宏定义  */
#define AF_UNSPEC PF_UNSPEC
#define AF_LOCAL  PF_LOCAL
#define AF_UNIX   PF_UNIX
#define AF_FILE   PF_FILE
#define AF_INET   PF_INET
#define AF_AX25   PF_AX25
#define AF_IPX    PF_IPX
#define AF_APPLETALK  PF_APPLETALK
#define AF_NETROM PF_NETROM
#define AF_BRIDGE PF_BRIDGE
#define AF_ATMPVC PF_ATMPVC
#define AF_X25    PF_X25
#define AF_INET6  PF_INET6
```

### IPv4 套接字格式地址
常用的 IPv4 地址族的结构：
```
/* IPV4套接字地址，32bit值. uint32_t为32位无符号类型数据 */
typedef uint32_t in_addr_t;
struct in_addr{
    in_addr_t s_addr;
};
  
/* 描述IPV4的套接字地址格式  */
struct sockaddr_in{
    sa_family_t sin_family; /* 16-bit */
    in_port_t sin_port;     /* 端口口  16-bit*/
    struct in_addr sin_addr;    /* Internet address. 32-bit */

    /* 这里仅仅用作占位符，不做实际用处  */
    unsigned char sin_zero[8];
  };
```
可以发现和 sockaddr 一样，都有一个 16-bit 的 sin_family 字段，对于 IPv4 来说这个值就是 AF_INET。

接下来是端口号，我们可以看到端口号最多是 16-bit，也就是说最大支持 2 的 16 次方，这个数字是 65536，所以我们应该知道支持寻址的端口号最多就是 65535。关于端口，我在前面的章节也提到过，这里重点阐述一下保留端口。所谓保留端口就是大家约定俗成的，已经被对应服务广为使用的端口，比如 ftp 的 21 端口，ssh 的 22 端口，http 的 80 端口等。一般而言，大于 5000 的端口可以作为我们自己应用程序的端口使用。

下面是 glibc 定义的保留端口。
```

/* Standard well-known ports.  */
enum
  {
    IPPORT_ECHO = 7,    /* Echo service.  */
    IPPORT_DISCARD = 9,   /* Discard transmissions service.  */
    IPPORT_SYSTAT = 11,   /* System status service.  */
    IPPORT_DAYTIME = 13,  /* Time of day service.  */
    IPPORT_NETSTAT = 15,  /* Network status service.  */
    IPPORT_FTP = 21,    /* File Transfer Protocol.  */
    IPPORT_TELNET = 23,   /* Telnet protocol.  */
    IPPORT_SMTP = 25,   /* Simple Mail Transfer Protocol.  */
    IPPORT_TIMESERVER = 37, /* Timeserver service.  */
    IPPORT_NAMESERVER = 42, /* Domain Name Service.  */
    IPPORT_WHOIS = 43,    /* Internet Whois service.  */
    IPPORT_MTP = 57,

    IPPORT_TFTP = 69,   /* Trivial File Transfer Protocol.  */
    IPPORT_RJE = 77,
    IPPORT_FINGER = 79,   /* Finger service.  */
    IPPORT_TTYLINK = 87,
    IPPORT_SUPDUP = 95,   /* SUPDUP protocol.  */
    
    IPPORT_EXECSERVER = 512,  /* execd service.  */
    IPPORT_LOGINSERVER = 513, /* rlogind service.  */
    IPPORT_CMDSERVER = 514,
    IPPORT_EFSSERVER = 520,

    /* UDP ports.  */
    IPPORT_BIFFUDP = 512,
    IPPORT_WHOSERVER = 513,
    IPPORT_ROUTESERVER = 520,

    /* Ports less than this value are reserved for privileged processes.  */
    IPPORT_RESERVED = 1024,

    /* Ports greater this value are reserved for (non-privileged) servers.  */
    IPPORT_USERRESERVED = 5000
```
实际的 IPv4 地址是一个 32-bit 的字段，可以想象最多支持的地址数就是 2 的 32 次方，大约是 42 亿，应该说这个数字在设计之初还是非常巨大的，无奈互联网蓬勃发展，全球接入的设备越来越多，这个数字渐渐显得不太够用了，于是大家所熟知的 IPv6 就隆重登场了。

### IPv6 套接字地址格式
```
struct sockaddr_in6 {
    sa_family_t sin6_family; /* 16-bit */
    in_port_t sin6_port;  /* 传输端口号 # 16-bit */
    uint32_t sin6_flowinfo; /* IPv6流控信息 32-bit*/
    struct in6_addr sin6_addr;  /* IPv6地址128-bit */
    uint32_t sin6_scope_id; /* IPv6域ID 32-bit */
};
```
整个结构体长度是28个字节，其中流控信息和域 ID 先不用管（这两个字段，一个在 glibc 的官网上根本没出现，另一个是当前未使用的字段）。

这里的地址族显然应该是 AF_INET6，端口同 IPv4 地址一样，关键的地址从 32 位升级到 128 位，这个数字就大到恐怖了，完全解决了寻址数字不够的问题。

以上无论 IPv4 还是 IPv6 的地址格式都是因特网套接字的格式，还有一种本地套接字格式，用来做为本地进程间的通信， 也就是前面提到的 AF_LOCAL。
```
struct sockaddr_un {
    unsigned short sun_family; /* 固定为 AF_LOCAL */
    char sun_path[108];   /* 路径名 */
};
```

## 几种套接字地址格式比较
这几种地址的比较见下图，IPv4 和 IPv6 套接字地址结构的长度是固定的，而本地地址结构的长度是可变的。
![image](https://user-images.githubusercontent.com/34932312/76951149-2a81d380-6946-11ea-845b-c288d04e1d2c.png)

## 总结
这一讲重点讲述了什么是套接字，以及对应的套接字地址格式。套接字作为网络编程的基础，概念异常重要。套接字的设计为我们打开了网络编程的大门，实际上，正是因为 BSD 套接字如此成功，各大 Unix 厂商（包括开源的 Linux）以及 Windows 平台才会很快照搬了过来。在下一讲中，我们将开始创建并使用套接字，建立连接，进一步开始我们的网络编程之旅。

## homework
- IPv4、IPv6、本地套接字格式以及通用地址套接字，它们有什么共性呢？如果你是 BSD 套接字的设计者，你为什么要这样设计呢？
- 为什么本地套接字格式不需要端口号，而 IPv4 和 IPv6 套接字格式却需要端口号呢？

unix系统有一种一统天下的简洁之美:一切皆文件，socket也是文件。
1.像sock_addr的结构体里描述的那样，几种套接字都要有`地址族和地址`两个字段。这容易理解，你要与外部通信，肯定要至少告诉计算机对方的地址和使用的是哪一种地址。与远程计算机的通信还需要一个端口号。而本地socket的不同之处在于不需要端口号，那么就有了问题2;
2.本地socket本质上是在访问本地的文件系统，所以自然不需要端口。远程socket是直接将一段字节流发送到远程计算机的一个进程，而远程计算机可能同时有多个进程在监听，所以用端口号标定要发给哪一个进程。

第一问，通用网络地址结构是所有具体地址结构的抽象，有了统一可以操作的地址结构，那么就可以涉及一套统一的接口，简化了接口设计。通用地址结构中第一个字段表明了地址的类型，后面的数据可以通过具体类型解析出来，一般只有将具体地址类型的指针强制转化成通用类型，这样操作才不会造成内存越界。
第二问，本地socket基于文件操作的，因此只需要根据文件路径便可区分，不需要使用端口的概念。

# 套接字和地址
前面介绍了 IPv4、IPv6 以及本地套接字格式，下面介绍`怎么使用这些套接字格式完成连接的建立`，经典的`TCP 三次握手理论`也会贯穿其中。我希望经过这一讲的讲解，你会牢牢记住 TCP 三次握手和客户端、服务器模型。

## 服务端准备连接的过程
### 创建套接字
要创建一个可用的套接字，需要使用下面的函数：
```
int socket(int domain, int type, int protocol)
```
domain 就是指 PF_INET、PF_INET6 以及 PF_LOCAL 等，表示什么样的套接字。
type 可用的值是：
 - SOCK_STREAM: 表示的是字节流，对应 TCP；
 - SOCK_DGRAM: 表示的是数据报，对应 UDP；
 - SOCK_RAW: 表示的是原始套接字。
参数 protocol 原本是用来指定通信协议的，但现在基本废弃。因为协议已经通过前面两个参数指定完成。protocol 目前一般写成 0 即可。

### bind: 设定电话号码
创建出来的套接字如果需要被别人使用，就需要`调用 bind 函数把套接字和套接字地址绑定`，就像去电信局登记我们的电话号码一样。

调用 bind 函数的方式如下：
```
bind(int fd, sockaddr * addr, socklen_t len)
```
bind 函数后面的第二个参数是通用地址格式sockaddr * addr。这里有一个地方值得注意，那就是虽然接收的是通用地址格式，实际上传入的参数可能是 IPv4、IPv6 或者本地套接字格式。bind 函数会根据 len 字段判断传入的参数 addr 该怎么解析，len 字段表示的就是传入的地址长度，它是一个可变值。

这里其实可以把 bind 函数理解成这样：
```
bind(int fd, void * addr, socklen_t len)
```
不过 BSD 设计套接字的时候大约是 1982 年，那个时候的 C 语言还没有void *的支持，为了解决这个问题，BSD 的设计者们创造性地设计了通用地址格式来作为支持 bind 和 accept 等这些函数的参数。

对于使用者来说，每次需要将 IPv4、IPv6 或者本地套接字格式转化为通用套接字格式，就像下面的 IPv4 套接字地址格式的例子一样：
```
struct sockaddr_in name;
bind (sock, (struct sockaddr *) &name, sizeof (name)
```
对于实现者来说，可根据该地址结构的前两个字节判断出是哪种地址。为了处理长度可变的结构，需要读取函数里的第三个参数，也就是 len 字段，这样就可以对地址进行解析和判断了。

设置 bind 的时候，对地址和端口可以有多种处理方式。
我们可以把地址设置成本机的 IP 地址，这相当告诉操作系统内核，仅仅对目标 IP 是本机 IP 地址的 IP 包进行处理。但是这样写的程序在部署时有一个问题，我们编写应用程序时并不清楚自己的应用程序将会被部署到哪台机器上。这个时候，可以利用`通配地址`的能力帮助我们解决这个问题。通配地址相当于告诉操作系统内核：“Hi，我可不挑活，只要目标地址是咱们的都可以。”比如一台机器有两块网卡，IP 地址分别是 202.61.22.55 和 192.168.1.11，那么向这两个 IP 请求的请求包都会被我们编写的应用程序处理。

那么该如何设置通配地址呢？
对于 IPv4 的地址来说，使用`INADDR_ANY`来完成通配地址的设置；对于 IPv6 的地址来说，使用`IN6ADDR_ANY`来完成通配地址的设置。
```
struct sockaddr_in name;
name.sin_addr.s_addr = htonl (INADDR_ANY); /* IPV4通配地址 */
```
除了地址，还有端口。如果把端口设置成 0，就相当于把端口的选择权交给操作系统内核来处理，操作系统内核会根据一定的算法选择一个空闲的端口，完成套接字的绑定。这在服务器端不常使用。

一般来说，服务器端的程序一定要绑定到一个众所周知的端口上。服务器端的 IP 地址和端口数据，相当于打电话拨号时需要知道的对方号码，如果没有电话号码，就没有办法和对方建立连接。

我们来看一个初始化 IPv4 TCP 套接字的例子:
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/socket.h>
#include <netinet/in.h>

//
// Created by huangyong on 2020-03-19.
//
int make_socket(uint16_t port) {
    int sock;
    struct sockaddr_in name;

    /* 创建字节流类型的IPV4 socket */
    sock = socket(PF_INET, SOCK_STREAM, 0);
    if (sock < 0) {
        perror("socket");
        exit(EXIT_FAILURE);
    }

    /* 绑定到port和ip */
    name.sin_family = AF_INET; /* ipv4 */
    name.sin_port = htons(port); /* 指定端口 */
    name.sin_addr.s_addr = htonl(INADDR_ANY); /* 通配地址 */

    /* 把IPV4地址转换成通用地址格式，同时传递长度 */
    if (bind(sock, (struct sockaddr *)&name, sizeof(name)) < 0) {
        perror("bind");
        exit (EXIT_FAILURE);
    }
    return sock;
}
```

### listen：接上电话线，一切准备就绪

`bind 函数只是让我们的套接字和地址关联`，如同登记了电话号码。如果要让别人打通电话，还需要我们把电话设备接入电话线，让服务器真正处于可接听的状态，这个过程需要依赖 listen 函数。

初始化创建的套接字，可以认为是一个"主动"套接字，其目的是之后主动发起请求（通过调用 connect 函数，后面会讲到）。通过 listen 函数，可以将原来的"主动"套接字转换为"被动"套接字，告诉操作系统内核：“我这个套接字是用来等待用户请求的。”当然，操作系统内核会为此做好接收用户请求的一切准备，比如完成连接队列。

listen 函数的原型是这样的：
```
int listen (int socketfd, int backlog)
```
socketfd 为`套接字描述符`，backlog官方的解释为`未完成连接队列的大小`，这个`参数的大小决定了可以接收的并发数目`。这个参数越大，并发数目理论上也会越大。但是参数过大也会占用过多的系统资源，一些系统，比如 Linux 并不允许对这个参数进行改变。对于 backlog 整个参数的设置有一些最佳实践后面结合具体的实例进行解读。

### accept: 电话铃响起了……
当客户端的连接请求到达时，服务器端应答成功，连接建立，这个时候操作系统内核需要把这个事件通知到应用程序，并让应用程序感知到这个连接。

连接建立之后，你可以把 accept 这个函数看成是`操作系统内核和应用程序之间的桥梁`。它的原型是：
```
int accept(int listensockfd, struct sockaddr *cliaddr, socklen_t *addrlen)
```
- listensockfd是套接字，可以叫它为 listen 套接字，因为这就是前面通过 bind，listen 一系列操作而得到的套接字
- 函数的返回值有两个部分，第一个部分 cliadd 是通过指针方式获取的客户端的地址，addrlen 告诉我们地址的大小，这可以理解成当我们拿起电话机时，看到了来电显示，知道了对方的号码；另一个部分是函数的返回值，这个返回值是一个全新的描述字，代表了与客户端的连接。

这里一定要注意有两个套接字描述字，第一个是`监听套接字描述字 listensockfd`，它是作为输入参数存在的；第二个是返回的已连接套接字描述字。

监听套接字一直都存在，它是要为成千上万的客户来服务的，直到这个监听套接字关闭.

一旦一个客户和服务器连接成功，完成了 TCP 三次握手，操作系统内核就为这个客户生成一个已连接套接字，让应用服务器使用这个已连接套接字和客户进行通信处理。如果应用服务器完成了对这个客户的服务，比如一次网购下单，一次付款成功，那么关闭的就是已连接套接字，这样就完成了 TCP 连接的释放。请注意，这个时候释放的只是这一个客户连接，其它被服务的客户连接可能还存在。最重要的是，监听套接字一直都处于“监听”状态，等待新的客户请求到达并服务。


## 客户端发起连接的过程
客户端发起连接请求的过程第一步还是和服务端一样，要建立一个套接字，方法和前面是一样的，不一样的是客户端需要调用 connect 向服务端发起请求。

### connect: 拨打电话
客户端和服务器端的连接建立，是通过 connect 函数完成的。这是 connect 的构建函数：
```
int connect(int sockfd, const struct sockaddr *servaddr, socklen_t addrlen)
```
- sockfd 是连接套接字，通过前面讲述的 socket 函数创建
- servaddr 代表指向套接字地址结构的指针。套接字地址结构必须含有服务器的 IP 地址和端口号。
- addrlen 代表套接字地址结构的大小

如果是 TCP 套接字，那么`调用connect函数将激发TCP的三次握手过程`，而且仅在连接建立成功或出错时才返回。其中出错返回可能有以下几种情况：
- 三次握手无法建立，`客户端发出的 SYN 包没有任何响应`，于是返回`TIMEOUT 错误`。这种情况比较常见的原因是对应的服务端 IP 写错。
- `客户端收到了RST（复位）回答，这时候客户端会立即返回 CONNECTION REFUSED 错误`。
    - 这种情况比较常见于客户端发送连接请求时的请求端口写错，因为 RST 是 TCP 在发生错误时发送的一种 TCP 分节。产生 RST 的三个条件是：目的地为某端口的 SYN 到达，然而该端口上没有正在监听的服务器（如前所述）；TCP 想取消一个已有连接；TCP 接收到一个根本不存在的连接上的分节。
    - RST标示复位、用来异常的关闭连接。发送RST包关闭连接时，不必等缓冲区的包都发出去，直接就丢弃缓冲区中的包，发送RST。而接收端收到RST包后，也不必发送ACK包来确认。
    - 什么时候发送RST包? 1、建立连接的SYN到达某端口，但是该端口上没有正在监听的服务。2、TCP收到了一个根本不存在的连接上的分节。3、请求超时。 使用setsockopt的SO_RCVTIMEO选项设置recv的超时时间。接收数据超时时，会发送RST包。
- 客户发出的 SYN 包在网络上引起了"destination unreachable"，即目的不可达的错误。这种情况比较常见的原因是客户端和服务器端路由不通。    
- 根据不同的返回值，我们可以做进一步的排查。

## TCP 三次握手
![image](https://user-images.githubusercontent.com/34932312/77058130-f96dd580-6a0f-11ea-8f0b-8409f3590eec.png)
这里我们`使用的网络编程模型都是阻塞式的`。所谓阻塞式，就是调用发起后不会直接返回，由操作系统内核处理之后才会返回。

我们先看一下最初的过程，服务器端通过`socket，bind 和 listen`完成了被动套接字的准备工作，被动的意思就是等着别人来连接，然后调用 accept，就会阻塞在这里，等待客户端的连接来临；客户端通过调用`socket 和 connect`函数之后，也会阻塞。接下来的事情是由操作系统内核完成的，更具体一点的说，是`操作系统内核网络协议栈`在工作。下面是具体的过程：

- 客户端的协议栈向服务器端发送了`SYN包`，并告诉服务器端`当前发送序列号 j`，客户端进入`SYNC_SENT`状态
- 服务器端的协议栈收到这个包之后，和客户端进行`ACK`应答，应答的值为`j+1`，表示对 SYN 包 j 的确认，同时`服务器也发送一个 SYN 包`，告诉客户端当前我的`发送序列号为 k`，服务器端进入`SYNC_RCVD`状态
- 客户端协议栈收到 ACK 之后，使得应用程序从 connect 调用返回，表示`客户端到服务器端的单向连接建立成功`，客户端的状态为`ESTABLISHED`，同时客户端协议栈也会对服务器端的 SYN 包进行应答，应答数据为`k+1`
- 应答包到达服务器端后，服务器端协议栈使得 accept 阻塞调用返回，这个时候`服务器端到客户端的单向连接也建立成功`，服务器端也进入`ESTABLISHED`状态。

## 总结
我们分别从服务端和客户端的角度，介绍了`如何创建套接字`，并`利用套接字完成 TCP 连接的建立`。
- 服务器端通过创建 socket，bind，listen 完成初始化，通过 accept 完成连接的建立
- 客户端通过创建 socket，connect 发起连接建立请求。

## homework
- 第一道是关于阻塞调用的，既然有阻塞调用，就应该有非阻塞调用，那么如何使用非阻塞调用套接字呢？使用的场景又是哪里呢？
- 第二道是关于客户端的，客户端发起 connect 调用之前，可以调用 bind 函数么？

思考题1：非阻塞调用的场景就是高性能服务器编程！我所有的调用都不需要等待对方准备好了再返回，而是立即返回，那么我怎么知道是否准备好了？就是把这些fd注册到类似select或者epoll这样的调用中，变多个fd阻塞为一个fd阻塞，只要有任何一个fd准备好了，select或者epoll都会返回，然后我们在从中取出准备好了的fd进行各种IO操作，从容自然 ^o^

之前看过一些文章解释，为什么tcp建立连接需要三次握手，解释如下：
tcp连接的双方要确保各自的收发消息的能力都是正常的。
客户端第一次发送握手消息到服务端，
服务端接收到握手消息后把ack和自己的syn一同发送给客户端，这是第二次握手，
当客户端接收到服务端发送来的第二次握手消息后，客户端可以确认“服务端的收发能力OK，客户端的收发能力OK”，但是服务端只能确认“客户端的发送OK，服务端的接收OK”，
所以还需要第三次握手，客户端收到服务端的第二次握手消息后，发起第三次握手消息，服务端收到客户端发送的第三次握手消息后，就能够确定“服务端的发送OK，客户端的接收OK”，
至此，客户端和服务端都能够确认自己和对方的收发能力OK，，tcp连接建立完成。

客户端其实可以bind，但是没必要bind。客户端调用connect成功后系统会随机分配一个可用端口。这里涉及到端口选择算法和列维模型。


# 使用套接字进行读写-开始交流
前面我们介绍了套接字相关的知识，包括套接字的格式，套接字的创建以及TCP连接的建立等。下面我们来介绍如何使用创建的套接字收发数据。

## 发送数据
发送数据时常用的有三个函数，分别是 write、send 和 sendmsg。
```
ssize_t write (int socketfd, const void *buffer, size_t size)ssize_t 
这个函数是常见的文件写函数，如果把 socketfd 换成文件描述符，就是普通的文件写入。
函数说明：write()会把参数buf所指的内存写入count个字节到所指的文件内。
返回值：如果顺利write()会返回实际写入的字节数。当有错误发生时则返回-1，错误代码存入errno中。
write()函数从buf写数据到fd中时，若buf中数据无法一次性读完，那么第二次读buf中数据时，其读位置指针（也就是第二个参数buf）不会自动移动，需要程序员编程控制，而不是简单的将buf首地址填入第二参数即可。

send (int socketfd, const void *buffer, size_t size, int flags)ssize_t 
如果想指定选项，发送带外数据，就需要使用第二个带 flag 的函数。所谓带外数据，是一种基于 TCP 协议的紧急数据，用于客户端 - 服务器在特定场景下的紧急处理。
数据分为两种，一种是带内数据，一种是带外数据。带内数据就是我们平常传输或者说是口头叫的数据，带外数据拥有更高的优先级。带外数据不要求再启动一个连接进行传输，而是使用已有的连接进行传输。所以当TCP发送带外数据时，他的TCP首部一定是设置了URG标志和紧急指针的 。而紧急指针就是用来指出带外数据字节在正常字节流中的位置的 。

sendmsg(int sockfd, const struct msghdr *msg, int flags)
如果想指定多重缓冲区传输数据，就需要使用第三个函数，以结构体 msghdr 的方式发送数据。
```
你看到这里可能会问，既然套接字描述符是一种特殊的描述符，那么在套接字描述符上调用 write 函数，应该和在普通文件描述符上调用 write 函数的行为是一致的，都是`通过描述符句柄写入指定的数据`。

对于普通文件描述符而言，一个文件描述符代表了打开的一个文件句柄，通过调用 write 函数，操作系统内核帮我们不断地往文件系统中写入字节流。注意，写入的字节流大小通常和输入参数 size 的值是相同的，否则表示出错。

对于套接字描述符而言，它代表了一个双向连接，在套接字描述符上调用 write 写入的字节数有可能比请求的数量少，这在普通文件描述符情况下是不正常的。

产生这个现象的原因在于操作系统内核为读取和发送数据做了很多我们表面上看不到的工作。接下来我拿 write 函数举例，重点阐述发送缓冲区的概念。

## Linux中的文件描述符
相关知识：https://blog.csdn.net/cywosp/article/details/38965239

## 发送缓冲区
当 TCP 三次握手成功，TCP 连接成功建立后，`操作系统内核会为每一个连接创建配套的基础设施，比如发送缓冲区`。

发送缓冲区的大小可以通过套接字选项来改变，当我们的应用程序调用 write 函数时，实际所做的事情是`把数据从应用程序中拷贝到操作系统内核的发送缓冲区中`，并不一定是把数据通过套接字写出去。这里有几种情况：
- 第一种情况很简单，`操作系统内核的发送缓冲区足够大，可以直接容纳这份数据`，那么皆大欢喜，我们的`程序从 write 调用中退出，返回写入的字节数就是应用程序的数据大小`。
- 第二种情况是，操作系统内核的发送缓冲区是够大了，不过还有数据没有发送完，或者数据发送完了，但是操作系统内核的发送缓冲区不足以容纳应用程序数据
    - 在这种情况下，操作系统内核并不会返回，也不会报错，而是`应用程序被阻塞`，也就是说应用程序在 write 函数调用处停留，不直接返回。
    - 那么什么时候才会返回呢？实际上，每个操作系统内核的处理是不同的。大部分 UNIX 系统的做法是一直等到可以把应用程序数据完全放到操作系统内核的发送缓冲区中，再从系统调用中返回。
    - 我们的操作系统内核是很聪明的，当 TCP 连接建立之后，它就开始运作起来。你可以把发送缓冲区想象成一条包裹流水线，有个聪明且忙碌的工人不断地从流水线上取出包裹（数据），这个工人会按照 TCP/IP 的语义，将取出的包裹（数据）封装成 TCP 的 MSS 包，以及 IP 的 MTU 包，最后走数据链路层将数据发送出去。这样我们的发送缓冲区就又空了一部分，于是又可以继续从应用程序搬一部分数据到发送缓冲区里，这样一直进行下去，到某一个时刻，应用程序的数据可以完全放置到发送缓冲区里。在这个时候，`write 阻塞调用返回`。注意返回的时刻，应用程序数据并没有全部被发送出去，发送缓冲区里还有部分数据，这部分数据会在稍后由操作系统内核通过网络发送出去。
![image](https://user-images.githubusercontent.com/34932312/77132893-0f28dc80-6a9c-11ea-9e4c-743863d64867.png)

## 读取数据
套接字描述本身和本地文件描述符并无区别，在 UNIX 的世界里万物都是文件，这就意味着可以将套接字描述符传递给那些原先为处理本地文件而设计的函数。这些函数包括 read 和 write 交换数据的函数。

### read 函数
这个函数的原型如下：
```
ssize_t read (int socketfd, void *buffer, size_t size)
```
read 函数要求操作系统内核从套接字描述字socketfd读取最多多少个字节（size），并将结果存储到 buffer 中。

返回值告诉我们`实际读取的字节数目`，也有一些特殊情况，如果`返回值为 0，表示 EOF（end-of-file），这在网络中表示对端发送了 FIN 包，要处理断连的情况`；如果返回值为 -1，表示出错。当然，如果是非阻塞 I/O，情况会略有不同，在后面我们会重点讲述非阻塞 I/O 的特点。

注意这里是最多读取 size 个字节。如果我们想让应用程序每次都读到 size 个字节，就需要编写下面的函数，不断地循环读取。
```
/* 从socketfd描述字中读取"size"个字节. */
size_t readn(int fd, void *buffer, size_t size) {
    char *buffer_pointer = buffer;
    int length = size;

    while (length > 0) {
        int result = read(fd, buffer_pointer, length);

        if (result < 0) {
            if (errno == EINTR)
                continue;     /* 考虑非阻塞的情况，这里需要再次调用read */
            else
                return (-1);
        } else if (result == 0)
            break;                /* EOF(End of File)表示套接字关闭 */

        length -= result;
        buffer_pointer += result;
    }
    return (size - length);        /* 返回的是实际读取的字节数*/
}
```
- 6-19 行的循环条件表示的是，在没读满 size 个字节之前，一直都要循环下去。
- 10-11 行表示的是非阻塞 I/O 的情况下，没有数据可以读，需要继续调用 read。
- 14-15 行表示读到对方发出的 FIN 包，表现形式是 EOF，此时需要关闭套接字。
- 17-18 行，需要读取的字符数减少，缓存指针往下移动。
- 20 行是在读取 EOF 跳出循环后，返回实际读取的字符数。

## 缓冲区实验
我们用一个客户端 - 服务器的例子来解释一下读取缓冲区和发送缓冲区的概念。在这个例子中客户端不断地发送数据，服务器端每读取一段数据之后进行休眠，以模拟实际业务处理所需要的时间。

服务器端读取数据程序：
```

```

gcc main.c -o server


