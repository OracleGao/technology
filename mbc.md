[toc]

## 版本信息
### 20180919 v1.1.2 By 小砾
- 音频、图文识别结果协议增加识别时间属性

### 20180917 v1.1.1 By 小砾
- 识别处理公共部分增加port端口属性
- 识别结果协议视频部分调整

### 20180915 v1.0.0 By 小砾
- 创建文档

# kafka消息队列通信

## 爬虫协议【媒体爬虫引擎->媒体处理引擎】
## topic
- topic-cp

### 公共部分
| name         | datatype | desc | 
| :--:         | :------: | ---  |
| id           | string   | uuid，全局唯一 |
| title        | string   | 标题 |
| appId        | long     | 所属应用id |
| protocolType | string   | 协议类型，http, https, hls, rmtp|  
| url          | string   | 获取媒体的url，对于hls协议，是m3u8文件url |
| originType   | int      | 来源类型：1.盲采，2送检 |
| mediaType    | int      | 媒体类型：1.视频，2音频，3.图文 |
| crawlAt      | datetime | 爬取时间 yyyy-mm-dd HH:mi:ss.SSS |

### 例子
- 视频
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "hls",
    "url": "http://host/test.m3u8",
    "originType": "1",
    "mediaType": "1",
    "crawlAt": "2018-09-15 11:36:01.001"
}
```
- 音频
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "3",
    "protocolType": "http",
    "url": "http://audio.xmcdn.com/group45/M03/45/15/wKgKjluIACHgXzCAAE2DPX0Lz04857.m4a",
    "originType": "1",
    "mediaType": "2",
    "crawlAt": "2018-09-15 11:36:01.123"
}
```

- 图文
``` json
{
    "id": "201809150001",
    "title": "习近平出席第四届东方经济论坛全会并致辞",
    "appId": "2",
    "protocolType": "https",
    "url": "https://www.toutiao.com/a6600592699005338116/",
    "originType": "1",
    "mediaType": "3",
    "crawlAt": "2018-09-15 15:36:01.721"
}
```

## 媒体处理消息协议【媒体处理引擎->识别告警引擎】
## topic
- topic-pr

### 公共部分
| name            | datatype | desc | 
| :--:            | :------: | ---  |
| host            | string   | 所在节点ip / 主机名 |
| port            | int      | 所在节点文件服务器端口 |
| processBeginAt  | datetime | 处理开始时间 yyyy-mm-dd HH:mi:ss.SSS |
| processEndAt    | datetime | 处理结束时间 yyyy-mm-dd HH:mi:ss.SSS |
| processDuration | long     | 处理时长单位毫秒 |

## 视频处理消息
- 引用@爬虫协议->公共部分
- 引用@处理识别协议->公共部分

| name          | datatype | desc | 
| :--:          | :------: | ---  |
| segNum        | string   | 段编号，从m000001开始 |
| segType       | int      | 段类型：1开始段，2普通段，3结束段 |
| segPlayTime   | long     | 段播放时长单位ms |
| segBeginTime  | long     | 段开始时间，相对于整个视频开头的时长,单位ms |
| kbps          | int      | 码率 |
| playTime      | long     | 累计播放时长，当segType==3时填写总时长 |
| segUrl        | string   | 段落url |
| picFrameRate  | long     | 视频提取图片的时间间隔，单位毫秒 |
| picFrameUrl   | string   | 图片帧url,图片获取url: ${picFrameUrl}/{$pics[i]}。0 <= i < picCount |
| picFmt        | string   | 图片格式 |
| pics          | pics[]   | 图片名称数组 |
| picCount      | int      | 图片数量 |
| audioUrl      | string   | 视频提取的音频url，音频获取url：${audioUrl}/{$audios[i]}。其中0 <= i < ${audioCount} |
| audioFmt      | string   | 音频格式 |
| audioKbps     | int      | 音频码率 |
| audios        | string[] | 音频数组 |
| audioCount    | int      | 音频数量 |

