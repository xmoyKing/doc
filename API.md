本文档为API接口文档

----
`host/common_access/method`

请求中都会带UID，表示用户唯一标识（目前暂定QQ号）
响应中有三个字段：
  code表示响应码，0为成功，其他为错误，具体见API
  message表示code对应的响应信息，
  data为返回的数据，分两种类型，数组/对象，若不需要返回数据，则为空对象

## 1.QQ登录
save_user_info
### request：
```
{
  "UID": 1223457
  "nickname": "youngou",
  "profile_photo_url": "https://path/profile",
  "sex": 1
}
```
### response：
```
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
```
{
  "UID": 1223457
}
```
### response：
```
{
  "code": 0,
  "message": "成功",
  "data": [
    {
      "TID":100,
      "name":"腾讯大厦",
      "latitude":22.540503,"longitude":113.934528,"level":3,"state":1,
      "info": {
        "desc": "该景点是...",
        "pics": ["http://pic1url...", "..."],
        "..."
      }
    },
    {"TID":102,"name":"万利达大厦","latitude":22.540730,"longitude":113.933570","level":2,"state":1}
  ]
}
```
state表示是否点亮，0未点亮，1已点亮，
level表示景点级别 0+

## 2.获取附近景点
get_nearby_spot

### request：
```
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
{
  "code": 0,
  "message": "成功",
  "data": [
    "参考获取用户所有点亮景点response"
  ]
}

## 3.获取景点详细信息
get_spot_detail
### request：
```
{
  "UID": 1223457,
  "TID": 411
}
```

### response：
{
  "code": 0,
  "message": "成功",
  "data": 
    {
      "TID":100,
      "name":"腾讯大厦",
      "latitude":22.540503,"longitude":113.934528,"level":3,"state":1,
      "info": {
        "desc": "该景点是...",
        "pics": ["http://pic1url...", "..."],
        "..."
      }
    }
}


## 4.获取景点评论信息
get_spot_comments
### request：
```
{
  "UID": 1223457,
  "TID": 411
}
```
### response：
{
  "code": 0,
  "message": "成功",
  "data": [
      {
        "UID": 1223457
        "nickname": "youngou",
        "profile_photo_url": "https://path/profile",
        "sex": 1,
        "comment": "景点很棒",
        "timestamp": "2017-05-06 18:20",
      }
  ]
}

## 5.点亮景点
light_spot
### request：
```
{
  "UID": 1223457,
  "TID": 5
  "Lat": 456564.111,
  "Lng": 1564.1515,
}
```
Lat表示纬度
Lng表示经度
TID表示景点ID
### response：
{
  "code": 0,
  "message": "成功",
  "data": {
    "points": 10,
  }
}
points表示当前用户点亮景点获取到的积分

## 6.发表评论
remark_spot
### request：
```
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
{
  "code": 0,
  "message": "成功",
  "data": {}
}

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
{
  "code": 0,
  "message": "成功",
  "data": [
    "图片地址1",
    "图片地址2",
    "...."
  ]
}

## 8.捐赠
donate
### request:
```
{
  "UID": 1223457,
  "type": 1
}
```
type表示捐赠类型

### response：
{
  "code": 0,
  "message": "成功",
  "data": {}
}

## 9.关注/取关
follow
### request:
```
{
  "UID": 1223457,
  "UID_T": 132423,
  "type": 1
}
```
UID_T表示关注目标
type：1关注，0表示取关

### response：
{
  "code": 0,
  "message": "成功",
  "data": {}
}

## 10.获取关系列表
get_relations 暂定
### request:
```
{
  "UID": 1223457,
  "type": 1
}
```
type：0表示我关注的，1表示粉丝

### response：
{
  "code": 0,
  "message": "成功",
  "data": [
      {
        "UID": 1223457
        "nickname": "youngou",
        "profile_photo_url": "https://path/profile",
        "sex": 1,
        "isfriend": 1,
      }
  ]
}


## 11.排行榜
get_ranks
### request:
```
{
  "UID": 1223457,
  "type": 1
}
```
type表示预留的排行榜类型
### response：
{
  "code": 0,
  "message": "成功",
  "data": [
      {
        "UID": 1223457
        "nickname": "youngou",
        "profile_photo_url": "https://path/profile",
        "sex": 1,
        "points": 1212,
        "donate_points": 1212,
      }
  ]
}

## 12.扫一扫
get_spot_name
### request:
```
{
  "UID": 1223457,
  "pic": "..."
}
```
pic表示图片原文件，二进制
### response：
```
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
```
{
  "code": 1,
  "message": "请换一张图片",
  "data": {}
}
```