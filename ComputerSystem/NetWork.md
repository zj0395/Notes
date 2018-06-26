# COMPUTER NETWORKS
- distribution system - 分布式系统  
- vpn - virtual private network 虚拟专用网络  
- B2C - Busness to Customer  
  B2B  
  G2C - Government to Customer  
  C2C  
  P2P - Peer to Peer

- RFID - 无线射频识别  
  passive RFID 无源RFID  
  active RFID 有源RFID  

- GPS - Global Positioning System  
- NFC - Near Field Communication - 近场通信
- PAN - Personal Area Network
  LAN - Local Area Network  
  MAN - Metropolitan Area Network - 城域网  
  WAN - Wide Area Network - 广域网

- ISP - Internet Service Provider
- Protocol Stack 一个特定的系统所使用的一组协议，每一层一个协议，称协议栈  
  a stack of layer - 层次栈  
  a stack of level - 分级栈
  二者都是每一层都建立在下一层的基础上


 - OSI Open System Interconnection  
   7.应用层  
   6.表示层  
   5.会话层  
   4.传输层  
   3.网络层  
   2.数据链路层  
   1.物理层  
   每一层都有  服务、接口、协议
   
- TCP/IP  - Transport Control Protocol / Internet Protocol - 传输控制协议/因特网协议  
  应用层 - 对应OSI 7  
  传输层 - 对应OSI 4  
  互联网层 - 对应OSI 3  
  链路层
  
- UDP - User Datagram Protocol - 用户数据报协议
- LTE - Long Term Evolution - 长期演进
- WEP - Wired Equivalent Privacy - 有限等效加密 - 以被破解  
  WPA - Wifi Protected Access  
  
- ITU - Internetional Telecommunication Union - 国际电信联盟
- IEEE - Institute of Electical and Electronics Engineers - 电气和电子工程师协会  
  802.3 - 以太网  
  802.11 - WLAN  
  802.15 - 个域网 - 蓝牙  
  802.16 - 宽带无线 WIMAX
  
- SONET - Synchronous Optical Network - 同步光纤网络  
  DSL - 数字用户线路  
  ADSL - Asymmetric DSL - 非对称数字用户线  
  
- 尼奎斯特定理： 最大数据速率 = 2B×log2V（比特/秒）  
  V 以2为底的对数乘以 2B，B为带宽，V为离散等级  
  即 3KHz 的信道不可能超过 6000bps 的速度传输 V=2（二进制）的信号  

## 第三章 数据链路层
- 作用  
  向网络层提供一个定义良好的服务接口  
  处理传输错误  
  调节数据流，确保慢速的接收方不会被快速的发送方淹没
  
- 帧的管理构成了数据链路层的核心  
  从网络层获得数据包，然后将这些数据包封装成帧（frame）以便传输  
  
- 三种服务  
  无确认的无连接  
  有确认的无连接  
  有确认的有链接
  1. 创建连接，初始化各种变量和计数器  
  2. 传输
  3. 释放连接
  
- 基于反馈的流量控制
- 基于速率的流量控制
- NIC - Network Interface Cards - 网络接口卡  
  FEC - Forward Error Correction - 前向纠错  
  纠错码 - error-correcting code  
  检错码 - error-detecting code
  
- CRC - Cyclic Redundancy Check - 循环冗余校验码  
  PPP - Point-to-Point Protocol - 面向字节，用于无确认的无连接  
  HDLC - High-Level Data Link Control - 高级数据链路控制。面向比特，滑动窗口、确认、超时机制
  
## 第四章 介质访问控制子层
- MAC - Mediun Access Control - 介质访问控制  