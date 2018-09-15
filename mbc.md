[toc]

# kafka消息队列通信
## 爬虫协议
### 公共部分
| name         | datatype | required | default | desc | 
| :--:         | :------: | :------: | :-----: | ---  |
| id           | string   | yes      |         | uuid，全局唯一 |
| title        | string   | yes      |         | 标题 |
| appId        | long     | yes      |         | 所属应用id |
| protocolType | string   | yes      |         | 协议类型，http, https, hls, rmtp|  
| url          | string   | yes      |         | 获取媒体的url，对于hls协议，是m3u8文件url |
| originType   | int      | yes      |         | 来源类型：1.盲采，2送检 |
| mediaType    | int      | yes      |         | 媒体类型：1.视频，2音频，3.图文 |
| crawlAt      | datetime | yes      |         | 爬取时间 |

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
    "crawlAt": "2018-09-15 11:36:01"
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
    "crawlAt": "2018-09-15 11:36:01"
}
```

- 图文
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "2",
    "protocolType": "https",
    "url": "https://www.toutiao.com/a6600592699005338116/",
    "originType": "1",
    "mediaType": "3",
    "crawlAt": "2018-09-15 15:36:01"
}
```

## 处理识别协议
### 公共部分
| name            | datatype | required | default | desc | 
| :--:            | :------: | :-----: | :------: | ---  |
| host            | string   | yes     |          | 所在节点ip / 主机名 |
| processBeginAt  | datetime | yes     |          | 处理开始时间 |
| processEndAt    | datetime | yes     |          | 处理结束时间 |
| processDuration | long     | yes     |          | 处理时长单位毫秒 |

### 视频部分
- 引用@爬虫协议->公共部分
- 引用@处理识别协议->公共部分

| name          | datatype | required | default | desc | 
| :--:          | :------: | :-----: | :------: | ---  |
| segNum        | string   | yes     |          | 段编号，从m000001开始 |
| segType       | int      | yes     |          | 段类型：1开始段，2普通段，3结束段 |
| segPlayTime   | long     | yes     |          | 段播放时长单位ms |
| kbps          | int      | yes     |          | 码率 |
| playTime      | long     | no      |          | 累计播放时长，当segType==3时填写总时长 |
| segUrl        | string   | yes     |          | 段落url |
| picFrameRate  | long     | yes     |          | 视频提取图片的时间间隔，单位毫秒 |
| picFrameUrl   | string   | yes     |          | 图片帧url,图片获取url: ${picFrameUrl}/{$picNum}.${picFmt}。其中picNum可有picBeinNum开始，每次自增1，到picEndNum结束 |
| picFmt        | string   | yes     |          | 图片格式 |
| pics          | string[] | yes     |          | 图片名称数组 |
| picCount      | int      | yes     |          | 图片数量 |
| audioUrl      | string   | yes     |          | 视频提取的音频url，音频获取url：${audioUrl}/{$audios[i]}。其中0 < i < ${audioCount} |
| audioFmt      | string   | yes     |          | 音频格式 |
| audioKbps     | int      | yes     |          | 音频码率 |
| audios        | string[] | yes     |          | 音频数组 |
| audioCount    | int      | yes     |          | 音频数量 |

### 视频部分消息举例
``` json
{
    "id": "xxxx",
    "title": "xxx",
    "appId": "1",
    "protocolType": "hls",
    "url": "http://host/test.m3u8",
    "originType": "1",
    "mediaType": "1",
    "crawlAt": "2018-09-15 11:36:01",
    "segNum": "m000001",
    "segType": "1",
    "segPlayTime": "20000",
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
    "processBeginAt": "2018-09-15 14:52:02",
    "processEndAt": "2018-09-15 14:55:02",
    "processDuration": "180000"
}
```

### 音频部分
- 引用@爬虫协议->公共部分
- 引用@处理识别协议->公共部分

| name     | datatype | required | default | desc | 
| :--:     | :------: | :-----:  | :-----: | ---  |
| kbps     | int      | yes      |         | 码率 |
| audioUrl | string   | yes      |         | 音频url |
| audioFmt | string   | yes      |         | 音频格式 |

### 音频部分消息举例
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
    "processBeginAt": "2018-09-15 14:52:02",
    "processEndAt": "2018-09-15 14:55:02",
    "processDuration": "180000"
}
```


### 图文部分
- 引用@爬虫协议->公共部分
- 引用@处理识别协议->公共部分

| name           | datatype | required | default | desc | 
| :--:           | :------: | :-----:  | :-----: | ---  |
| tiUrl          | string   | yes      |         | 图文url目录 |
| textUrl        | string   | yes      |         | 文本url |
| picUrl         | string   | yes      |         | 图片url，图片获取url: ${picUrl}/{$pics[i].name} 0 < i < ${picCount} |
| pics           | object[] | yes      |         | 图片数组 |
| pics.name      | string   | yes      |         | 图片名称 |
| pics.fmt       | string   | yes      |         | 图片格式 png, jpg, unkonw |
| pics.originUrl | string   | yse      |         | 图片来源链接 |
| picCount       | int      | yes      |         | 图片数量 |

### 图文部分消息举例
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
    "textUrl": "http://work2:8080/ti/xxxxxxxx/text",
    "picUrl": "http://work2:8080/ti/xxxxxxxx/pic",
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
    "processBeginAt": "2018-09-15 14:52:02",
    "processEndAt": "2018-09-15 14:55:02",
    "processDuration": "180000"
}
```

## 识别结果协议
### 视频结果

### 音频结果

### 图文结果


# 处理过程存储协议
## 节点目录结构
- 基础目录视频videos, 音频audios, 图文tis（text image）

### videos
- 以目录形式存放处理过的视频，视频id作为目录名称，每个视频目录结构如下：
  - crawl.json存放爬取消息
  - ts存放m3u8文件和所有可播放的ts视频文件，ts文件编号从t00000001开始
  - frames 图片帧目录，存储所有从视频中提取的图片，编号从f000000001开始
  - audios 音频存储目录，存储所有从ts视频文件中提取出来的音频文件，编号与ts文件编号对应
  - process 存放所有分段处理结果消息文件（推送给kafka的json），分段编号.json

### audios
- 以目录形式存放处理过的音频，音频id作为目录名称，每个音频目录结构如下：
  - crawl.json存放爬取消息
  - 音频文件 id.格式，比如xxxxx.m4a
  - prcoess.json 处理结果消息文件（推送给kafka的json）

### tis
- 以目录形式存放处理过的图文，图文id作为目录名称，每个图文目录结构如下：
  - crawl.json存放爬取消息
  - 文本文件 id.格式，比如xxxxx.txt
  - pics，存放所有图文中的图片，图片编号p00000001开始
  - prcoess.json 处理结果消息文件（推送给kafka的json）
