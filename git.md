## Installation
### Windows
- Download url: https://github.com/git-for-windows/git/releases/tag/v2.14.1.windows.1

## Peer’s Certificate issuer is not recognized
- 忽略证书
``` shell
git config --global http."sslVerify" false
```

- Ref: [【Peer’s Certificate issuer is not recognized】错误解决方法](https://www.jianshu.com/p/fa71d97dcde0)

## Git Deploy Key
- 自动化构建时，使用免密方式拉取Git代码库的依赖
- 存放代码库拉取秘钥
- 这种方式仅用于拉取git依赖代码自动化构建

## git deploy key 配置
- Settings->Deploy Keys->Add Deploy Key
- 输入title和粘贴public key文件内容id_rsa.pub

## 构建环境配置
- 将http方式更换为ssh方式（仅限自动化构建环境使用）
``` bash
git config --global url."git@www.gitee.com:".insteadOf "https://www.gitee.com/"
```

- 配置私钥编辑~/.ssh/config(如果没有新建)
```
Host www.gitee.com
  User git
  HostName www.gitee.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa
```
