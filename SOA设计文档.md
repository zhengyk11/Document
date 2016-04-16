# DQZ音乐推荐系统设计文档

计34 邓志杰 桥本优 郑玉昆

[TOC]

## 一、综述

### 1.1 项目背景

随着多媒体和网络技术的发展，我们已经习惯了在电脑和手机上搜索和聆听美妙的音乐，与此同时我们可能在骑行、阅读或者运动，所以我们突发奇想，为了能够不影响当前活动的情况下听到适合的歌曲，我们决定开发一款基于语音的音乐推荐系统，旨在让用户能够解放双手，听想听的歌。

### 1.2 项目目标

设计一款基于语音的音乐推荐系统，通过用户输入的语音向用户推荐最适合的音乐。具体目标如下：

- 通过语音和文字输入解放用户双手- 基于多种现有的服务实现目标功能- 基于多种推荐方式满足客户的需求

### 1.3 文档功能

本文档适用的对象为所有用户、后期开发人员以及所有对本项目感兴趣的开发者。从此文档中，你可以了解到音乐推荐网站的功能、行为以及技术细节，包括网站的实现方法和各个模块的划分与接口设计，为后续的修改开发提供便利。本文档主要分为以下几部分：系统结构设计，接口设计和数据库设计。

## 二、系统行为本项目的设计与实现分为以下几个部分。### 2.1 用户系统- 用户可以进行使用已有账号登录- 用户可以进行使用邮箱进行注册- 用户可以进行通过第三方账户免注册登录- 登录后，用户可以修改个人资料- 用户可以查看个人信息和听歌历史### 2.2 音乐播放系统- 根据推荐系统返回结果播放歌曲- 用户可以暂停歌曲- 用户可以选择下一首- 用户可以调节音量- 用户可以查看歌词### 2.3 语音输入系统

- 根据用户语音转换为文字
### 2.4 音乐推荐系统

- 根据网易云接口获取歌单内歌曲的收听量和评论量进行推荐
- 根据用户行文数据库的数据进行筛选和推荐


## 三、框架接口

### 1. 用户系统与音乐播放系统

- 输入模块

  ``x-webkit-speech``：支持webkit核心的浏览器，用于将语音转换为文字。

- 播放模块

  ``iframe``或``embed``：用于生成网易云音乐的外链播放器。

### 2. 语音输入系统

- 中文分词API

```default
http://apistore.baidu.com/apiworks/servicedetail/143.html
```
  
- 接口地址

```default
http://apis.baidu.com/apistore/pullword/words
```

- 请求参数

参数名	|类型	|必填	|参数位置|	描述| 默认值
-----|-----|-----|-------|---|----------
source| string|是|urlParam|一段中文|清华大学是好学校
param1| string|是|urlParam| 0-1之间的小数表示出词概率或值， 1表示只抽出100%有把握的词;|0
param2| string|是|urlParam	|0-1之间的小数表示出词概率或值;|1

- JSON返回示例

```
清华:0.604942  //清华为分词后结果，0.604942为分词的把握概率值
清华大学:1 
华大:0.068537 
大学:0.949906 
好学:0.659566 
学校:0.936925
```

其中，param1表示选词概率，param1=0.8表示只出概率在0.8以上的词；param2表示是否在分词结果中呈现概率，1表示显示，0表示不显示。


### 3. 音乐推荐系统

通过网易云音乐api获取类似歌单和歌曲信息。以获取歌曲信息为例：

```default
Full request URI: http://music.163.com/api/song/detail/?id=28377211&ids=%5B28377211%5D
```

- 必要参数：

  - id：歌曲ID

  - ids：[id]

