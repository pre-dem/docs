## API 规范

### 基本格式

> http(s)://domain/v1/:app_id/functional-path?query

Authorization：SHA2(url:sk)
**注意这里的 url 不含 scheme**

如果忽略鉴权，这里可以为空

**TODO** 如果需要，可以在query里加入utc 时间

除了自定义的上报，都会带上platform, platform 用字符串表示，i(ios)和a(android)

客户在使用时，需要配置两个参数，app_key 以及域名

app_key 分为两部分，app_id 和 sk

服务端根据 app_id 找寻对应的app

### 基本功能

#### 崩溃上报

> http(s)://domain/v1/:app_id/crashes/:platform

#### Http监控

> http(s)://domain/v1/:app_id/http-stats/:platform

#### 网络诊断

> http(s)://domain/v1/:app_id/net-diags/:platform

#### 服务配置

> http(s)://domain/v1/:app_id/app-config/:platform

#### 自定义上报

> http(s)://domain/v1/:app_id/events

---

## Crash API Spec

### 获取崩溃日志上传 token 接口

请求包：

```
GET /v1/${app_id}/crash-report-token/${platform}?md5=${md5}
```

`${platform}` 目前支持两个平台，其中 `i` 代表 `iOS`, `a` 代表 `android`

例：

```
GET /v1/f127c8d8f9ede0f464e80f5f4b46658/crash-report-token/i?md5=${af47219ebc749eab8127caedba}
```

- 若获取崩溃报告成功，返回：

```
200
{
	"message": "ok",
	"token": ${UploadToken}  // string
	"key": ${FileKey}
}
```

- 若获取失败，返回：

```
400
{
	"error_code": ${ErrorCode},  // long
	"error_message": ${ErrorMessage} // string
}
```

### 上报崩溃日志描述信息

请求包:

```
POST v1/${app_id}/crashes/${platform}
Content-Type: application/json
Body:
{
	AppId        string `json:"app_id"`
	AppBundleId  string `json:"app_bundle_id"`
	AppName      string `json:"app_name"`
	AppVersion   string `json:"app_version"`
	DeviceModel  string `json:"device_model"`
	OsPlatform   string `json:"os_platform"`
	OsVersion    string `json:"os_version"`
	OsBuild      string `json:"os_build"`
	SdkVersion   string `json:"sdk_version"`
	SdkId        string `json:"sdk_id"`
	DeviceId     string `json:"device_id"`
	ReportUUID   string `json:"report_uuid"`
	CrashLogKey  string `json:"crash_log_key"`
	Manufacturer string `json:"manufacturer"`
	StartTime    string `json:"start_time"`
	CrashTime    string `json:"crash_time"`
}
```

`${platform}` 目前支持两个平台，其中 `i` 代表 `iOS`, `a` 代表 `android`

例：

```
POST /v1/f127c8d8f9ede0f464e80f5f4b46658/crashes/i
```

返回包：

- 若上传崩溃描述信息成功，返回：

```
200
{
	"message": "ok"
}
```

- 若上传崩溃描述信息失败，返回：

```
400
{
	"error_code": ${ErrorCode},  // long
	"error_message": ${ErrorMessage} // string
}
```

---

## HTTP Monitor API Spec

### 上报监控数据

请求包:

