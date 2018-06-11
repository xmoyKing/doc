本文档为API接口文档

----

9000端口测试进程使用：
1. 若9000端口的upload地址不可用，则说明测试进程可能出错，使用`ps -aux | grep 9000` 查看是否存在对应端口的进程。
1. 若端口正常，则使用`pm2 list`查看其中`HERE_Start`是否是`绿色online`状态，若为其他状态，可以用`pm2 restart HERE_Start`重启应用。(其中`pm2`是一个进程管理工具)
![pm2-list](/AM-USER/doc/raw/master/images/pm2-list.png)
1. 若还是有问题，则说明代码有错误，使用`pm2 log HERE_Start`查看进程输出的信息，其中包括红色报错信息和绿色接口访问记录。
![pm2-log-HERE-Start](/AM-USER/doc/raw/master/images/pm2-log-HERE-Start.png)
1. 根据其中的红色报错信息查看是否代码有bug，若需要更多详细信息，请查看`/root/.pm2/logs/HERE-Start-error-0.log`文件记录。

----
`host:port/common_access/method`

请求中都会带UID，表示用户唯一标识（目前暂定QQ号）
响应中有三个字段：
- code 表示响应码，0为成功，其他为错误，具体见API
- message 表示code对应的响应信息，
- data 为返回的数据，分两种类型，数组/对象，
  - 返回多个相同类型的数据用数组，即使该数组只有1个元素
  - 返回单种数据的用对象，
  - 若不需要返回数据，则为空对象

## 1.QQ登录
save_user_info
### request：
```json
{
  "UID": 1223457,
  "nickname": "youngou",
  "profile_photo_url": "https://path/profile",
  "sex": 1
}
```
### response：
```json
{
  "code": 1,
  "message": "已存在该用户",
  "data": {}
}
```

# 地图加载
## 1.获取用户所有点亮景点
get_light_area
### request：
```json
{
  "UID": 1223457
}
```
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": [
    {
      "TID":100,
      "name":"腾讯大厦",
      "latitude":22.540503,
      "longitude":113.934528,
      "level":3,
      "state":1,
      "info": {
        "desc": "该景点描述",
        "pics": ["用户在改景点上传的图片1", "..."],
        "address": "景点地址",
        "其他字段": "可自行添加"
      }
    },
    {"...": "..."}
  ]
}
```

state表示是否点亮，0未点亮，1已点亮，在此API中，返回的景点信息的state应该都是1

level表示景点级别，目前至少有4种

其他景点信息都作为info附加在其内

## 2.获取附近景点
get_nearby_spot 返回Lat & Lng附近5公里以内的所有景点信息，即该用户点亮景点state未1，未点亮景点state为0。

### request：
```json
{
  "UID": 1223457,
  "Lat": 456564.111,
  "Lng": 1564.1515,
  "radius": 5
}
```
Lat表示纬度
Lng表示经度
radius表示半径，单位公里
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": [
    {
      "TID":100,
      "name":"腾讯大厦",
      "latitude":22.540503,
      "longitude":113.934528,
      "level":3,
      "state":1,
      "info": {
        "desc": "该景点描述",
        "pics": ["用户在改景点上传的图片1", "..."],
        "address": "景点地址",
        "其他字段": "可自行添加"
      }
    },
  ]
}
```
此API中，返回景点的state有0，有1，

## 3.获取景点详细信息
get_spot_detail
### request：
```json
{
  "UID": 1223457,
  "TID": 411
}
```

### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": 
    {
      "TID":100,
      "name":"腾讯大厦",
      "latitude":22.540503,
      "longitude":113.934528,
      "level":3,
      "state":1,
      "info": {
        "desc": "该景点描述",
        "pics": ["用户在改景点上传的图片1", "..."],
        "address": "景点地址",
        "其他字段": "可自行添加"
      }
    }
}
```


## 4.获取景点评论信息
get_spot_comments
### request：
```json
{
  "UID": 1223457,
  "TID": 411
}
```
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": [
      {
        "UID": 1223457,
        "nickname": "youngou",
        "profile_photo_url": "https://path/profile",
        "sex": 1,
        "comment": "景点很棒",
        "timestamp": "2017-05-06 18:20",
      }
  ]
}
```

## 5.点亮景点
light_spot
### request：
```json
{
  "UID": 1223457,
  "TID": 5,
  "Lat": 456564.111,
  "Lng": 1564.1515,
}
```
Lat表示纬度
Lng表示经度
TID表示景点ID
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": {
    "points": 10,
  }
}
```
points表示当前用户点亮景点获取到的积分

## 6.发表评论
remark_spot
### request：
```json
{
  "UID": 1223457,
  "TID": 5,
  "comment": "纯文本评论信息",
  "files": "预留作为图片评论信息"
}
```
Lat表示纬度
Lng表示经度
comment表示发表评论内容
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": {}
}
```

## 7.上传图片
upload_img 与景点无关的公用接口
### request:
```
{
  "UID": 1223457,
  "files": "预留作为图片评论信息"
}
```
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": [
    "图片地址1",
    "图片地址2",
    "...."
  ]
}
```

## 8.捐赠
donate
### request:
```json
{
  "UID": 1223457,
  "type": 1
}
```
type表示捐赠类型

### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": {}
}
```

## 9.关注/取关
follow
### request:
```json
{
  "UID": 1223457,
  "UID_T": 132423,
  "type": 1
}
```
UID_T表示关注目标

type：1关注，0表示取关

### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": {}
}
```

## 10.获取关系列表
get_relations 暂定
### request:
```json
{
  "UID": 1223457,
  "type": 1
}
```
type：0表示我关注的，1表示粉丝

### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": [
      {
        "UID": 1223457,
        "nickname": "youngou",
        "profile_photo_url": "https://path/profile",
        "sex": 1,
        "isfriend": 1,
      }
  ]
}
```

## 11.排行榜
get_ranks
### request:
```json
{
  "UID": 1223457,
  "type": 1
}
```
type表示预留的排行榜类型
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": [
      {
        "UID": 1223457,
        "nickname": "youngou",
        "profile_photo_url": "https://path/profile",
        "sex": 1,
        "points": 1212,
        "donate_points": 1212,
      }
  ]
}
```

## 12.扫一扫
get_spot_name
### request:
```json
{
  "UID": 1223457,
  "pic": "..."
}
```
pic表示图片原文件，二进制
### response：
```json
{
  "code": 0,
  "message": "成功",
  "data": 
      {
        "返回景点信息"
      }
}
```
若失败：
```json
{
  "code": 1,
  "message": "请换一张图片",
  "data": {}
}
```