### 视频处理消息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "hls",
    "url": "http://host/test.m3u8",
    "originType": "1",
    "mediaType": "1",
    "crawlAt": "2018-09-15 11:36:01.001",
    "segNum": "m000001",
    "segType": "1",
    "segPlayTime": "20000",
    "segBeginTime": "0"
    "kbps": "256",
    "playTime": "20000",
    "segUrl": "http://work1:8080/videos/tx000001",
    "picFrameRate": "3000",
    "picFrameUrl": "http://work1:8080/videos/tx000001/pics",
    "pics": [
        "f0001.png",
        "f0002.png",
        "f0003.png"
    ],
    "picCount": "3",
    "audioUrl": "http://work1:8080/videos/tx000001/audios",
    "audioFmt": "mp3",
    "audioKbps": "64",
    "audios": [
        "a0001.mp3",
        "a0002.mp3"
    ],
    "audioCount": "2",
    "host": "work1",
    "port": "8008",
    "processBeginAt": "2018-09-15 14:52:02.324",
    "processEndAt": "2018-09-15 14:55:02.324",
    "processDuration": "180000"
}
```

### 音频处理消息
- 引用@爬虫协议->公共部分
- 引用@处理识别协议->公共部分

| name     | datatype | desc | 
| :--:     | :------: | ---  |
| kbps     | int      | 码率 |
| audioUrl | string   | 音频url |
| audioFmt | string   | 音频格式 |

### 音频处理消息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "https",
    "url": "http://audio.xmcdn.com/group45/M03/45/15/wKgKjluIACHgXzCAAE2DPX0Lz04857.m4a",
    "originType": "1",
    "mediaType": "2",
    "crawlAt": "2018-09-15 11:36:01.123",
    "kbps": "64",
    "audioUrl": "http://work1:8080/audios/xxxx/xxxxxxxxxx.m4a",
    "audioFmt": "m4a",
    "host": "work1",
    "port": "8008",
    "processBeginAt": "2018-09-15 14:52:02.234",
    "processEndAt": "2018-09-15 14:55:02.234",
    "processDuration": "180000"
}
```

### 图文处理消息
- 引用@爬虫协议->公共部分
- 引用@处理识别协议->公共部分

| name           | datatype | desc | 
| :--:           | :------: | ---  |
| tiUrl          | string   | 图文url目录 |
| textUrl        | string   | 文本url |
| picUrl         | string   | 图片url，图片获取url: ${picUrl}/{$pics[i].name} 0 < i < ${picCount} |
| pics           | object[] | 图片数组 |
| pics.name      | string   | 图片名称 |
| pics.fmt       | string   | 图片格式 png, jpg, unkonw |
| pics.originUrl | string   | 图片来源链接 |
| picCount       | int      | 图片数量 |

