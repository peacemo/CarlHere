---
layout: post
title: "Gradle切换国内镜像源"
date:   2021-03-03
tags: [Android]
comments: false
toc: false
author: shikamaru
excerpt_separator: ""
---
# Gradle仓库切换为国内的阿里源，加快访问速度

## 仅对当前项目生效

在 build.gradle 文件内修改/添加 repositories 配置

```groovy
repositories {
//	google()
//  jcenter()

	//对应google()
    maven {url 'https://maven.aliyun.com/repository/google'}
    //对应jcenter()
    maven {url 'https://maven.aliyun.com/repository/jcenter'}
    //公共库
    maven {url 'https://maven.aliyun.com/repository/public'}
}
```