# IPv4

[TOC]

### 概述

- 我们把整个因特网看成为一个单一的、抽象的网络。
- IP 地址就是给每个连接在互联网上的主机（或路由器）分配一个在全世界范围是唯一的 32 位的标识符。
- IP 地址现在由互联网名字和数字分配机构ICANN (Internet Corporation for Assigned Names and Numbers)进行分配。 

### IP地址的编址方式

- ==分类的 IP 地址==。这是最基本的编址方法，在 1981 年就通过了相应的标准协议。

  - 将IP地址划分为若干个固定类。

  - 每一类地址都由两个固定长度的字段组成，其中一个字段是==网络号 net-id==，它标志主机（或路由器）所连接到的网络，而另一个字段则是==主机号 host-id==，它标志该主机（或路由器）。

  - 主机号在它前面的网络号所指明的**网络范围内必须是唯一的**。

  - 由此可见，一个 IP 地址在整个互联网范围内是唯一的。

    

- ==子网的划分==。这是对最基本的编址方法的改进，其标准 [RFC 950] 在 1985 年通过。

- ==构成超网==。这是比较新的无分类编址方法。 1993 年提出后很快就得到推广应用。

## IP地址码

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-21_10-10-15.png" style="zoom:80%;" />

### 点分十进制法

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-21_10-13-12.png" style="zoom:80%;" />

==不同类型IP地址net-id 不同==

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-21_10-31-30.png" style="zoom:80%;" />

按照8位一组

- A类地址net-id必需以0开头

  所以0000,0000 - 0111,1111 == 0 - 127

  需要排除net-id全为0的情况 (0), 还需要排除A类IP中的特殊全为1的情况(127)

  所以1 - 126

- B类地址net-id必需以10开头

  所以10000000.00000000——10111111.11111111 == 128.0 - 191.255

  需要排除net-id全为0的情况 (128.0 )

  所以128.1 - 191.255

- 同理C类地址, 排除net-id全为0的情况

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-21_10-56-30.png" style="zoom:80%;" />

$$
\because 0-255 地址
\therefore 256个地址 
- 网段0000 - 广播地址1111  = 254个地址
$$

### 一般不使用的特殊IP地址

==host-id 不能全为1,  全一为广播地址==

==host-id 不能全为0, 表示一个网段==

==net-id不能全为0, 排除固定值==

例如 B类IP 不能为(1000000000, 00000000 == 128.0)

不能为==127.0.0.1 本地回环地址==

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-21_10-18-27.png" style="zoom:80%;" />

保留的私网地址

10.0.0.0 -- 10.255.255.255

172.16.0.0 -- 172.31.255.255

192.168.0.0 -- 192.168.255.255

==网关==虽然可以使用所有地址但是==一般使用第一个可用地址==

常见地址进制转换 

tips 利用右移性质

| 二进制    | 十进制        |
| --------- | ------------- |
| 1000,0000 | 2^7 = 128     |
| 1100,0000 | 2^7+2^6= 192  |
| 1110,0000 | 224           |
| 1111,0000 | 240           |
| 1111,1000 | 248           |
| 1111,1100 | 252           |
| 1111,1110 | 255           |
| 1111,1111 | 2^8 - 1 = 255 |

## IP地址的重要特点

1. IP 地址是一种分等级的地址结构。分两个等级的好处是：
   - IP 地址管理机构在分配 IP 地址时==只分配网络号==，而剩下的主机号则由得到该网络号的单位自行分配。这样就方便了 IP 地址的管理
   - 路由器仅根据目的主机所连接的网络号来转发分组（而不考虑目的主机号），这样就可以使路由表中的项目数大幅度减少，从而减小了路由表所占的存储空间。 
2. ==实际上 IP 地址是标志一个主机（或路由器）和一条链路的接口。==
   当一个主机同时连接到两个网络上时，该主机就必须同时具有两个相应的 IP 地址，其网络号 net-id 必须是不同的。这种主机称为多归属主机 (multihomed host)。
   由于一个路由器至少应当连接到两个网络（这样它才能将 IP 数据报从一个网络转发到另一个网络），因此一个==路由器至少应当有两个不同的 IP 地址。==
