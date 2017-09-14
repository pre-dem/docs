## 简介

pre-dem-web-sdk 是一款 JavaScript 端无埋点统计工具。几乎可以在 0 配置的情况下前端报错监控、Ajax 请求的记录等功能。


## 功能

| 功能 | 版本 |
| - | - |
| 窗口性能分析 | v1.0.0 |
| AJAX 分析 | v1.0.0 |
| 错误拦截 | v1.0.0 |

## 快速集成
首先请根据[创建第一个 App](quickstart/init)的步骤获取 App 的 `App Key`

将 predem-web-sdk.js 文件引入到你需要的 html 页面中， 然后初始化 SDK.

![](_media/websdk-init.png)

具体调用逻辑为：

- 引入 js 文件
``` objc
    <script type="text/javascript" src="predem-web-sdk.js"></script>
```

- 初始化
``` objc
    <script type="text/javascript">
        window.InitWebSdk({tag: TAG, token: App Key});
    </script>
```
## 字段释义

### 窗口性能

| 字段 | 解释 |
| - | - |
| Domain | 域名 |
| Path | 路径 |
| Ua |  |
| Platform | 平台 |
| NavigationStart | 浏览器处理当前网页的启动时间 |
| UnloadEventStart | 平台 |
| UnloadEventEnd | 平台 |
| RedirectStart | 平台 |
| RedirectEnd | 平台 |
| FetchStart | 浏览器发起http请求读取文档的毫秒时间戳 |
| DomainLookupStart | 域名查询开始时的时间戳 |
| DomainLookupEnd | 域名查询结束时的时间戳 |
| ConnectStart | http请求开始向服务器发送的时间戳 |
| ConnectEnd | 平台 |
| SecureConnectionStart | 平台 |
| RequestStart | 平台 |
| ResponseStart | 平台 |
| ResponseEnd | 平台 |
| DomLoading | 平台 |
| DomInteractive | 平台 |
| Platform | 平台 |
| Platform | 平台 |
| Platform | 平台 |
| Platform | 平台 |
| Platform | 平台 |

### Ajax

| 字段 | 解释 |
| - | - |

### 错误信息

| 字段 | 解释 |
| - | - |