```
POST v1/${app_id}/http-stats/${platform}
Content-Type: application/x-gzip
Content-Encoding: gzip
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

* `<Content>`: 上报信息正文，格式：

```
${value0}\t${value1}\t${value2}\t${value3}\n
${value0}\t${value1}\t${value2}\t${value3}\n
...
```
当字段值不存在时使用 `-` 填充相应字段进行上报
数据使用 gzip 进行压缩

* 上报数据结构

```
{
	AppId             string // app id
	Platform          int64  // 上报的客户端类型，1: iOS, 2: Android
	AppName           string // 宿主 App 的名字。
	AppBundleId       string // 宿主 App 的唯一标识号(包名)
	OsVersion         string // 系统版本号
	DeviceModel       string // 设备型号
	DeviceUUID        string // 设备唯一识别号
	Domain            string // 请求的 Domain Name
	Path              string // 请求的 Path
	Method            string // 请求使用的 HTTP 方法，如 POST 等
	HostIP            string // 实际发生请求的主机 IP 地址
	StatusCode        int64  // 服务器返回的 HTTP 状态码
	StartTimestamp    uint64 // 请求开始时间戳，单位是 Unix ms
	ResponseTimeStamp uint64 // 服务器返回 Response 的时间戳，单位是 Unix ms
	EndTimestamp      uint64 // 请求结束时间戳，单位是 Unix ms
	DnsTime           uint64 // 请求的 DNS 解析时间, 单位是 ms
	DataLength        uint64 // 请求返回的 data 的总长度，单位是 byte
	NetworkErrorCode  int64  // 请求发生网络错误时的错误码
	NetworkErrorMsg   string // 请求发生网络错误时的错误信息
}
```

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

## NetDiagnose API Spec

### 上报一条网络诊断数据

请求包：

```
POST v1/${app_id}/net-diags/${platform}
Content-Type: application/json
{
	AppId         string  `json:"app_id"`
	AppBundleId   string  `json:"app_bundle_id"`
	AppName       string  `json:"app_name"`
	AppVersion    string  `json:"app_version"`
	DeviceModel   string  `json:"device_model"`
	OsPlatform    string  `json:"os_platform"`
	OsVersion     string  `json:"os_version"`
	SdkVersion    string  `json:"sdk_version"`
	SdkId         string  `json:"sdk_id"`
	DeviceId      string  `json:"device_id"`
	ResultID      string  `json:"result_id"`
	PingCode      int     `json:"ping_code"`
	PingIp        string  `json:"ping_ip"`
	PingSize      uint    `json:"ping_size"`
	PingMaxRtt    float64 `json:"ping_max_rtt"`
	PingMinRtt    float64 `json:"ping_min_rtt"`
	PingAvgRtt    float64 `json:"ping_avg_rtt"`
	PingLoss      int     `json:"ping_loss"`
	PingCount     int     `json:"ping_count"`
	PingTotalTime float64 `json:"ping_total_time"`
	PingStddev    float64 `json:"ping_stddev"`
	TcpCode       int     `json:"tcp_code"`
	TcpIp         string  `json:"tcp_ip"`
	TcpMaxTime    float64 `json:"tcp_max_time"`
	TcpMinTime    float64 `json:"tcp_min_time"`
	TcpAvgTime    float64 `json:"tcp_avg_time"`
	TcpLoss       int     `json:"tcp_loss"`
	TcpCount      int     `json:"tcp_count"`
	TcpTotalTime  float64 `json:"tcp_total_time"`
	TcpStddev     float64 `json:"tcp_stddev"`
	TrCode        int     `json:"tr_code"`
	TrIp          string  `json:"tr_ip"`
	TrContent     string  `json:"tr_content"`
	DnsRecords    string  `json:"dns_records"`
	HttpCode      int     `json:"http_code"`
	HttpIp        string  `json:"http_ip"`
	HttpDuration  float64 `json:"http_duration"`
	HttpBodySize  int     `json:"http_body_size"`
}
```

返回包：

- 如果请求成功，返回包含如下内容的 JSON 字符串（已格式化，便于阅读）：

```
201 
{}
```

- 如果请求失败，返回包含如下内容的JSON字符串（已格式化，便于阅读）：

```
{
    "error_message":    ${Error} // string
    "error_code":       ${ErrorCode}
}
```

---

## 日活统计 API Spec

### 上报地址
通过config 配置进行上传日活数据，使用json 进行上报
`http(s)://domain/v1/:app_id/app-config/:platform`

### 上报间隔
config 一天内获取一次，启动时获取一次，异步获取更新，上次更新时间距离现在的时间不能超过24小时

### 上报内容

```
app_bundle_id     : String (app 包名，比如com.xxx.yyy)
app_name          : String (app 名字 比如王者荣耀)
app_version       : String 
device_model      : String
os_platform       : String
os_version        : String
sdk_version       : String
sdk_id            : String （通过sdk生成的唯一id）
device_id         : String  (设备唯一id，会有权限问题)
```

服务端需要根据客户端IP 分离出

```
country           : String
province          : String
city              : String
isp               : String
```

---

## 自定义事件 API Spec

### 上报一条自定义事件

```
POST v1/${app_id}/events
Content-Type: application/json
Body:
{
    AppBundleId  string `json:"app_bundle_id"`
    AppName      string `json:"app_name"`
    AppVersion   string `json:"app_version"`
    DeviceModel  string `json:"device_model"`
    OsPlatform   string `json:"os_platform"`
    OsVersion    string `json:"os_version"`
    OsBuild      string `json:"os_build"`
    SdkVersion   string `json:"sdk_version"`
    SdkId        string `json:"sdk_id"`
    Tag          string `json:"tag"`
    Manufacturer string `json:"manufacturer"`
    Name         string `json:"name"`
    Content      string `json:"content"`
    Type         string `json:"type"`
}
```

其中，`Content` 是自定义事件映射表序列化为字符串后的结果。

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




