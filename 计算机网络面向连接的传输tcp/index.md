# 计算机网络: 面向连接的传输-TCP


TCP是因特网运输层的面向连接的可靠的运输协议。依赖于差错检测、重传、累计确认、定时器以及带有序号和确认号的首部字段，使得它可以在不可靠的IP协议之上，提供可靠的运输协议。

### 3.5.1 TCP连接概述

- TCP是面向连接的。这里的**连接**指的是一两条逻辑连接，其共同状态包括要维护的参数、TCP缓存等,仅仅保留在两个通信端系统的TCP程序中。

- *TCP是点到点的。即单个发送方和单个接收方之间的连接，其中发送方被称为客户端，接收端被称为服务器。

- *建立连接的过程：客户先发送一个特殊（首部字段特殊）的TCP报文段，服务器用另一个特殊的TCP报文段来响应，最后客户端再用第三个特殊报文段作为响应，前两个报文段不承载任何应用层数据。这样一个建立连接的过程称为三次握手。

一旦建立起TCP连接，两个应用进程旧可以相互发送数据。客户进程将数据通过套接字传给TCP，TCP将这些数据存入**发送缓存**，接下来再陆续从发送缓存里取出数据，封装送给网络层。

- ***MSS**（Maximum Segment Size,最大报文长度）：报文段里应用层数据的最大长度，不包括首部字段。

- ***MTU**（Maximum Transmission Unit,最大传输单元）：包含TCP首部的报文段最大长度，以太网和PPP链路层协议都有1500字节的MTU，一个典型值是1460。

![20211205-1222-97iFQ4](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1222-97iFQ4.png)

TCP连接的组成：客户端主机上的缓存、变量与套接字；服务器主机上的缓存、变量与套接字。

### 3.5.2 TCP报文段结构

<img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1223-Mg4kAl.png" alt="20211205-1223-Mg4kAl" width="346" data-align="center">

TCP的首部一般是20字节，比UDP首部多12字节。

- 源端口号和目的端口号：用于多路复用/分解
- 32比特的序号字段/32比特的确认号字段：用于实现可靠数据传输
- 16比特的接收窗口字段，用于流量控制
- 4比特首部长度字段：指示了TCP的首部长度
- 可选与变长的选项字段：用于连接双方协商最大报文长度（MSS）
- 6比特的标志字段：
  - ACK：指示确认字段中的值是否有效
  - RST、SYN、FIN：用于连接的建立和断开。
  - CWR、ECE：拥塞明确。
  - PSH：数据是否立即交给上层。
  - URG：指示包含紧急数据。

#### 序号和确认号

序号和确认号是TCP首部中最重要的两个字段。一个报文段的序号是报文段首字节的字节流编号。
假定数据流由一个包含500000字节的文件组成，其MSS为1000字节，数据流的首字节编号是0。该TCP将为该数据流构建500个报文段。给第一个报文段分配序号0,第二个报文段分配序号1000,第三个报文段分配序号2000,以此类推。每一个序号被填入到相应TCP 报文段首部的序号字段中。 

![20211205-1223-YRHci7](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1223-YRHci7.png)

确认号用于接收端向发送端发送已经接收到的正确顺序报文段中，序号最大的报文段。也就是主机正在等待的数据的下一个字节的序号 

<img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1224-rtHt3F.png" alt="20211205-1224-rtHt3F" data-align="center" width="351">

### 3.5.3 往返时间的估计和超时

TCP采用超时/重传机制来处理报文段的丢失问题，那么如何设置超时间隔呢。

#### 估计往返时间

RTT（SimpleRTT ）:某报文段被发出（交给IP）到对该报文的确认被收到之间的时间间隔。
由于路由器的拥塞，报文段的RTT会随时间波动，TCP维持一个RTT的均值（EstimatedRTT)，一旦获得一个新的R TT，TCP采用下列公式来更新EstimatedRTT： ![20211205-1224-3Qky73](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1224-3Qky73.png)
<img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1226-vb6T1L.png" alt="20211205-1226-vb6T1L" data-align="center" width="521">

DevRTT ：RTT偏差，用于估算SampleRTT 一般会偏离EstimatedRTT的程度，RTT波动较小，则DevRTT值就很小。 

![20211205-1226-C515ft](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1226-C515ft.png)

#### 设置和管理重传超时间隔

![20211205-1227-B8aFZd](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1227-B8aFZd.png)

超时间隔被定义为上式，初始Timeoutinterval为1秒，当出现超时后，Timeoutinterval值将加倍，再次接收报文段后，再使用上式计算Timeoutinterval。

