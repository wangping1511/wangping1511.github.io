---
layout: article
aside:
  toc: true
title: ElasticSearch（一）
tags: ElasticSearch
---

### 安装

#### 下载

安装 elasticsearch 需要 java8 或以上版本，下载地址： 中国镜像地址 [华为云](https://mirrors.huaweicloud.com/elasticsearch/)

#### 启动

```
./elasticsearch/bin/elasticsearch
```

或配置环境变量  `~/.bash_profile` ，直接在终端输入  `elasticsearch`  启动

```
export PATH="/Users/wangping/software/elasticsearch-7.10.1/bin:$PATH"
```

验证是否启动成功 `http://localhost:9200/`

```json
{
  name: "wangpingdeMacBook-Pro.local",
  cluster_name: "elasticsearch",
  cluster_uuid: "aonT5I1mT8m6B9AJWKMuSQ",
  version: {
    number: "7.10.1",
    build_flavor: "default",
    build_type: "tar",
    build_hash: "1c34507e66d7db1211f66f3513706fdf548736aa",
    build_date: "2020-12-05T01:00:33.671820Z",
    build_snapshot: false,
    lucene_version: "8.7.0",
    minimum_wire_compatibility_version: "6.8.0",
    minimum_index_compatibility_version: "6.0.0-beta1"
  },
  tagline: "You Know, for Search"
}
```

#### 安装中文分词 `IK`

**安装的分词插件要和 elasticsearch 版本保持一致，下载地址：[elasticsearch-analysis-ik](https://github.com/medcl/elasticsearch-analysis-ik)，把下载好的 `IK` 放在 `elasticsearch` 根目录的 `Plugin` 下面**

*重启  `elasticsearch` 打印出日志：*

```
loaded plugin [analysis-ik]
```



### 概念

*ES7.0 没有 type 这个概念了，之前的 type 表示一个表，index 表示一个库*

- **ES 实例**：对应的是关系型数据库的表

- **Index (索引)**：这里的 Index 是名词，一个 Index 对应的是关系型数据库的表
- **Document (文档)**：Elasticsearch 使用 JSON 文档来表示一个对象，就像是关系数据库中一个 Table 中的一行数据
- **Field (字段)**：每个文档包含多个字段，类似关系数据库中一个 Table 的列

和 `MySQL` 比较

| **Elasticsearch** | **MySQL** |
| :---------------- | :-------- |
| ES 实例           | Database  |
| Type              | Table     |
| Document          | Row       |
| Field             | Column    |



### 使用

1. 使用 Elasticsearch 提供的 RESTful API 来执行文档的增删改查等操作。

2. 格式为：http://localhost:9200/index/book/1， **Index** 的名称为 book

3. 用 http 方法操作资源，如使用 GET 获取资源，使用 POST、PUT 新增或更新资源，使用 DELETE 删除资源等

4. 下面使用 [HTTPie](https://httpie.io/) 请求接口。

#### 创建索引

请求：

```
http put :9200/book
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 71
content-type: application/json; charset=UTF-8

{
    "acknowledged": true,
    "index": "book",
    "shards_acknowledged": true
}
```

#### 查看所有索引

请求：

```
http get :9200/_cat/indices\?v\&pretty
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 160
content-type: text/plain; charset=UTF-8

health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   book  7Ox07xT2RR2I8hv8VhWCQA   1   1          2            0      3.6kb          3.6kb
```



#### 查看索引

请求：

```
http get :9200/book
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 231
content-type: application/json; charset=UTF-8

{
    "book": {
        "aliases": {},
        "mappings": {},
        "settings": {
            "index": {
                "creation_date": "1611724115381",
                "number_of_replicas": "1",
                "number_of_shards": "1",
                "provided_name": "book",
                "routing": {
                    "allocation": {
                        "include": {
                            "_tier_preference": "data_content"
                        }
                    }
                },
                "uuid": "INWhxQr8StaUFbYPkTDWkA",
                "version": {
                    "created": "7100199"
                }
            }
        }
    }
}
```

#### 删除索引

请求：

```
http delte :9200/book
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 47
content-type: application/json; charset=UTF-8

{
    "acknowledged": true
}
```

#### 添加文档

book 指索引名称，会将文档添加到此索引下，如果索引不存在会先创建索引；1 为文档唯一标识，可以不写 ES 会自动生成一个唯一的 ID。

请求：

```
http put :9200/book/_doc/1 title="ElasticSearch 入门" key:='["ElasticSearch", "Java"]' year=2021
```

响应：

```
HTTP/1.1 201 Created
Location: /book/_doc/1
content-encoding: gzip
content-length: 139
content-type: application/json; charset=UTF-8

{
    "_id": "1",
    "_index": "book",
    "_primary_term": 1,
    "_seq_no": 1,
    "_shards": {
        "failed": 0,
        "successful": 1,
        "total": 2
    },
    "_type": "_doc",
    "_version": 1,
    "result": "created"
}
```

#### 更新文档

##### 更新整个文档

指定索引和 ID，旧文档会替换新文档，响应的结果 `_version` 为 `"updated"` 、`_version` 增加1 

请求：

```
http put :9200/book/_doc/1 title="ElasticSearch 入门" key:='["ElasticSearch", "Java"]' year=2020
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 141
content-type: application/json; charset=UTF-8

{
    "_id": "1",
    "_index": "book",
    "_primary_term": 1,
    "_seq_no": 7,
    "_shards": {
        "failed": 0,
        "successful": 1,
        "total": 2
    },
    "_type": "_doc",
    "_version": 2,
    "result": "updated"
}
```

##### 更新局部文档

只更新部分文档，而不是替换整个文档，请求路径`_doc` 替换为  `_update`，请求接受参数 `doc`，它会把 doc 中的参数合并到文档中，文档存在的字段会被替换，不存在的会新增进来；结果 `_version` 为 `"updated"` 、`_version` 增加1 

请求：

```
echo '{"doc": {"year": 2018}}' | http post :9200/book/_doc/1/_update
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 142
content-type: application/json; charset=UTF-8

{
    "_id": "1",
    "_index": "book",
    "_primary_term": 1,
    "_seq_no": 17,
    "_shards": {
        "failed": 0,
        "successful": 1,
        "total": 2
    },
    "_type": "_doc",
    "_version": 8,
    "result": "updated"
}
```

#### 检索文档

##### id 检索文档

请求：

```
http :9200/book/_doc/1
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 183
content-type: application/json; charset=UTF-8

{
    "_id": "1",
    "_index": "book",
    "_primary_term": 1,
    "_seq_no": 17,
    "_source": {
        "key": [
            "ElasticSearch",
            "Java"
        ],
        "page": 200,
        "title": "ElasticSearch 入门",
        "year": 2018
    },
    "_type": "_doc",
    "_version": 8,
    "found": true
}
```

##### 检索某些字段

请求：

```
http :9200/book/_doc/1\?_source=title
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 151
content-type: application/json; charset=UTF-8

{
    "_id": "1",
    "_index": "book",
    "_primary_term": 1,
    "_seq_no": 17,
    "_source": {
        "title": "ElasticSearch 入门"
    },
    "_type": "_doc",
    "_version": 8,
    "found": true
}
```

##### 检索所有文档

请求：

```
http :9200/movie/_search
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 288
content-type: application/json; charset=UTF-8

{
    "_shards": {
        "failed": 0,
        "skipped": 0,
        "successful": 1,
        "total": 1
    },
    "hits": {
        "hits": [
            {
                "_id": "2",
                "_index": "book",
                "_score": 1.0,
                "_source": {
                    "key": [
                        "ElasticSearch",
                        "Java"
                    ],
                    "title": "ElasticSearch 入门",
                    "year": "2020"
                },
                "_type": "1"
            },
            {
                "_id": "1",
                "_index": "book",
                "_score": 1.0,
                "_source": {
                    "key": [
                        "ElasticSearch",
                        "Java"
                    ],
                    "page": 200,
                    "title": "ElasticSearch 入门",
                    "year": 2018
                },
                "_type": "1"
            }
        ],
        "max_score": 1.0,
        "total": {
            "relation": "eq",
            "value": 3
        }
    },
    "timed_out": false,
    "took": 2
}
```

##### Query string

直接在 get 请求时的 url 后追加 `q=key:value`

请求：

```
http :9200/book/_search\?q=title:ElasticSearch
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 247
content-type: application/json; charset=UTF-8

{
    "_shards": {
        "failed": 0,
        "skipped": 0,
        "successful": 1,
        "total": 1
    },
    "hits": {
        "hits": [
            {
                "_id": "1",
                "_index": "book",
                "_score": 0.2876821,
                "_source": {
                    "key": [
                        "ElasticSearch",
                        "Java"
                    ],
                    "page": 200,
                    "title": "ElasticSearch 入门",
                    "year": 2018
                },
                "_type": "1"
            }
        ],
        "max_score": 0.2876821,
        "total": {
            "relation": "eq",
            "value": 1
        }
    },
    "timed_out": false,
    "took": 596
}
```

##### DSL 搜索

请求：

```
echo '{"query": {"match": {"key": "java"}}}' | http get :9200/book/_search
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 245
content-type: application/json; charset=UTF-8

{
    "_shards": {
        "failed": 0,
        "skipped": 0,
        "successful": 1,
        "total": 1
    },
    "hits": {
        "hits": [
            {
                "_id": "1",
                "_index": "book",
                "_score": 0.2876821,
                "_source": {
                    "key": [
                        "ElasticSearch",
                        "Java"
                    ],
                    "page": 200,
                    "title": "ElasticSearch 入门",
                    "year": 2018
                },
                "_type": "1"
            }
        ],
        "max_score": 0.2876821,
        "total": {
            "relation": "eq",
            "value": 1
        }
    },
    "timed_out": false,
    "took": 2
}
```

##### 删除文档

请求：

```
http delete :9200/book/_doc/1
```

响应：

```
HTTP/1.1 200 OK
content-encoding: gzip
content-length: 159
content-type: application/json; charset=UTF-8

{
    "_id": "AccTQ3cBe3_40lKN80zx",
    "_index": "book",
    "_primary_term": 1,
    "_seq_no": 2,
    "_shards": {
        "failed": 0,
        "successful": 1,
        "total": 2
    },
    "_type": "_doc",
    "_version": 2,
    "result": "deleted"
}
```

