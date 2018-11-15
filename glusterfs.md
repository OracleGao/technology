# volume create: gfsv_service: failed: Host dev02 is not in 'Peer in Cluster' state
## 环境
- 阿里云(aws)Nat环境，弹性公网ip(ECS公网)
- 3台机器（设192.168.1.101-102为ECS公网ip）
  - 192.168.1.101 / dev01
  - 192.168.1.102 / dev02
  - 192.168.1.103 / dev03
## solution
1. dev01上移除节点
``` shell
gluster peer detach dev02
gluster peer detach dev03
```
2. 修改每台机器的/etc/hosts如下
- 192.168.1.101 / dev01
``` txt
127.0.0.1        dev01
192.168.1.102    dev02
192.168.1.103    dev03
```
- 192.168.1.102 / dev02
``` txt
192.168.1.101    dev01
127.0.0.1        dev02
192.168.1.103    dev03
```
- 192.168.1.103 / dev03
``` txt
192.168.1.101    dev01
192.168.1.102    dev02
127.0.0.1        dev03
```
3. dev01上重新加入节点
``` shell
gluster peer probe dev02
gluster peer probe dev03
```

## refs
- https://www.alibabacloud.com/help/zh/doc-detail/32321.htm
- https://serverfault.com/questions/531359/why-cant-i-create-this-gluster-volume
