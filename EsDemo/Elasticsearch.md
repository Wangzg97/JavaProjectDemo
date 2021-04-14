## elasticsearch

*笔记来源：[【尚硅谷】ElasticSearch入门到精通2021最新教程（基于ELK技术栈elasticsearch 7.8.x版本）_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1hh411D7sb?p=2&spm_id_from=pageDriver)*

## 1. 简介

### 1.1 Elasticsearch是什么 

The Elastic Stack，包括Elasticsearch、KIbana、Beats和Logstash（也称为ELK Stack）。能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化。Elasticsearch，简称ES，ES是一个开源的高扩展的分布式全文搜索引擎，是整个Elastic Stack技术栈的核心。它可以近乎实时的存储、检索数据；本身扩展性好。



### 1.2 全文搜索引擎

工作原理是计算机索引程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明改词在文章中出现的次数和位置，当用户查询时，检索程序就根据事先建立的索引进行查找，并将查找的结果反馈给用户的检索方式



### 1.3 Elasticsear And Solr

Lucene是Apache软件基金会Jakarta项目组的一个子项目，能够做全文索引和搜寻。Lucene提供了全文搜索功能类库的核心工具包，不能独立使用。

Solr



## 2. Elasticsearch入门

### 2.1 安装

官网：[开源搜索：Elasticsearch、ELK Stack 和 Kibana 的开发者 | Elastic](https://www.elastic.co/cn/)

### 2.2 数据格式

es与mysql存储的数据对比：

| ES                | MYSQL              |
| ----------------- | ------------------ |
| Index（索引）     | Database（数据库） |
| Type（类型）      | Table（表）        |
| Documents（文档） | Row（行）          |
| Fields（字段）    | Column（列）       |

**注：**Elasticsearch7.x中Type的概念已经被删除了。

### 2.3 基础操作

#### 2.3.1 索引操作

##### 2.3.1.1 创建索引 PUT

   ![](https://i.loli.net/2021/04/12/TJicCxRHXqd1DKU.png)

##### 2.3.1.2 取得索引信息 GET

   1）指定索引

   ![](https://i.loli.net/2021/04/12/qiedNOt59ICJQbr.png)

   2）查看所有索引信息

   ![](https://i.loli.net/2021/04/12/8NTxdJeSctKsWL2.png)

##### 2.3.1.3  删除索引 DELETE

   ![](https://i.loli.net/2021/04/12/gW1hCHslrBJdxXT.png)



#### 2.3.2 文档操作

##### 2.3.2.1 创建文档 POST

   1）生成默认id

   ![](https://i.loli.net/2021/04/12/gmHG7OEAUVJhuwT.png)

   2）自定义id

   ![](https://i.loli.net/2021/04/12/RZQTx2ir4z9eWap.png)

##### 2.3.2.2  查询文档 GET

   1）主键查询--查询指定id

   ![](https://i.loli.net/2021/04/12/lNTHA91F4R3BwGW.png)

   2）全查询--查询所有

   ![](https://i.loli.net/2021/04/12/UOdIYLg9X3Q6qZH.png)

##### 2.3.2.3 修改 PUT/POST

   1）全量更新

   ![](C:\Users\wangzg\AppData\Roaming\Typora\typora-user-images\image-20210412221654879.png)


   2）局部更新

   ![image-20210412221854602](C:\Users\wangzg\AppData\Roaming\Typora\typora-user-images\image-20210412221854602.png)

##### 2.3.2.4  删除 DELETE

   ![](https://i.loli.net/2021/04/12/tbUYj9DlumdZzwJ.png)

##### 2.3.2.5  条件查询

​	1）查询指定字段值

![](C:\Users\wangzg\AppData\Roaming\Typora\typora-user-images\image-20210412223702563.png)

​		2）分页查询

查询参数

    {
        "query": {
            "match_all": {
                
            }
        },
        "from": 0,
        "size": 3
    }
![](C:\Users\wangzg\AppData\Roaming\Typora\typora-user-images\image-20210412223944661.png)

​		3）只显示固定字段

查询参数

    {
        "query": {
            "match_all": {
                
            }
        },
        "from": 0,
        "size": 3,
        "_source": ["name"]
    }
![](https://i.loli.net/2021/04/12/AWKhoImfXu5zM2g.png)

​		4）排序

​	查询参数

```json
{
    "query": {
        "match_all": {}
    },
    "from": 0,
    "size": 3,
    "sort": {
        "age": {
            "order": "asc"
        }
    }
}
```



![](https://i.loli.net/2021/04/12/XsyoqmiL7xRYaNb.png)

##### 2.3.2.6 多条件查询 & 范围查询

多条件查询参数：

1）must为多个条件同时成立

```json
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "name": "张三"
                    }
                },
                {
                    "match": {
                        "age": 20
                    }
                }
            ]
        }
    }
}
```

2）should为多个条件任一个成立即可，即“或”。



范围查询参数 **filter** & **range**

```json
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "name": "张三"
                    }
                },
                {
                    "match": {
                        "age": 20
                    }
                }
            ],
            "filter": {
                "range": {
                    "age": {
                        "gt": 10
                    }
                }
            }
        }
    }
}
```



##### 2.3.2.7 全文检索

- match：查询时会进行分词

  查询示例：

  ```json
  {
      "query": {
          "match": {
              "name": "张"
          }
      }
  }
  ```

  

- match_phrase：查询时不会进行分词

  查询示例：

  ```json
  {
      "query": {
          "match_phrase": {
              "name": "张三"
          }
      }
  }
  ```

- 高亮显示

  查询示例：

  ```json
  {
      "query": {
          "match_phrase": {
              "name": "张三"
          }
      },
      "highlight": {
          "fields": {
              "age": {}
          }
      }
  }
  ```

  ![](https://i.loli.net/2021/04/13/nc7sDi1QLqKwCk2.png)

##### 2.3.2.8 聚合操作

查询参数：

1）分组查询

```json
{
    "aggs": {                    //聚合操作
        "age_group": {           //聚合操作的名字
            "terms": {           //分组
                "field": "age"   //分组字段
            }
        }
    },
    "size": 0                    //不显示原始数据，只显示聚合查询的分组的结果
}
```

2）求平均值

```json
{
    "aggs": {                    //聚合操作
        "age_avg": {           //聚合操作的名字
            "avg": {           //平均值
                "field": "age"   //求平均值的字段
            }
        }
    },
    "size": 0                    //不显示原始数据，只显示聚合查询的分组的结果
}
```



##### 2.3.2.9 映射关系

通过  **PUT http://127.0.0.1:9200/user**  创建user索引

通过  **PUT http://127.0.0.1:9200/user/_mapping**  创建映射关系，参数示例如下：

```json
{
    "properties": {
        "name": {
            "type": "text",   //text类型可以分词
            "index": true     //表示当前字段可以进行索引，即可以被查询
        },
        "sex": {
            "type": "keyword",//keyword类型不可以进行分词
            "index": true
        },
        "phone": {
            "type": "keyword",//keyword类型不可以进行分词
            "index": false    //表示当前字段不能进行索引
        }
    }
}
```



## 3. Java API





