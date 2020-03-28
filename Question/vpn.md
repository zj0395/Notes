# vpn相关配置

## 在云机器上运行vpn
每次启动vpn后，需要在机器上添加routes，保证web server和ssh server及其它server工作正常  
方法: 在openvpn配置文件里增加以下配置
```
cript-security 2
up /home/zj/softs/openvpn-config/set_vpn.sh`
```
/home/zj/softs/openvpn-config/set_vpn.sh脚本代码: 
```
#!/bin/bash
# 下4个值可从此命令取到: ip r
IP="172.21.0.11"
DEV="eth0"
SUBNET="172.21.0.0/20"
GW="172.21.0.1"

sudo ip route del table 128 to $SUBNET dev $DEV
sudo ip route del table 128 default via $GW

sudo ip rule add from $IP table 128
sudo ip route add table 128 to $SUBNET dev $DEV
sudo ip route add table 128 default via $GW
```

## 在客户机上运行vpn
为部分需要直连的机器添加route  
- widows: `route ADD 192.144.174.0 MASK 255.255.255.0 192.168.1.1`  
    `192.144.174.0`为目的ip，`192.168.1.1`为本地网关  
- linux: TODO
