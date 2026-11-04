【AI 配音怎么做，我是怎么做视频的？保姆级教程之AI配音教学】 https://www.bilibili.com/video/BV1bAxg6GEX8/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862

# 1.原理
[00:30]

## 1.先有配音
[00:43]
稿子-->配音

![[_posts/AI/短视频/影视解说类/实现工具/media/d463734f58812aa34c3c74cd961d0049_MD5.webp]]

## 2.算出每个字的时间
[01:03]
每个字的时间轴

3种方法：

![[_posts/AI/短视频/影视解说类/实现工具/media/cf1deac5bedf36564ee695210b2e5346_MD5.webp]]


## 3.把画面卡在词上
[01:36]



## 4.一帧一帧导出
[02:23]



# 2.怎么选配音方式
[02:47]

用生成式的语音大模型，选平台上自带的音色，使用API

模型测评： https://artificialanalysis.ai/text-to-speech/leaderboard/provider-voice


![[_posts/AI/短视频/影视解说类/实现工具/media/64d0c275c721368c44fb2bb2c4024b62_MD5.webp]]

![[_posts/AI/短视频/影视解说类/实现工具/media/57f11d7e291ede427279c9c0f52bc22a_MD5.webp]]

# 3.上手实践
[05:18]

## 4份文件

### 读法规则.md 
告诉模型稿子怎么改，配音才不读错

![[_posts/AI/短视频/影视解说类/实现工具/media/665d5cea610e7ae0b30353fba6ac4397_MD5.webp]]

### 稿子.txt 
![[_posts/AI/短视频/影视解说类/实现工具/media/900152b28368656ce8720b9540906f08_MD5.webp]]


### 画面要求.md 

![[_posts/AI/短视频/影视解说类/实现工具/media/68949ca7a4c08547040cc85789b38e38_MD5.webp]]

### 配音设置.md 

![[_posts/AI/短视频/影视解说类/实现工具/media/23b3bfd8d5b17721384cbfd671609a61_MD5.webp]]

### key.txt


## 实践步骤


### 3.整篇配音
[07:27]

让它先按读法规则改一遍稿子，再整篇生成配音

### 4.拿到每个字的时间
[08:05]

配音模型，在生成配音时，一起返回的`词级时间戳`

![[_posts/AI/短视频/影视解说类/实现工具/media/c9a497b832eac5aeb974c07df23ba118_MD5.webp]]


### 5.按时间做画面
[09:09]

![[_posts/AI/短视频/影视解说类/实现工具/media/07fa968c412d18d754d05cc375346ce8_MD5.webp]]