### 3.5.4 可靠数据传输

英特网网络层（IP服务）是不可靠的，IP服务的数据报可能会溢出路由器缓存而丢失；也可能是乱序到达；数据报中的比特还可能会损坏（0变成1）。
TCP在IP不可靠的尽力而为的服务上创建了一种可靠数据传输服务，确保一个进程从其接收缓存中读取的数据流是无损坏、无间隙、非冗余、按序的数据流。下图给出了TCP发送方的高度简化描述。 

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1227-UANfad.png" title="" alt="20211205-1227-UANfad" data-align="center">NextSeqNum和SendBase的差组成了窗口。

#### 三种情况

1. 确认丢失 
   
   <img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1229-OHtSTm.png" alt="20211205-1229-OHtSTm" width="373" data-align="center">

2. 超时重传 
   
   <img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1229-Z3he2N.png" alt="20211205-1229-Z3he2N" data-align="center" width="289">

3. 确认丢失，但是没有超时
   <img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1231-hneVm7.png" alt="20211205-1231-hneVm7" width="284" data-align="center">
   
   #### 超时间隔加倍
   
   TCP重传具有最小序号的还未被确认的报文段。只是每次TCP重传时都会将下一次的超时间隔设为先前值的两倍，一旦收到确认，就立马使用EstimatedRTT值与DevRTT值推算得到超时间隔。
   这种设定有助于拥塞控制，防止链路中有太多的数据报。
   
   #### 快速重传
   
   冗余ACK：发送方已经收到了对该报文的确认，接收方再次发送该报文的ACK。
   一旦收到3个冗余ACK, TCP就执行快速重传。

### 3.5.5 流量控制

TCP收到连续的、正确的字节后，就将数据放入接收缓存，等待应用程序取出（并不是一到达立马取出）。
当数据过多时，就可能溢出缓存。因此TCP提供了流量控制服务，以消除发送方使接收方缓存溢出的可能性。
流量控制是一个速度匹配服务，即发送方的发送速率与接收方的应用程序读取速率相匹配。
TCP通过记录并发送缓存空闲空间的大小来实现流量控制 

当rwnd等于0时，主机A作为发送方，继续发送只有一个字节的报文，这些报文段将会被接收方B确认，但B的缓存被清空后，确认报文里将包含一个非0的 rwnd值。

### 3.5.6 TCP连接管理

#### 连接的建立

1. **客户端发送SYN报文段。** 客户端的TCP首先向服务器端的TCP发送一个特殊的TCP报文段，该报文段不包含任何应用层数据，但是首部一个标志位（SYN）被设置为1。这个特殊报文段被称为SYN报文段。另外，客户会随机地选择一个初始序号（client-isn）,并将此编号放置于该起始的TCP的SYN 报文段的序号字段中。该报文段会被封装在一个IP数据报中，并发送给服务器。
2. **服务器回传SYNACK报文段。** 当TCP的SYN报文段的IP数据报到达服务器主机，服务器从数据报中提取出TCP的SYN报文段，并为TCP连接分配TCP缓存和变量。并回送一个允许连接的报文段，这个报文段不包含任何应用层数据，首部的SYN字段也为1，确认号字段为client-isn+1 ，并选择自己的初始序号sever-isn，将其放到TCP首部的序号字段。含义：“我收到了你发起建立连接的SYN分组，该分组带有初始序号 clientjsno我同意建立该连接。我自己的初始序号是server-isn”。
3. **连接建立。** 在收到SYNACK后，客户端给给该连接分配缓存和变量。再发送一个报文段，在确认字段设置为server-isn + 1，并且SYN字段设置为0，该阶段的报文段可以携带客户到服务器的数据。连接建立后，后续报文段中的SYN字段都被置为0。 
   
   <img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1232-LVbvtz.png" alt="20211205-1232-LVbvtz" data-align="center" width="407">

#### 连接的终止

参与TCP连接的两方任意一方都可终止该连接。当连接结束后，主机中的缓存和变量都将被释放。
假设客户端打算关闭TCP连接。

1. 客户向服务器发送一个特殊报文段，其FIN字段设置为1.
2. 服务器接收到该报文段，回送一个确认报文段，然后也发送足迹的FIN为1的终止报文段
3. 客户对这个报文段进行确认，随后两台主机上关于该连接的所有资源都被释放。 
   
   <img title="" src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211205-1233-nb3oHN.png" alt="20211205-1233-nb3oHN" data-align="center" width="349">