3.  用转发器或网桥连接起来的若干个局域网仍为一个网络，==因此这些局域网都具有同样的网络号 net-id。==
4.  所有分配到网络号 net-id 的网络，无论是范围很小的局域网，还是可能覆盖很大地理范围的广域网，都是平等的。

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-21_10-29-29.png" style="zoom:80%;" />

## CIDR/斜线法

无分类的两级编址：

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-23_17-38-21.png" style="zoom:80%;" />

CIDR(classless Inter-Domain Routing无类别地址) 使用“==斜线记法==”(slash notation)，它又称为 CIDR 记法，即在 IP 地址面加上一个斜线“/”，然后写上网络前缀所占的位数（==这个数值对应于三级编址中子网掩码中 1 的个数，也是net-id位数==）。

例如：128.14.32.0/20

01000000.00001110.0001(0000.00000000)


$$
表示的地址块共有2^{12}个地址（因为斜线后面的 20 是网络前缀的位数，所以这个地址的主机号是 12 位）。
$$

128.14.32.0/20 地址块的最小地址：128.14.32.0
128.14.32.0/20 地址块的最大地址：128.14.47.255
全 0 和全 1 的主机号地址一般不使用。

## IP数据报

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-25_01-15-36.png"/>

- 版本：占 4 位，==指 IP 协议的版本==。目前的 IP 协议版本号为 4 (即 IPv4)。

- 首部长度：占 4 位，可表示的最大数值是 15 个单位(一个单位为 4 字节)，==因此 IP 的首部长度的最大值是 60 字节===。

- 区分服务：占 8 位，用来获得更好的服务。在旧标准中叫做服务类型，但实际上一直未被使用过。
  1998 年这个字段改名为区分服务。只有在使用区分服务（DiffServ）时，这个字段才起作用。
  在一般的情况下都不使用这个字段 

- 总长度：首部和数据之和的长度，单位为字节，因此数据报的最度——占 16 位，大长度为 65535 字节。总长度必须不超过最大传送单元 MTU。 

- 标识(identification) ：占 16 位，它是一个计数器，用来产生 IP 数据报的标识

- 标志(flag) ：占 3 位，目前只有前两位有意义。

  标志字段的最低位是 MF (More Fragment)。MF = 1 表示后面“还有分片”。MF = 0 表示最后一个分片。
  标志字段中间的一位是 DF (Don't Fragment) 。只有当 DF = 0 时才允许分片。 

- 生存时间：占8 位，记为 TTL (Time To Live)，指示数据报在网络中可通过的路由器数的最大值。

- 协议：占8 位，指出此数据报携带的数据使用何种协议，以便目的主机的 IP 层将数据部分上交给那个处理过程

<img src="..\..\..\imgs\_Net\计算机网络\Snipaste_2020-08-25_01-20-44.png"/>

- 首部检验和：占16 位，只检验数据报的首部，不检验数据部分。这里不采用 CRC 检验码而采用简单的计算方法。 

- 源地址和目的地址都各占 4 字节

## 网关

网关到底是什么呢？网关实质上是一个网络通向其他网络的IP地址。比如有网络A和网络B，网络A的IP地址范围为“192.168.1.1~192. 168.1.254”，子网掩码为255.255.255.0；网络B的IP地址范围为“192.168.2.1~192.168.2.254”，子网掩码为255.255.255.0。在没有路由器的情况下，两个网络之间是不能进行TCP/IP通信的，即使是两个网络连接在同一台交换机（或集线器）上，TCP/IP协议也会根据子网掩码（255.255.255.0）与主机的IP 地址作 “与” 运算的结果不同判定两个网络中的主机处在不同的网络里。而要实现这两个网络之间的通信，则必须通过网关。如果网络A中的主机发现数据包的目的主机不在本地网络中，就把数据包转发给它自己的网关，再由网关转发给网络B的网关，网络B的网关再转发给网络B的某个主机（如附图所示）。网络A向网络B转发数据包的过程。