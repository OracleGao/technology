# ssh tunnel 反向代理(内网穿透)
- 在内网主机执行"ssh -NfR <remote binding host>:<remote bing port>:<local host>:<local port> <remote login user>@<remote host>"指令，使用远程主机代理内网上主机服务
- 内网主机能够正常访问47.93.199.97主机，以下示例将内网主机的ssh服务代理到47.93.199.97主机的2222端口，在内网主机上执行下列指令后，输入47.93.199.97主机ssh登录root密码，即可在
``` shell
ssh -NfR *:2222:127.0.0.1:22 root@47.93.199.97
```
- 

## ref
- [ssh tunnel 三种模式](https://github.com/caojx-git/learn/blob/master/notes/linux/ssh%20tunnel%20%E4%B8%89%E7%A7%8D%E6%A8%A1%E5%BC%8F_2.md)
