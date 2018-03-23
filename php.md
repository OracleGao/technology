# Certificate Verify Failed 【php 7.1】
## Desc
- use file_get_contents() func get https message.
- operation failed with code 1. OpenSSL Error messages: error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify failed

## Solution
这是因为连接packagist.org需要ssl证书，给php配置默认ssl证书可以解决

下载证书 http://curl.haxx.se/ca/cacert.pem 

修改php.ini
``` ini
curl.cainfo=/etc/ssl/certs/cacert.pem
openssl.cafile=/etc/ssl/certs/cacert.pem
openssl.capath=/etc/ssl/certs
```
必须已启用openssl, curl扩展

## Ref
- (给php添加ssl证书)[https://www.cnblogs.com/imbin/p/5207105.html]
