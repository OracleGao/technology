# awk命令示例

## sql group by
- file 'data'
```text
112.224.17.226 - - [13/Aug/2018:15:13:22 +0800] "GET /v1/unreads
112.224.17.226 - - [13/Aug/2018:15:13:22 +0800] "GET /v1/unreads/
112.224.17.226 - - [12/Aug/2018:15:13:22 +0800] "GET /v1/unreads
183.209.247.111 - - [13/Aug/2018:15:13:22 +0800] "GET /v1/groups
183.209.247.111 - - [13/Aug/2018:15:13:22 +0800] "GET /v1/unread
183.209.247.111 - - [13/Aug/2018:15:13:22 +0800] "GET /v1/messag
112.224.17.226 - - [13/Aug/2018:15:13:22 +0800] "POST /v1/group
183.209.247.111 - - [13/Aug/2018:15:13:23 +0800] "POST /v1/profile?s
183.209.247.111 - - [13/Aug/2018:15:13:23 +0800] "POST /v1/unreads/g
223.104.6.127 - - [13/Aug/2018:15:13:23 +0800] "GET /v1/unreads/
```
- group by ip
```shell
cat data | awk -F ' ' '{a[$1] += 1}END{for (i in a){printf "%s,%d\n", i, a[i]} }'
```
```shell

```

- group by ip date method
```shell
cat data | awk -F ' ' '{a[$1","substr($4, 2, 11)","substr($6, 2)] += 1}END{for (i in a){printf "%s,%d\n", i, a[i]} }'
```
```shell

```
