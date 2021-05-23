---
layout: post
title: "VSCode 中为py文件添加头部基本信息"
date:   2021-05-12
tags: [python]
comments: false
toc: false
author: shikamaru
excerpt_separator: ""
---

* 打开`File -> Preferences -> User Snippets`

<img src="../images/2021-05-12-VSCode_py_header/image-20210512164811612.png" alt="image-20210512164811612" style="zoom:50%;" />

* 添加python.json

<img src="../images/2021-05-12-VSCode_py_header/image-20210512164856442.png" alt="image-20210512164856442" style="zoom:50%;" />

* 文件内容

```json
{
    "HEADER":{
        "prefix": "header",
        "body": [
        "# -*- encoding: utf-8 -*-",
        "'''",
        "@File    :   $TM_FILENAME",
        "@Time    :   $CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
        "@Author  :   carlchen ",
        "@Contact :   carlchenxq@gmail.com",
        // "@License :   (C)Copyright 2017-2018, Liugroup-NLPR-CASIA",
        // "@Desc    :   None",
        
        "'''",
        "",
        "# here put the import lib",
        "$0"
    ],
    }
    
}
```

* 使用时在代码开始键入`header`就行了

<img src="../images/2021-05-12-VSCode_py_header/image-20210512165040773.png" alt="image-20210512165040773" style="zoom:50%;" />