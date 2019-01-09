# GlusterFS安装

## Ubuntu 16.04
### Gluster Server安装
- server端安装自带client端
- 安装指令
``` shell
apt-get update
apt-get install -y glusterfs-server
```
- 验证指令
``` shell
gluster --version
```
``` txt
glusterfs 3.7.6 built on Dec 25 2015 20:50:46
Repository revision: git://git.gluster.com/glusterfs.git
Copyright (c) 2006-2011 Gluster Inc. <http://www.gluster.com>
GlusterFS comes with ABSOLUTELY NO WARRANTY.
You may redistribute copies of GlusterFS under the terms of the GNU General Public License.
```
