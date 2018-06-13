## API 规范

### 基本格式

> http(s)://domain/v2/:app_id/functional-path?query

Authorization：SHA2(url:sk)
**注意这里的 url 不含 scheme**

如果忽略鉴权，这里可以为空

客户在使用时，需要配置两个参数，app_key 以及域名

app_key 分为两部分，app_id 和 sk, App_id 为 app_key 的前8位

服务端根据 app_id 找寻对应的app

### 基本功能

#### 日志上报

> http(s)://domain/v2/:app_id/log-capture

#### 崩溃上报

> http(s)://domain/v2/:app_id/crashes

#### Http监控

> http(s)://domain/v2/:app_id/http-monitors

#### 网络诊断

> http(s)://domain/v2/:app_id/net-diags

#### Web性能

> http(s)://domain/v2/:app_id/web-performances

#### 服务配置

> http(s)://domain/v2/:app_id/app-config

#### 自定义上报

> http(s)://domain/v2/:app_id/custom-events
> http(s)://domain/v2/:app_id/custom-events.png


---

## 基础事件

所有的数据都是按照基础事件的格式进行上报, http 请求中的User Agent 要使用系统的UA

```
type BaseEvent struct {
    // Event base
    Time       int64 `json:"time"` 客户端时间
    // App base
    AppId       string `json:"app_id"`
    AppBundleId string `json:"app_bundle_id"`
    AppName     string `json:"app_name"`
    AppVersion  string `json:"app_version"`

    // server base， 如果有referer 就不需要写
    Domain  string `json:"domain"`
    Path    string `json:"path"`

    // Device base，如果有完整UA 就不需要填写
    DeviceModel  string `json:"device_model"`
    Manufacturer string `json:"manufacturer"`
    DeviceId     string `json:"device_id"`

    // OS base ，如果有完整UA 就不需要填写
    OsPlatform string `json:"os_platform"`
    OsVersion  string `json:"os_version"`
    OsBuild    string `json:"os_build"`

    // SDK base
    SdkVersion string `json:"sdk_version"`
    SdkId      string `json:"sdk_id"`

    // Custom
    Tag string `json:"tag"`

    Type    string `json:"type"`
    Name    string `json:"name"`
    Content string `json:"content"` // 如果有数据不在上述列表中，需要json 序列化嵌入到此字段中
}
```

每个事件是一个JSON，每个JSON 使用换行符隔开。

## 日志上报

### 上报监控数据

请求包:

```
POST v2/:app_id/log-capture
Content-Type: application/x-gzip
Content-Encoding: gzip
Authorization：SHA2(url:sk)
Body: ${Content}
```

返回包:

- 如果请求成功，返回包含如下内容的 JSON 字符串（已格式化，便于阅读）：

```
201
{}
```

- 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：

```
{
    "error_message":    ${ErrorMessage} // string
    "error_code":       ${ErrorCode} // string
}
```

* `<Content>`: 上报信息正文，base event格式，content中为真实数据信息，需要序列化，格式：

```
{"time":时间戳UTC时间,"type":"auto_captured","name":"log","app_version":"版本号","sdk_version":"SDK版本号","sdk_id":"sdk唯一id","tag":"指定标示，比如uid等","content":"序列化内容"}\n
{"time":时间戳UTC时间,"type":"auto_captured","name":"log","app_version":"版本号","sdk_version":"SDK版本号","sdk_id":"sdk唯一id","tag":"指定标示，比如uid等","content":"序列化内容"}\n
```

content中数据格式
```
{
"log_key":"日志内容",
"start_time":"日志开始时间",
"end_time": "日志结束时间",
"log_tags":"日志标签",
"error_count":"错误日志数目",
}
```

数据使用 gzip 进行压缩

## 崩溃上报

### 上报监控数据

请求包:

```
POST v2/:app_id/crashes
Content-Type: application/x-gzip
Content-Encoding: gzip
Authorization：SHA2(url:sk)
Body: ${Content}
```

返回包:

- 如果请求成功，返回包含如下内容的 JSON 字符串（已格式化，便于阅读）：

```
201
{}
```

- 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：

```
{
    "error_message":    ${ErrorMessage} // string
    "error_code":       ${ErrorCode} // string
}
```

* `<Content>`: 上报信息正文，base event格式，content中为真实数据信息，需要序列化，格式：

```
{"time":时间戳UTC时间,"type":"auto_captured","name":"crash","app_version":"版本号","sdk_version":"SDK版本号","sdk_id":"sdk唯一id","tag":"指定标示，比如uid等","content":"序列化内容"}\n
{"time":时间戳UTC时间,"type":"auto_captured","name":"crash","app_version":"版本号","sdk_version":"SDK版本号","sdk_id":"sdk唯一id","tag":"指定标示，比如uid等","content":"序列化内容"}\n
```

