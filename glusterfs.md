# 【ERROR】 volume create: gfsv_service: failed: Host dev02 is not in 'Peer in Cluster' state
- 目前发现有两种情况会报这个错误，1首次安装在Nat环境(阿里云)；2重新格式化或者重装，以下分别列出解决方案

## 首次安装
### 环境
- 阿里云(aws)Nat环境，弹性公网ip(ECS公网)
- 3台机器（设192.168.1.101-102为ECS公网ip）
  - 192.168.1.101 / dev01
  - 192.168.1.102 / dev02
  - 192.168.1.103 / dev03
### solution
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

### refs
- https://www.alibabacloud.com/help/zh/doc-detail/32321.htm
- https://serverfault.com/questions/531359/why-cant-i-create-this-gluster-volume

## 重新格式化（系统修复）
### 问题原因
- 重新格式化glusterfs磁盘导致glusterfs在磁盘上的秘钥发生了变化（秘钥chksum是对/var/lib/glusterfs/vols/{vol_name}/info的内容进行计算得出的）。这个时候想移除dev02节点或者重新添加dev02节点都不能移除或添加。不能添加时因为已经存在节点，不能移除是因为秘钥不对了。
### 解决方案
- 这时候可以通过清空dev01(dev02,dev03)上的/var/lib/glusterd/peers目录下的文件并且重启gluster服务（service glusterfs-server stop/start）来是清空操作生效。然后重新添加dev02和dev03节点。接下来创建卷、启动卷、挂载客户端就可以依次执行了。

# 【warning】 WARNING: getfattr not found, certain checks will be skipped..
- install attr
``` shell
apt install attr
```
