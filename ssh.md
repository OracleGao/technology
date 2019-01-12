# SSH
## ssh tunnel 反向代理(内网穿透)
- 通过外网主机的特定端口访问内网主机特定端口的网络服务

### 设备信息
- 47.93.199.97 外网代理主机，用来代理内网主机的网络服务。不能访问192.168.10.2内网主机
- 192.168.10.2 内网主机，可以通过ssh客户端登录外网47.93.199.97主机

### 示例场景
- 通过ssh root@47.93.199.97 -p 2222的方式登录192.168.10.2主机（通过47.93.199.97的2222端口，访问192.168.10.2的22端口的ssh服务）
- 通过47.93.199.97的23306端口访问192.168.102主机的3306端口的mysql服务

### 环境准备
- 使用root分别能够登录 47.93.199.97， 192.168.10.2(非root请使用sudo切换root权限)

#### 外网代理主机（47.93.199.97）
- 允许代理的服务绑定到0.0.0.0地址，允许其他主机远程访问服务端口
- 编辑/etc/ssh/sshd_config， 增加或者设置GatewayPorts配置
- 需要重启ssh服务
``` txt
GatewayPorts yes
```

- 保持代理服务端与客户端长时间有效
- 编辑/etc/ssh/sshd_config， 增加或者设置ClientAliveInterval，ClientAliveCountMax 配置
- 需要重启ssh服务
``` shell
ClientAliveInterval 60     #服务端主动向客户端请求响应的间隔
ClientAliveCountMax 10     #服务器发出请求后客户端没有响应的次数达到一定值就自动断开
```

- 重启ssh服务
``` shell
service ssh restart
```

#### 内网被代理主机（192.168.10.2）
- 保持客户端与代理服务端长时间有效
- 编辑/etc/ssh/ssh_config(服务端的配置文件sshd_config，注意区别)，增加或设置TCPKeepAlive，ServerAliveInterval 配置
``` txt
TCPKeepAlive yes         #保持与服务端的tcp连接
ServerAliveInterval 60   #客户端主动向服务端请求响应的间隔
```

### 代理搭建
#### ssh代理服务
- 在内网主机执行'ssh -NfR <remote binding host>:<remote bing port>:<local host>:<local port> <remote login user>@<remote host>'指令，然后输入密码，即可完成代理服务搭建
- 在192.168.10.2内网主机执行以下指令，搭建ssh代理服务
``` shell
ssh -NfR *:2222:127.0.0.1:22 root@47.93.199.97
```
  
#### mysql代理服务
- 在192.168.10.2内网主机执行以下指令，搭建mysql代理服务。通过47.93.199.97的23306端口即可访问内网主机192.168.10.2的3306端口的mysql服务
``` shell
ssh -NfR *:23306:127.0.0.1:3306 root@47.93.199.97
```

### Refs
- [ssh tunnel 三种模式](https://github.com/caojx-git/learn/blob/master/notes/linux/ssh%20tunnel%20%E4%B8%89%E7%A7%8D%E6%A8%A1%E5%BC%8F_2.md)
