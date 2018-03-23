# certificate verify failed 【php 7.1】
## Desc
- operation failed with code 1. OpenSSL Error messages: error:14090086:SSL routines:ssl3_get_server_certificate:certificate verify failed

## solution
这是因为连接packagist.org需要ssl证书，给php配置默认ssl证书可以解决

下载证书 http://curl.haxx.se/ca/cacert.pem 

修改php.ini

curl.cainfo=/etc/ssl/certs/cacert.pem
openssl.cafile=/etc/ssl/certs/cacert.pem
openssl.capath=/etc/ssl/certs

必须已启用openssl, curl扩展
