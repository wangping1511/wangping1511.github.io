---
layout: article
title: HTTP Client 工具
tags: ["HTTPie", "HTTP Client", "cURL"]
---

## cURL

### 安装

#### macOS

自带

### 使用

#### 查看网站响应的数据

```bash
$ curl www.baidu.com
```

#### `-V` 显示请求头和通信过程

```bash
$ curl -v www.baidu.com
```

#### `--trace [filename]` 显示更详细的通信过程

```bash
$ curl --trace trace_info.txt www.baidu.com
or
$ curl --trace-ascii trace_info.txt www.baidu.com
```

#### `-i` 显示响应头信息

```bash
$ curl -i www.baidu.com
```

#### ` -o [filename] `  把响应的数据下载下来

```bash
$ curl -o baidu.html www.baidu.com
```

#### `-X [options]` 请求方式

```bash
$ curl -X POST www.baidu.com
or
$ curl -X DELETE www.baidu.com
```

#### `-H` 传递请求 head 参数

传 Token

```bash
$ curl -H 'token=*******' www.baidu.com
```

传参数类型

```bash
$ curl -H 'Content-Type: application/json;charset=UTF-8' www.baidu.com
```

#### `--data-raw` or `--data` 传递参数

使用这种方法请求方式会默认变成 `POST`

```bash
$ curl www.baidu.com --data '{ "pageNum": 1, "pageSize": 10}'
```



## HTTPie

### 安装

#### macOS

```bash
$ brew install httpie
```



### 使用

#### 查看网站响应的数据

```bash
$ https httpie.io/hello
```

#### 自定义请求方式、请求头、JSON 数据

```bash
$ http PUT pie.dev/put X-API-Token:123 name=John
```

#### From 请求

```bash
$ http -f POST pie.dev/post hello=World
```

#### 下载文件

```bash
$ http --download pie.dev/image/png
```

#### 上传文件

```bash
$ http pie.dev/post < files/data.json
```