### 图文处理消息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "2",
    "protocolType": "https",
    "url": "https://www.toutiao.com/a6600592699005338116/",
    "originType": "1",
    "mediaType": "3",
    "crawlAt": "2018-09-15 11:36:01",
    "tiUrl": "http://work2:8080/ti",
    "textUrl": "http://work2:8080/tis/xxxxxxxx/text.txt",
    "picUrl": "http://work2:8080/tis/xxxxxxxx/pics",
    "pics": [
        {    
            "name": "xxx.png",
            "fmt": "png",
            "originUrl": "http://p3.pstatp.com/large/pgc-image/1536820244582fa8828424f"
        },
        {
            "name": "yyy.jpg",
            "fmt": "jpg",
            "originUrl": "http://p9.pstatp.com/large/pgc-image/153682024511643a15620f0"
        }
    ],
    "picCount": "2",
    "host": "work2",
    "port": "8008",
    "processBeginAt": "2018-09-15 14:52:02.234",
    "processEndAt": "2018-09-15 14:55:02.234",
    "processDuration": "180000"
}
```

## 识别告警协议【识别告警引擎->资源池】
## topic
- 视频：topic-rr-video
- 音频：topic-rr-audio
- 图文：topic-rr-txtimg

### 公共部分
| name          | datatype | desc | 
| :--:          | :------: | ---  |
| alarmCount    | int      | 告警个数，【对于视频段类型，当segType==3 结束段时填写】 |
| alarms        | object[] | 告警数组，【仅适用于音频和图文】 |
| alarms.score  | int      | 综合识别得分，以整型存储小数，后2位为小数部分【仅适用于音频和图文】  |
| alarms.reason | string   | 告警原因【仅适用于音频和图文】  |
| alarms.type   | int      | 告警类型14种预警类型 1-14【仅适用于音频和图文】  |
| alarms.level  | int      | 告警等级 1黄 2红【仅适用于音频和图文】  |
| recoBegin     | string   | 识别开始，yyyy-mm-dd HH:mi:ss.SSS |
| recoEnd       | string   | 识别结束，yyyy-mm-dd HH:mi:ss.SSS |
| recoDuration  | long     | 识别时间单位毫秒 |

### 视频识别信息
- 引用#处理识别协议->公共部分

| name              | datatype | desc | 
| :--:              | :------: | ---  |
| lrc               | string   | 音频识别结果，lrc格式(只保留时间和文字) |

### 视频识别信息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "hls",
    "url": "http://host/test.m3u8",
    "originType": "1",
    "mediaType": "1",
    "crawlAt": "2018-09-15 11:36:01.001",
    "segNum": "m000001",
    "segType": "3",
    "segPlayTime": "20000",
    "segBeginTime": "0"
    "kbps": "256",
    "playTime": "20000",
    "segUrl": "http://work1:8080/videos/tx000001",
    "picFrameRate": "3000",
    "picFrameUrl": "http://work1:8080/videos/tx000001/pics",
    "pics": [
        "f0001.png",
        "f0002.png",
        "f0003.png"
    ],
    "picCount": "3",
    "audioUrl": "http://work1:8080/videos/tx000001/audios",
    "audioFmt": "mp3",
    "audioKbps": "64",
    "audios": [
        "a0001.mp3",
        "a0002.mp3"
    ],
    "audioCount": "2",
    "host": "work1",
    "port": "8008",
    "processBeginAt": "2018-09-15 14:52:02.324",
    "processEndAt": "2018-09-15 14:55:02.324",
    "processDuration": "180000"
    "alarmCount": "1",
    "lrc": "[00:29.72]淋着瓢泼大雨\n[00:31.07]做疯狂的事情\n[00:32.38]易烊千玺：\n[00:32.68]也许已经不流行\n[00:34.39]创意也没多新颖",
    "recoBegin": "2018-09-16 16:34:15.437",
    "recoEnd": "2018-09-16 16:54:26.654",
    "recoDuration": "34721",
}
```

### 视频告警信息
- 引用@爬虫协议->公共部分

| name                    | datatype | desc | 
| :--:                    | :------: | ---  |
| alarm                   | object   | 告警对象 |
| alarm.absoluteBeginAt   | long     | 告警绝对开始时间（相对于整个视频开始时间） |
| alarm.absoluteEndAt     | long     | 告警绝对结束时间（相对于整个视频开始时间） |
| alarm.duration          | long     | 告警时长 |
| alarm.reason            | stirng   | 告警原因 |
| alarm.type              | int      | 告警类型14种预警类型 1-14 |
| alarm.level             | int      | 告警等级 1黄 2红 |
| alarm.score             | int      | 综合识别得分，以整型存储小数，后2位为小数部分 |
| alarm.lrc               | string   | 告警期间音频识别结果，lrc格式(只保留时间和文字) | 
| alarm.recoAt           | string   | 识别时间 yyyy-mm-dd HH:mi:ss.SSS |