- JSON返回示例
  
  ```json
  {"songs":[{"starred":false,"popularity":100.0,"starredNum":0,"playedNum":0,"dayPlays":0,"hearTime":0,"mp3Url":"http://m2.music.126.net/t24d6GoZRkqxzFAovlZEQw==/6027522743661760.mp3","rtUrls":[],"artists":[{"img1v1Id":0,"picId":0,"briefDesc":"","picUrl":"http://p3.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","albumSize":0,"img1v1Url":"http://p4.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","trans":"","musicSize":0,"alias":[],"name":"Quiana","id":194643},{"img1v1Id":0,"picId":0,"briefDesc":"","picUrl":"http://p4.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","albumSize":0,"img1v1Url":"http://p4.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","trans":"","musicSize":0,"alias":[],"name":"Jerry Barnes","id":0}],"copyrightId":0,"score":100,"hMusic":null,"mMusic":{"bitrate":160000,"dfsId":6037418348311743,"playTime":213864,"sr":44100,"volumeDelta":-2.65076E-4,"name":"Moon Without The Stars","id":46689711,"size":4300608,"extension":"mp3"},"lMusic":{"bitrate":96000,"dfsId":6027522743661760,"playTime":213864,"sr":44100,"volumeDelta":-2.65076E-4,"name":"Moon Without The Stars","id":46689712,"size":2590737,"extension":"mp3"},"album":{"songs":[],"artists":[{"img1v1Id":0,"picId":0,"briefDesc":"","picUrl":"http://p4.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","albumSize":0,"img1v1Url":"http://p3.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","trans":"","musicSize":0,"alias":[],"name":"群星","id":122455}],"copyrightId":0,"picId":6046214441318535,"briefDesc":"","artist":{"img1v1Id":0,"picId":0,"briefDesc":"","picUrl":"http://p4.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","albumSize":0,"img1v1Url":"http://p3.music.126.net/6y-UleORITEDbvrOLV0Q8A==/5639395138885805.jpg","trans":"","musicSize":0,"alias":[],"name":"","id":0},"picUrl":"http://p3.music.126.net/LH7PtVBMt02PbphpyAiNmw==/6046214441318535.jpg","commentThreadId":"R_AL_3_2774288","publishTime":1384272000007,"company":"雷亚游戏","blurPicUrl":"http://p3.music.126.net/LH7PtVBMt02PbphpyAiNmw==/6046214441318535.jpg","status":3,"description":"","tags":"","alias":[],"companyId":0,"pic":6046214441318535,"name":"古树旋律 Deemo","id":2774288,"type":"专辑","size":111},"commentThreadId":"R_SO_4_28377211","fee":0,"mvid":0,"ftype":0,"rtype":0,"rurl":null,"copyFrom":"","bMusic":{"bitrate":96000,"dfsId":6027522743661760,"playTime":213864,"sr":44100,"volumeDelta":-2.65076E-4,"name":"Moon Without The Stars","id":46689712,"size":2590737,"extension":"mp3"},"ringtone":"","disc":"2","no":15,"audition":{"bitrate":64000,"dfsId":5871392092502085,"playTime":213864,"sr":44100,"volumeDelta":0.0,"name":"Moon Without The Stars","id":46649321,"size":1731610,"extension":"m4a"},"crbt":null,"rtUrl":null,"position":14,"duration":213864,"status":0,"alias":[],"name":"Moon Without The Stars","id":28377211}],"equalizers":{},"code":200}
  ``` 

### 4. 结果返回

- Json数据返回，前端接口回调
- 文字输入（浏览器将语音转为文字），歌单返回- 使用微信朋友圈动态作为输入- 使用微博动态作为输入
- 扩展：提供基于外部服务的语音输入接口，推广到多种浏览器

## 四、模块设计

### 1. 前端模块- 页面设计：bootstrap- 页面逻辑：JavaScript### 2. 后端模块- 架构：python- 用户系统：oauth认证- 数据预处理：api调用- 推荐系统：调用网易云音乐接口的推荐，基于数据库的推荐- 数据库管理：MySQL或者sqlite3### 3. 前后端交互  
  计划使用ajax实现前后端交互。