content中数据格式
```
{
"report_uuid":"crash 报告的唯一识别号",
"crash_log_key":"crash 内容",
"start_time":"系统启动的时间",
"crash_time":"崩溃发生的时间",

}
```

数据使用 gzip 进行压缩

## HTTP Monitor API Spec

### 上报监控数据

请求包:

```
POST v2/:app_id/http-monitors
Content-Type: application/x-gzip
Content-Encoding: gzip
Authorization：SHA2(url:sk)
Body: ${Content}
```

返回包:

- 如果请求成功，返回包含如下内容的 JSON 字符串（已格式化，便于阅读）：

```
201
{}
```

- 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：

```
{
    "error_message":    ${ErrorMessage} // string
    "error_code":   ${ErrorCode} // string
}
```

* `<Content>`: 上报信息正文，base event格式，content中为真实数据信息，需要序列化，格式：

```
{"time":时间戳UTC时间,"type":"auto_captured","name":"monitor","app_version":"版本号","sdk_version":"SDK版本号","sdk_id":"sdk唯一id","tag":"指定标示，比如uid等","content":"序列化内容"}\n
{"time":时间戳UTC时间,"type":"auto_captured","name":"monitor","app_version":"版本号","sdk_version":"SDK版本号","sdk_id":"sdk唯一id","tag":"指定标示，比如uid等","content":"序列化内容"}\n
```

content中数据格式
```
{
"host_ip":"服务端IP",
"network_error_msg":"错误信息",
"status_code": httpcode,
"method":"http method",
"start_timestamp":请求开始时间戳毫秒,
"data_length":请求长度,
"query:{json 形式的请求参数},
"response_time_stamp":响应时间戳毫秒,
"url":"完整url",
"path": "请求path",
"dns_time": dns解析时间,
"end_timestamp": 结束时间戳毫秒,
"path1": "path 第一级",
"path2": "path 第二级",
"path3": "path 第三级",
"path4": "path 第四级",
"domain": "域名",
"network_error_code: 网络错误代码,
}
```

数据使用 gzip 进行压缩

* 上报数据结构

### 行为约定

#### log 目录结构及索引文件

```
// 一个完整的 log 目录信息结构如下
.
|- index.json // 滚动 log 文件对应的索引文件
|- log.1      // 每个 log 以文件大小作为上线
|- log.2
|- log.3
|- ...
```

```
// index 文件需要包含的基本信息
{
  read_file_index: 1,       // 最近一次被读取的文件
  read_file_position: 324,  // 最近一次读取到的位置
  write_file_index: 2,      // 最近一次写入的文件
  write_file_position: 111  // 最近一次写入到的位置
}
```

#### log 行为约定

- 使用滚动 log 文件方式切换记录
  - 单个 log 文件以文件大小作为上限
  - 当一个文件写满后，创建新的文件
  - 当启动 App 时发现有 log 文件还未上报，创建新的文件写入 log 而不在原有文件做追加
- log 记录要保证读写原子性
  - I/O 操作要有独立线程
  - 可考虑维持一个读写的执行队列
- log 文件清除
  - 当 read_file_index 递增触发时，将小于等于该 index 的所有 log 文件清除
  
  **log 文件数量不超过 100 个，当累积文件数达到 100 时新写入文件从最老的 log 文件开始替换过期 log**
  
  **单个 log 文件大小不超过 64 KB**
  

#### 轮询上报

- 计时器
  - App 启动后，开启轮询计时器
  - 进入后台时停止计时器
  - 从后台回到前台时，恢复计时器
- 上报过程出现异常
  - 上报失败时，要将 index.json 的 read 信息恢复为上报读取前的数值
  - 上报失败时，不对 log 文件做清除操作

---

## Web性能

> http(s)://domain/v2/:app_id/web-performances
现代浏览器有Web性能API函数可以获取到网页真实的性能数据，通过上报汇聚可以得到
请求包按照基本事件形式，content 中 即 performance API  获取的 每一个entry，将content 序列化后填入基本事件中进行上报

---

## 配置处理 API Spec

### 上报地址
通过config 接口获取当前App配置，同时使用json 进行发送设备信息给服务器进行记录
`http(s)://domain/v2/:app_id/app-config`

### 上报间隔
config 一天内获取一次，启动时获取一次，异步获取更新，上次更新时间距离现在的时间不能超过24小时

### 上报内容

基础事件

---

## 自定义事件 API Spec

### 上报自定义事件
基础事件，然后将自定义的事件json 序列化为字符串之后放入到content 中

另外为了方便客户端使用，也增加了GET方式的上报，内容是相同的
```
GET v2/:app_id/custom-events.png
```

返回包：

- 若上传自定义事件成功，返回：

```
200
```

- 若上传自定义事件失败，返回：

```
4xx
{
	"error_message": ${msg},
	"error_code": ${code}
}
```

---