### 视频告警信息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "hls",
    "url": "http://host/test.m3u8",
    "originType": "1",
    "mediaType": "1",
    "crawlAt": "2018-09-15 11:36:01.001",
    "alarm" : {
        "absoluteBeginAt": "12100",
        "absoluteBeginEnd": "24300",
        "duration": "12200",
        "reason": "政治敏感话题告警",
        "type": "2",
        "level": "1",
        "score": "79",
        "lrc": "[00:29.72]淋着瓢泼大雨\n[00:31.07]做疯狂的事情\n[00:32.38]易烊千玺：\n[00:32.68]也许已经不流行\n[00:34.39]创意也没多新颖",
        "recoAt": "2018-09-15 11:38:03.221"
    }
}
```

### 音频识别告警信息
- 引用#处理识别协议->公共部分
- 引用#识别结果协议->公共部分

| name              | datatype | desc | 
| :---------------: | :------: | ---  |
| alarms.beginAt    | long     | 告警开始时间 |
| alarms.beginLrc   | string   | 告警开始的识别lrc段 |
| alarms.endAt      | long     | 告警结束时间 |
| alarms.endLrc     | string   | 告警结束的识别lrc段 |
| alarms.content    | string   | 告警识别的文字内容 |
| alarms.score      | int      | 识别得分，以整型存储小数，后2位为小数部分 |
| alarms.recoAt     | string   | 识别时间，yyyy-mm-dd HH:mi:ss.SSS |
| lrc               | string   | 音频识别结果，lrc格式(只保留时间和文字) |

### 音频识别告警信息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "https",
    "url": "http://audio.xmcdn.com/group45/M03/45/15/wKgKjluIACHgXzCAAE2DPX0Lz04857.m4a",
    "originType": "1",
    "mediaType": "2",
    "crawlAt": "2018-09-15 11:36:01",
    "kbps": "64",
    "audioUrl": "http://work1:8080/audios/xxxx/xxxxxxxxxx.m4a",
    "audioFmt": "m4a",
    "host": "work1",
    "port": "8008",
    "processBeginAt": "2018-09-15 14:52:02.234",
    "processEndAt": "2018-09-15 14:55:02.234",
    "processDuration": "180000",
    "alarmCount": "1",
    "alarms": [
        {
            "reason": "涉暴",
            "type": "2",
            "level": "2",
            "score": "82",
            "beginAt": "12000",
            "beginLrc": "[00:31.07]",
            "endAt": "16000",
            "endLrc": "[00:34.39]",
            "content": "[00:31.07]做疯狂的事情\n[00:32.38]易烊千玺：\n[00:32.68]也许已经不流行\n[00:34.39]创意也没多新颖"
        }
    ],
    "lrc": "[00:29.72]淋着瓢泼大雨\n[00:31.07]做疯狂的事情\n[00:32.38]易烊千玺：\n[00:32.68]也许已经不流行\n[00:34.39]创意也没多新颖\n[00:35.97]王源：\n[00:36.34]可我的这一颗真心\n[00:37.02]是喜欢你 oh",
    "recoBegin": "2018-09-16 16:34:15.437",
    "recoEnd": "2018-09-16 16:54:26.654",
    "recoDuration": "34721",
}
```
### 图文告警信息
- 引用#处理识别协议->公共部分
- 引用#识别结果协议->公共部分

| name              | datatype | desc | 
| :---------------: | :------: | ---  |
| alarms.mediaType  | int      | 告警媒体类型 1.文本 2.图片 |
| alarms.content    | string   | 当告警类型是1时，告警文字内容，2时为图片名称 |
| alarms.index      | int      | 告警开始文本字符串位置（从0开始计算），仅当告警类型为1时生效 |
| alarms.length     | int      | 告警字符长度，仅当告警类型为1时生效 |
| alarms.recoAt     | string   | 识别时间，yyyy-mm-dd HH:mi:ss.SSS |

