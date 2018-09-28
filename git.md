## Installation
### Windows
- Download url: https://github.com/git-for-windows/git/releases/tag/v2.14.1.windows.1

## 【Peer’s Certificate issuer is not recognized】错误解决方法
- 执行指令忽略证书认证
``` shell
git config --global http."sslVerify" false
```
### ref
- [【Peer’s Certificate issuer is not recognized】错误解决方法](https://www.jianshu.com/p/fa71d97dcde0)
