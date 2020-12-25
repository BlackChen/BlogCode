---
title: AFNetWorking3.x使用
date: 2016-09-13T17:52:44.000Z
tags: AFNetWorking3.x
---

# 普通使用

## 1.普通GET请求

### 代码

```    
// 创建网络请求管理对象
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
// 申明返回的结果是json类型
    manager.responseSerializer = [AFJSONResponseSerializer serializer];
// 申明请求的数据是json类型
    manager.requestSerializer = [AFJSONRequestSerializer serializer];
// 如果报接受类型不一致请替换一致text/html或别的
    manager.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"text/html", nil];
// 普通GET请求
    [manager GET:<#URLString#> parameters:nil progress:^(NSProgress * _Nonnull downloadProgress) {

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        DLog(@"请求成功：%@", responseObject);
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        DLog(@"请求失败：%@", error);
    }];
```

## 2.带参数GET请求

### 代码

```

// 参数字典
    NSDictionary _dict = [NSDictionary   dictionaryWithObjectsAndKeys:<#(nonnull id), ...#>, nil];
// 创建网络请求管理对象
    AFHTTPSessionManager_ manager = [AFHTTPSessionManager manager];
// 申明返回的结果是json类型
    manager.responseSerializer = [AFJSONResponseSerializer serializer];
// 申明请求的数据是json类型
    manager.requestSerializer = [AFJSONRequestSerializer serializer];
// 如果报接受类型不一致请替换一致text/html或别的   
    manager.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"text/html", nil];
// 带参数GET请求
    [manager GET:<#URLString#> parameters:dict progress:^(NSProgress * _Nonnull downloadProgress) {

      } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        DLog(@"请求成功：%@", responseObject);
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        DLog(@"请求失败：%@", error);
    }];

```
### 3.普通POST请求
#### 代码

```
// 创建网络请求管理对象
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
// 申明返回的结果是json类型
    manager.responseSerializer = [AFJSONResponseSerializer serializer];    
// 申明请求的数据是json类型
    manager.requestSerializer = [AFJSONRequestSerializer serializer];
// 如果报接受类型不一致请替换一致text/html或别的
    manager.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"text/html", nil];
// 普通POST请求
    [manager POST:<#URLString#> parameters:nil progress:^(NSProgress * _Nonnull uploadProgress) {

    } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
        DLog(@"请求成功：%@", responseObject);
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
        DLog(@"请求失败：%@", error);
    }];
```

## 4.带参数POST请求

### 代码

```
// 参数字典的
    NSDictionary _dict = [NSDictionary dictionaryWithObjectsAndKeys:<#(nonnull id), ...#>, nil];
// 创建网络请求管理对象
    AFHTTPSessionManager_ manager = [AFHTTPSessionManager manager];
// 申明返回的结果是json类型
    manager.responseSerializer = [AFJSONResponseSerializer serializer];
// 申明请求的数据是json类型
    manager.requestSerializer = [AFJSONRequestSerializer serializer];
// 如果报接受类型不一致请替换一致text/html或别的
    manager.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"text/html", nil];
// 带参数POST请求
    [manager POST:<#URLString#> parameters:dict progress:^(NSProgress * _Nonnull uploadProgress) {

      } success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
    DLog(@"请求成功：%@", responseObject);
    } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
    DLog(@"请求失败：%@", error);
    }];

```

# 封装使用

![](http://ww2.sinaimg.cn/large/65e4f1e6gw1f7m9bwwe7lj20u01hcwki.jpg)
