## 简介

pre-dem-objc 是由[七牛云](https://www.qiniu.com)发起和维护的针对 Objective-C 的集用户体验监控及报障于一体的开源 SDK，具有无埋点集成，轻量级，高性能等优点

## 功能清单

| 功能 | 版本 |
| - | - |
| crash 监控 | v1.0.0 |
| HTTP 性能监控 | v1.0.0 |
| UI 卡顿监控 | v1.0.0 |
| 网络诊断 | v1.0.0 |
| 自定义事件上报 | v1.0.0 |

## 安装

使用 [CocoaPods](https://cocoapods.org) 进行安装

```ruby
pod "PreDemObjc"
```

## 快速集成

首先请根据[创建第一个 App](quickstart/init)的步骤获取 App 的 `App Key`

然后，只需如下两行代码，您便可将 pre-dem-objc sdk 集成到您的 App 中：

![](_media/ios-sdk-integration.png)

具体调用逻辑为：

- 初始化

``` objc
    NSError *error;
    [PREDManager startWithAppKey:@"YOUR_APP_KEY"
                   serviceDomain:@"YOUR_SERVICE_DOMAIN"
                           error:&error];
```

初始化之后，SDK 便会自动采集包括 crash、HTTP 请求等监控数据并上报到您指定的服务器

- 网络诊断

``` objc
    [PREDManager  diagnose:@"YOUR_SERVER" 
                  complete:^(PREDNetDiagResult * _Nonnull result) {
        // you can retrieve the diagnostic result here
    }];
```

网络诊断功能会使用包括 ping, traceroute 等一系列网络工具对您指定的服务器进行网络诊断并将诊断结果上传服务器。

- 自定义事件

``` objc
    [PREDManager trackEventWithName:@"YOUR_EVENT_NAME" 
                              event:@{@"EVENT_KEY": EVENT_VALUE, @"EVENT_KEY": EVENT_VALUE}];
```

自定义事件上报功能能够将您自定义的事件直接上报至服务器。
