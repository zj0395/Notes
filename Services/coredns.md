# coredns

灵活、可扩展DNS服务器  
https://github.com/coredns/coredns  

## 安装
自行编译或下载二进制包  

## 使用
### 配置
- 配置文件`Corefile`
    ```
    .:53 {
        hosts ./zj-hosts {
            ttl 600 #second
        }
        log
    }
    ```

- host文件`zj-hosts`
    ```
    172.17.0.3 redis.zj
    172.17.0.2 mysql.zj
    ```

### 部署&测试
docker部署`docker run -d --name="test-dns-zj" zj0395/coredns:latest`  
查看容器ip`docker inspect test-dns-zj | grep IPAddress | awk '{print $2}' | awk -F '"' '{print $2}'  | tail -n 1`  
测试`dig @YOURIP redis.zj`  
```
[zj@VM-0-11-ubuntu ~]# dig @172.17.0.6 redis.zj

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> @172.17.0.6 redis.zj
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54118
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: e0b74b6aa72b256b (echoed)
;; QUESTION SECTION:
;redis.zj.			IN	A

;; ANSWER SECTION:
redis.zj.		600	IN	A	172.17.0.3

;; Query time: 0 msec
;; SERVER: 172.17.0.6#53(172.17.0.6)
;; WHEN: Wed Feb 05 14:30:34 CST 2020
;; MSG SIZE  rcvd: 73
```
修改dns配置文件`/etc/resolve.conf`，增加`nameserver YOURIP`；该文件只能指定ip，无法指定端口  
使用`ping redis.zj`进行测试

## 最佳实践
- 作为k8s组件用于服务发现
