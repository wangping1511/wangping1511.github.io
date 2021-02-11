```
layout: article
aside:
  toc: true
title: ElasticSearch(二) 可视化 Kibana
tags: ["ElasticSearch", "Kibana"]
```

## 安装

### 下载

安装 Kibana 需要 java8 或以上版本保存和 elasticsearch 版本一致，下载地址： 中国镜像地址 [华为云](https://mirrors.huaweicloud.com/kibana/)

### 配置

配置文件在 `../config/kibana.yml` 下，配置如下

```yaml
# 服务端口
server.port: 5601
# elastcsearch 的服务地址
elasticsearch.hosts: ["http://localhost:9200"]
# 设置中文语言
i18n.locale: "zh-CN"
```

### 启动

```
./kibana/bin/kibana
```

或配置环境变量  `~/.bash_profile` ，直接在终端输入  `kibana`  启动

```
export PATH="/Users/wangping/software/kibana-7.10.1-darwin-x86_64/bin:$PATH"
```

## 使用
