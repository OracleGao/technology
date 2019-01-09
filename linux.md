# vim 乱码
edit file ~/.vimrc
```text
set encoding=utf-8
set fileencodings=utf-8,cp936,gb18030,latin1
set termencoding=utf-8
set expandtab
set ts=4
set shiftwidth=4
set nu
syntax on

if has('mouse')
  set mouse-=a
endif
```

# 给普通用户添加sudo权限<ubuntu 16.04>
- 编辑/etc/sudoers,模仿root的设置方式在User privilege specification下方增加 ${username} ALL=(ALL:ALL) ALL，下面是给casia用户添加sudo权限的例子
``` txt
# User privilege specification
root    ALL=(ALL:ALL) ALL
casia   ALL=(ALL:ALL) ALL

```
