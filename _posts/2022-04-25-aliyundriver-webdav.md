---
layout: post
title: "RaiDrive配置aliyundrive-webdav，实现阿里云盘本地访问"
subtitle: "RaiDrive配置aliyundrive-webdav，实现阿里云盘本地访问"
date: 2022-4-25 09:58:47
author: "li.xx"
tags: 
  - 日常
---

## 参考GitHub项目

[messense](https://github.com/messense)/[aliyundrive-webdav](https://github.com/messense/aliyundrive-webdav)

[gylang](https://github.com/gylang)/[aliyundriver-webdav](https://github.com/gylang/aliyundriver-webdav)

#### 安装教程(界面存在一定偏差)

- 直接下载zip
- 下载阿里云盘webdav https://github.com/messense/aliyundrive-webdav/releases, 将aliyundrive-webdav.exe放于 bin目录下
- 运行根目录下 aliyundriver-webdav.exe 即可启动服务

关于开机自启 需要RaiDrive 和 webdav服务开机自启, webdav设置自启可能需要管理员权限

仅校验过 阿里云盘webdav 1.1.0版本

#### 更新记录

- 如果启动失败,可以执行change_mode.exe切换至web模式

**遇到问题可以查看 常见为题, 使用过程中存在的问题和解决也可以反馈记录**

#### 使用说明

# 安装RaiDrive

文件在指南目录下, 你也可以自行下载

配置

**最简单只需要填 refreshToken( 建议改端口 防止被占用)**

```
注意, 把地址Address去掉 不使用https
```

[![image-20220106211654620](https://li-xiaoxu.oss-cn-beijing.aliyuncs.com/blog/image-20220106211654620.png)](https://github.com/gylang/aliyundriver-webdav/blob/master/README.assets/image-20220106211654620.png)

# 获取refreshToken

> **1. 登录阿里云**
>
> https://www.aliyundrive.com/
>
> 1. 浏览器打开**开发者模式(F12)**
>
> [![image-20220106211310171](https://li-xiaoxu.oss-cn-beijing.aliyuncs.com/blog/image-20220106211310171.png)](https://github.com/gylang/aliyundriver-webdav/blob/master/README.assets/image-20220106211310171.png)
>
> 1. **获取refreshToken** [![image-20220106211538483](https://github.com/gylang/aliyundriver-webdav/raw/master/README.assets/image-20220106211538483.png)](https://github.com/gylang/aliyundriver-webdav/blob/master/README.assets/image-20220106211538483.png)

## 配置RaiDrive

```
注意, 把地址Address去掉 不使用https
```

[![image-20220114092345822](https://li-xiaoxu.oss-cn-beijing.aliyuncs.com/blog/image-20220114092345822.png)](https://github.com/gylang/aliyundriver-webdav/blob/master/README.assets/image-20220114092345822.png)

# 启动Raidrive服务

[![image-20220107112639591](https://li-xiaoxu.oss-cn-beijing.aliyuncs.com/blog/image-20220107112639591.png)](https://github.com/gylang/aliyundriver-webdav/blob/master/README.assets/image-20220107112639591.png)

# 查看是否生效

![image-20220107112534270](https://li-xiaoxu.oss-cn-beijing.aliyuncs.com/blog/image-20220107112534270.png)
