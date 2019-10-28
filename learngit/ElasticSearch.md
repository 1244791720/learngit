# 面向文档的ES组成与关系型数据库对比

```
Relational DB -> Databases -> Tables -> Rows -> Columns
Elasticsearch -> Indices -> Types -> Documents -> Fields
```

Elasticsearch集群可以包含多个索引(indices)（数据库），每一个索引可以包含多个类型(types)（表），每一个类型包含多个文档(documents)（行），然后每个文档包含多个字段(Fields)（列）。

- Elasticsearch 中一个索引（Indices）类似关系型数据库的一个库

- Elasticsearch 中一个类型 (type) 类似关系型数据库的一个表

- Elasticsearch 中的一个文档（document）类似关系型数据库的一行

  

# Elasticsearch请求组成部分

```
<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

- VERB HTTP方法： GET  ,  POST  ,  PUT  ,  HEAD  ,  DELETE

- PROTOCOL http或者https协议（只有在Elasticsearch前面有https代理的时候可用）

- HOST Elasticsearch集群中的任何一个节点的主机名，如果是在本地的节点，那么就叫
  localhost

- PORT Elasticsearch HTTP服务所在的端口，默认为9200

- PATH API路径（例如_count将返回集群中文档的数量），PATH可以包含多个组件，例如
  _cluster/stats或者_nodes/stats/jvm

- QUERY_STRING 一些可选的查询请求参数，例如 ?pretty  参数将使请求返回更加美观
  易读的JSON数据

- BODY 一个JSON格式的请求主体（如果请求需要的话）

  ## 简写格式（控制台上的格式）

  ```
  GET /_count
  {
  "query": {
  "match_all": {}
  }
  }
  ```

  _count 表示api路径

# 查找过滤内容

```
GET /_search
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search"        }},
        { "match": { "content": "Elasticsearch" }}
      ],
      "filter": [ 
        { "term":  { "status": "published" }},
        { "range": { "publish_date": { "gte": "2015-01-01" }}}
      ]
    }
  }
}
```

1, query 参数表明查询内容

2，bool 下的2个 match子句 用于设置匹配字段

3， filter 下的子句用于设置过滤字段

# 存储一个文档

```
PUT /megacorp/employee/1
{
"first_name" : "John",
"last_name" : "Smith",
"age" : 25,
"about" : "I love to go rock climbing",
"interests": [ "sports", "music" ]
}
```

path: /megacorp/employee/1  包含三部分信息：

| 名字     | 说明   |
| -------- | ------ |
| megacorp | 索引   |
| employee | 类型名 |
| 1        | id     |

# 通过文档id查找一个文档

```
GET /megacorp/employee/1
```

通过 get 方法直接查找 索引/类型/文档id

返回数据如下：

```
{
  "_index" : "megacorp",
  "_type" : "employee",
  "_id" : "1",
  "_version" : 2,
  "_seq_no" : 1,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "first_name" : "John",
    "last_name" : "Smith",
    "age" : 25,
    "about" : "I love to go rock climbing",
    "interests" : [
      "sports",
      "music"
    ]
  }
}

```

# 通过查询字符串查询

```
GET /megacorp/employee/_search?q=last_name:Smith
```

返回该类型下  last_name 字段为 Smith 的所有文档

```
{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "2",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "Jane",
          "last_name" : "Smith",
          "age" : 32,
          "about" : "I like to collect rock albums",
          "interests" : [
            "music"
          ]
        }
      },
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith",
          "age" : 25,
          "about" : "I love to go rock climbing",
          "interests" : [
            "sports",
            "music"
          ]
        }
      }
    ]
  }
}

```

# 使用DSL语句进行更加复杂的查询

## 匹配字段

DSL(Domain Specific Language特定领域语言)以JSON请求体的形式出现。我们可以这样表示之前关于“Smith”的查询:

```
GET /megacorp/employee/_search
{
	"query" : {
        "match" : {
            "last_name" : "Smith"
        }
	}
}
```

## 过滤字段

我们让搜索稍微再变的复杂一些。我们依旧想要找到姓氏为“Smith”的员工，但是我们只想得
到年龄大于30岁的员工。我们的语句将添加过滤器(filter),它使得我们高效率的执行一个结构
化搜索：

```
GET /megacorp/employee/_search
{
	"query": {
		"filtered": {
			"filter": {
				"range": {
					"age": {
						"gt": 30
					} < 1 >
				}
			},
			"query": {
				"match": {
					"last_name": "smith" < 2 >
				}
			}
		}
	}
}
```

- <1> 这部分查询属于区间过滤器(range filter),它用于查找所有年龄大于30岁的数据

—— gt  为"greater than"的缩写。

- <2> 这部分查询与之前的 match  语句(query)一致。

# 全文搜索（搜索单个关键字相关结果）

搜索所有喜欢“rock climbing”的员工：

```
GET /megacorp/employee/_search
{
	"query": {
		"match": {
			"about": "rock climbing"
		}
	}
}
```



# 短语搜索

```
GET /megacorp/employee/_search
{
	"query": {
		"match_phrase": {
			"about": "rock climbing"
		}
	}
}
```

将 "match" 改为 "match_phrase" 即可实现短语搜索