### 图文告警信息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "2",
    "protocolType": "https",
    "url": "https://www.toutiao.com/a6600592699005338116/",
    "originType": "1",
    "mediaType": "3",
    "crawlAt": "2018-09-15 11:36:01",
    "tiUrl": "http://work2:8080/ti",
    "textUrl": "http://work2:8080/tis/xxxxxxxx/text.txt",
    "picUrl": "http://work2:8080/tis/xxxxxxxx/pics",
    "pics": [
        {    
            "name": "xxx.png",
            "fmt": "png",
            "originUrl": "http://p3.pstatp.com/large/pgc-image/1536820244582fa8828424f"
        },
        {
            "name": "yyy.jpg",
            "fmt": "jpg",
            "originUrl": "http://p9.pstatp.com/large/pgc-image/153682024511643a15620f0"
        }
    ],
    "picCount": "2",
    "host": "work2",
    "port": "8008",
    "processBeginAt": "2018-09-15 14:52:02.234",
    "processEndAt": "2018-09-15 14:55:02.234",
    "processDuration": "180000",
    "alarmCount": "2",
    "alarms": [
        {
            "reason": "涉暴",
            "type": "2",
            "level": "2",
            "score": "92",
            "mediaType": "1",
            "content": "备战之姿迎接第18个国防教育日的到来",
            "index": "45",
            "length": "18",
            "recoAt": "2018-09-16 16:34:16.437"
        },
        {
            "reason": "涉暴",
            "type": "2",
            "level": "2",
            "score": "92",
            "mediaType": "2",
            "content": "xxx.png",
            "recoAt": "2018-09-16 16:44:16.437"
        }
    ],
    "recoBegin": "2018-09-16 16:34:15.437",
    "recoEnd": "2018-09-16 16:54:26.654",
    "recoDuration": "34721",
}
```

# 处理过程存储协议
## 节点目录结构
- 基础目录视频videos, 音频audios, 图文tis（text image）

### videos
- 以目录形式存放处理过的视频，视频uuid作为目录名称，每个视频目录结构如下：
  - crawl.json存放爬取消息
  - ts存放m3u8文件和所有可播放的ts视频文件，ts文件编号从t00000001开始
  - frames 图片帧目录，存储所有从视频中提取的图片，编号从f000000001开始
  - frames.json描述图片编号，对应的播放时间点，以及与ts文件对应关系。
    | name                    | datatype | desc | 
    | :---------------------: | :------: | ---- |
    | ${picName}              | string   | 图片名称 |
    | ${picName}.absoluteTime | long     | 相对于整个视频开始的绝对时间 |
    | ${picName}.relativeTime | long     | 相对于ts文件的播放时间 |
    | ${picName}.ts           | string   | ts文件名 |
    - 格式举例
    ``` json
       [
           "f00000001.png": {
               "absoluteTime": "3000",
               "relativeTime": "3000",
               "ts": "ts000000001.ts"
           },
           "f00000002.png": {
               "absoluteTime": "6000",
               "relativeTime": "3000",
               "ts": "ts000000001.ts"
           },
           ...
           "f000000051.png": {
               "absoluteTime": "153000",
               "relativeTime": "2300",
               "ts": "ts0000000012.ts"
           }
       ]
    ```
  - audios 音频存储目录，存储所有从ts视频文件中提取出来的音频文件，编号与ts文件编号对应
  - process 存放所有分段处理结果消息文件（推送给kafka的json），分段编号.json

### audios
- 以目录形式存放处理过的音频，音频uuid作为目录名称，每个音频目录结构如下：
  - crawl.json存放爬取消息
  - 音频文件 id.格式，比如xxxxx.m4a
  - prcoess.json 处理结果消息文件（推送给kafka的json）

### tis
- 以目录形式存放处理过的图文，图文uuid作为目录名称，每个图文目录结构如下：
  - crawl.json存放爬取消息
  - 文本文件 id.格式，比如xxxxx.txt
  - pics，存放所有图文中的图片，图片编号p00000001开始
  - prcoess.json 处理结果消息文件（推送给kafka的json）

# 告警媒体存储协议
## 节点目录结构
- 基础根目录包括alarms/videos, alarms/audios, alarms/tis

### vidoes
- 每个存在告警的视频文件根目录以，视频文件的uuid作为目录名称
  - 每条告警以及
  - 
