## 简介

pre-dem-C 是由[七牛云](https://www.qiniu.com)发起和维护的针对嵌入式系统及纯C客户端，实现的开源 SDK，具有轻量级，易于移植等优点

## 功能清单

| 功能 | 版本 |
| - | - |
| 自定义事件上报 | v1.0.0 |

## 快速开始

- 准备工作

参照 [Docs](https://pre-dem.github.io/docs) 注册 predem 的 app，生成上报 `domain` 及 `appKey`


- 集成 sdk 代码

将工程下载到本地，找到 `source` 和 `include` 目录，将其中的文件copy 到自己的目录下，另外把libcurl 也加入到自己项目中

- 引入头文件

在自己的项目中引入SDK的头文件
```
#include "predem_curl.h"
```

- 配置域名和appkey

在app 配置页面找到domain和appKey
```
predem_curl_init("{domain}", "{appKey}");
```
初始化已经完成

- 设置设备信息

把设备相关信息通过这个函数设置进去，具体参数见头文件描述
```
predem_curl_set_env("{}", "0.0.1", "arm", "qiniu", "abcdef", "macos", "999", NULL, "{user_id}");
```

- 上报自定义事件

C SDK 只支持自定义事件上报功能。C SDK 是基于 libcurl 实现的，所以在使用之前别忘了初始化libcurl, 程序中前面只要有一处做了初始化就成，这个函数是全局的，注意多线程冲突。
```
curl_global_init(CURL_GLOBAL_ALL);
```

上报事件, 可以参考头文件的注释以及单元测试中的代码，C SDK 默认集成了cJSON。
```
    cJSON* obj = cJSON_CreateObject();
    cJSON_AddStringToObject(obj, "clloud", "qiniu");

    const char* c = cJSON_PrintUnformatted(obj);

    int res = predem_curl_send_event("testsend", c);
    if (res != PREDEM_CURL_OK) {
        printf("code %d\n", res);
        FAIL("send event failed");
    }

    cJSON_free((void*)c);
    cJSON_Delete(obj);
```