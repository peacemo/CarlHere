---
layout: post
title: "修复Git工具连接问题"
date:   2021-03-10
tags: [Git]
comments: false
toc: false
author: shikamarushiy
---
使用git工具进行clone / pull / push等操作的时候，由于国内的网络环境问题，会导致操作中断等问题。可以通过修改 git 的 proxy 代理解决。

前提是保证主机打开了 SSR 

在终端中输入以下命令，更改 git 在连接 GitHub 的时候走 SSR 的代理

```bash
$ git config --global https.https://github.com.proxy socks5://127.0.0.1:1080
$ git config --global http.http://github.com.proxy socks5://127.0.0.1:1080
```

修改后连接速度达到 SSR 代理水准，一级棒！👍