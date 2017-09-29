## 简介

pre-dem-web 是一款 JavaScript 端无埋点统计工具。几乎可以在 0 配置的情况下前端报错监控、Ajax 请求的记录等功能。


## 功能

| 功能 | 版本 |
| - | - |
| 窗口性能分析 | v1.0.0 |
| AJAX 分析 | v1.0.0 |
| 错误拦截 | v1.0.0 |

## 快速集成
首先请根据[创建第一个 App](quickstart/init)的步骤获取 App 的 `App Key`

将 pre-dem-web.js 文件引入到你需要的 html 页面中， 然后初始化 SDK.

![](_media/websdk-init.png)

具体调用逻辑为：

- 引入 js 文件
```
    <script type="text/javascript" src="${WebSdkUrl}"></script>
```

- 初始化
```
    <script type="text/javascript">
        predem.init({appId: ${AppId}, domain: ${Domain}});
    </script>
```
- 设置上报 Tag (注释：用来设置唯一标识，根据自己的业务逻辑选择调用，允许不设置)
```
    <script type="text/javascript">
        predem.setTag(${Tag});
    </script>
```

## 字段释义
### 窗口性能

| 字段 | 解释 |
| - | - |
| Domain | 域名 |
| Path | 路径 |
| Ua | 声明了浏览器用于 HTTP 请求的用户代理头的值 |
| Platform | 平台 |
| NavigationStart | 浏览器处理当前网页的启动时间 |
| UnloadEventStart | 在浏览器开始卸载前一页时候，捕获所需的数据（如果相同域中有前一页需要卸载的话） |
| UnloadEventEnd | 在浏览器已完成前一页的卸载的时候，捕获所需的数据（如果相同域中有前一页需要卸载的话） |
| RedirectStart | 在浏览器开始 HTTP 重定向时捕获所需的数据。 |
| RedirectEnd | 在浏览器完成任何 HTTP 重定向时捕获所需的数据。 |
| FetchStart | 浏览器发起 HTTP 请求读取文档的毫秒时间戳 |
| DomainLookupStart | 域名查询开始时的时间戳 |
| DomainLookupEnd | 域名查询结束时的时间戳 |
| ConnectStart | HTTP 请求开始向服务器发送的时间戳 |
| ConnectEnd | 浏览器与服务器连接建立（握手和认证过程结束）的毫秒时间戳 |
| SecureConnectionStart | HTTPS 连接开始的时间，如果不是安全连接，则值为 0 |
| RequestStart | 浏览器向服务器发出 HTTP 请求时的时间戳。或者开始读取本地缓存时间 |
| ResponseStart | 浏览器从服务器（或读取本地缓存）收到第一个字节时的时间戳 |
| ResponseEnd | 浏览器从服务器收到最后一个字节时的毫秒时间戳 |
| DomLoading | 浏览器开始解析网页 DOM 结构的时间 |
| DomInteractive | 网页 DOM 树创建完成，开始加载内嵌资源的时间 |
| DomContentLoadedEventStart | 网页 DOMContentLoaded 事件发生时的时间戳 |
| DomContentLoadedEventEnd | 网页所有需要执行的脚本执行完成时的时间，DomReady 的时间 |
| DomComplete | 网页 DOM 结构生成时的时间戳 |
| LoadEventStart | 当前网页 Load 事件的回调函数开始执行的时间戳 |
| LoadEventEnd | 当前网页 Load 事件的回调函数结束运行时的时间戳 |
| NavigationType | 是一个数值常量，表示刚刚发生的导航类型, 0: 页面第一次加载; 1:页面重载过; 2:页面是通过“后退”或“前进”按钮打开的|
| RedirectCount | 表示页面加载前的重定向次数 |
| TriggerTime | 触发时间 |
| ClientIP | 客户端 IP |
| Country | 国家 |
| Province | 省份 |
| City | 城市 |
| Isp | 运营商 |

### Ajax

| 字段 | 解释 |
| - | - |
| AppBundleId | 宿主 App 的唯一标识号(包名) |
| AppName | 宿主 App 的名字 |
| AppVersion | App 版本号 |
| DeviceModel | 设备型号 |
| OsPlatform | 平台 |
| OsVersion | 系统版本号 |
| SdkVersion | sdk 版本号 |
| SdkId | sdk 唯一标识 |
| Tag | 用户标识 |
| Manufacturer | 厂商 |
| Domain | 域名 |
| Method | POST/GET/PUT/PATCH/DELETE |
| HostIP | 主机IP |
| StatusCode | 状态码 |
| StartTimestamp | 发送请求时间 |
| ResponseTimeStamp | response 时间 |
| EndTimestamp | 请求结束时间 |
| DnsTime | dns 解析时间 |
| DataLength | 数据长度 |
| NetworkErrorCode | 错误码 |
| NetworkErrorMsg | 错误信息 |
| ClientIP | 客户端 IP |
| Country | 国家 |
| Province | 省份 |
| City | 城市 |
| Isp | 运营商 |


### 错误信息

| 字段 | 解释 |
| - | - |
| AppBundleId | 宿主 App 的唯一标识号(包名) |
| AppName | 宿主 App 的名字 |
| AppVersion | App 版本号 |
| DeviceModel | 设备型号 |
| OsPlatform | 平台 |
| OsVersion | 系统版本号 |
| SdkVersion | sdk 版本号 |
| SdkId | sdk 唯一标识 |
| DeviceId | 设备唯一标识 |
| Manufacturer | 厂商 |
| StartTime | App 启动时间 |
| CrashTime | 崩溃触发时间 |
| Mode | 类型 |
| Message | 信息 |



