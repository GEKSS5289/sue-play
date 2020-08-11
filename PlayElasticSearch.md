# 🏀PlayElasticSearch
> 系统环境:CentOS 7
## ❤集群健康
    GET     /_cluster/health
## 🔨创建索引
    PUT     /index_test
    {
        "settings": {
            "index": {
                "number_of_shards": "2",
                "number_of_replicas": "0"
            }
        }
    }
## 🔍查看索引
    GET     _cat/indices?v
## 🗑删除索引
    DELETE      /index_test
## 🔨创建索引的同时创建mappings
    PUT     /index_str
    {
        "mappings": {
            "properties": {
                "realname": {
                	"type": "text",
                	"index": true
                },
                "username": {
                	"type": "keyword",
                	"index": false
                }
            }
        }
    }
## 🔍查看分词效果
    GET         /index_mapping/_analyze
    {
    	"field": "realname",
    	"text": "imooc is good"
    }
## ✏尝试修改
    POST        /index_str/_mapping
    {
        "properties": {
            "name": {
            	   "type": "long"
            }
        }
    }
## 🔨为已存在的索引创建或创建mapping
    POST        /index_str/_mapping
    {
        "properties": {
            "id": {
            	"type": "long"
            },
            "age": {
            	"type": "integer"
            },
            "nickname": {
                "type": "keyword"
            },
            "money1": {
                "type": "float"
            },
            "money2": {
                "type": "double"
            },
            "sex": {
                "type": "byte"
            },
            "score": {
                "type": "short"
            },
            "is_teenager": {
                "type": "boolean"
            },
            "birthday": {
                "type": "date"
            },
            "relationship": {
                "type": "object"
            }
        }
    }
    注：某个属性一旦被建立，就不能修改了，但是可以新增额外属性
## 📙主要数据属性
    text, keyword, string
    long, integer, short, byte
    double, float
    boolean
    date
    object
    数组不能混，类型一致
## 🉐字符串
    text：文字类需要被分词被倒排索引的内容，比如商品名称，商品详情，商品介绍，使用text。
    keyword：不会被分词，不会被倒排索引，直接匹配搜索，比如订单状态，用户qq，微信号，手机号等，这些精确匹配，无需分词。
##  📃文档基本操作-添加文档数据
    POST /my_doc/_doc/1 -> {索引名}/_doc/{索引ID}（是指索引在es中的id，而不是这条记录的id，比如记录的id从数据库来是1001，并不是这个。如果不写，则自动生成一个字符串。建议和数据id保持一致> ）
    
    {
        "id": 1001,
        "name": "imooc-1",
        "desc": "imooc is very good, 慕课网非常牛！",
        "create_date": "2019-12-24"
    }
    
    {
        "id": 1002,
        "name": "imooc-2",
        "desc": "imooc is fashion, 慕课网非常时尚！",
        "create_date": "2019-12-25"
    }
    
    {
        "id": 1003,
        "name": "imooc-3",
        "desc": "imooc is niubility, 慕课网很好很强大！",
        "create_date": "2019-12-26"
    }
    
    {
        "id": 1004,
        "name": "imooc-4",
        "desc": "imooc is good~！",
        "create_date": "2019-12-27"
    }
    
    {
        "id": 1005,
        "name": "imooc-5",
        "desc": "慕课网 is 强大！",
        "create_date": "2019-12-28"
    }
    
    {
        "id": 1006,
        "name": "imooc-6",
        "desc": "慕课是一个强大网站！",
        "create_date": "2019-12-29"
    }
    
    {
        "id": 1007,
        "name": "imooc-7",
        "desc": "慕课网是很牛网站！",
        "create_date": "2019-12-30"
    }
    
    {
        "id": 1008,
        "name": "imooc-8",
        "desc": "慕课网是很好看！",
        "create_date": "2019-12-31"
    }
    
    {
        "id": 1009,
        "name": "imooc-9",
        "desc": "在慕课网学习很久！",
        "create_date": "2020-01-01"
    }
    ⛑注：如果索引没有手动建立mappings，那么当插入文档数据的时候，会根据文档类型自动设置属性类型。这个就是es的动态映射，帮我们在index索引库中去建立数据结构的相关配置信息。
    “fields”: {“type”: “keyword”}
    对一个字段设置多种索引模式，使用text类型做全文检索，也可使用keyword类型做聚合和排序
    “ignore_above” : 256
    设置字段索引和存储的长度最大值，超过则被忽略
##  🗑文档基本操作-删除文档
    DELETE /my_doc/_doc/1
    ⛑注：文档删除不是立即删除，文档还是保存在磁盘上，索引增长越来越多，才会把那些曾经标识过删除的，进行清理，从磁盘上移出去。
    修改文档
##  ✏文档基本操作-修改文档
   ### 局部
        POST /my_doc/_doc/1/_update
        {
            "doc": {
                "name": "慕课"
            }
        }
   ### 全量替换
        PUT /my_doc/_doc/1
        {
             "id": 1001,
            "name": "imooc-1",
            "desc": "imooc is very good, 慕课网非常牛！",
            "create_date": "2019-12-24"
        }
        ⛑注：每次修改后，version会更改
## 🔍文档基本操作-查询
  ### 📄查询文档
        GET /index_demo/_doc/1
        GET /index_demo/_doc/_search
  ### 📬查询结果
        {
            "_index": "my_doc",
            "_type": "_doc",
            "_id": "2",
            "_score": 1.0,
            "_version": 9,
            "_source": {
                "id": 1002,
                "name": "imooc-2",
                "desc": "imooc is fashion",
                "create_date": "2019-12-25"
            }
        }
  ### 🍬元数据
        _index：文档数据所属那个索引，理解为数据库的某张表即可。
        _type：文档数据属于哪个类型，新版本使用_doc。
        _id：文档数据的唯一标识，类似数据库中某张表的主键。可以自动生成或者手动指定。
        _score：查询相关度，是否契合用户匹配，分数越高用户的搜索体验越高。
        _version：版本号。
        _source：文档数据，json格式。
  ### 👙定制结果集
        GET /index_demo/_doc/1?_source=id,name
        GET /index_demo/_doc/_search?_source=id,name
  ### 👑判断文档是否存在
        HEAD /index_demo/_doc/1
## 😂🔒文档乐观锁控制
  ### 🔬观察操作
   #### 📄插入新数据
        POST /my_doc/_doc
        {
               "id": 1010,
               "name": "imooc-1010",
               "desc": "imoocimooc！",
               "create_date": "2019-12-24"
        }
        # 此时 _version 为 1
   #### 📄修改数据
       POST    /my_doc/_doc/{_id}/_update
       {
           "doc": {
               "name": "慕课"
           }
       }
       # 此时 _version 为 2
   #### 📄模拟两个客户端操作同一个文档数据，_version都携带为一样的数值
       # 操作1
       POST    /my_doc/_doc/{_id}/_update?if_seq_no={数值}&if_primary_term={数值}
       {
           "doc": {
               "name": "慕课1"
           }
       }
       
       # 操作2
       POST    /my_doc/_doc/{_id}/_update?if_seq_no={数值}&if_primary_term={数值}
       {
           "doc": {
               "name": "慕课2"
           }
       }
 ### 版本元数据
   > _seq_no：文档版本号，作用同_version（相当于学生编号，每个班级的班主任为学生分配编号，效率要比学校教务处分配来的更加高效，管理起来更方便）
   > _primary_term：文档所在位置（相当于班级）
   > 官文地址：https://www.elastic.co/guide/en/elasticsearch/reference/current/optimistic-concurrency-control.html
## 🚀分词与内置分词器
  ### 🤨什么是分词?
  > 把文本转换为一个个的单词，分词称之为analysis。es默认只对英文语句做分词，中文不支持，每个中文字都会被拆分为独立的个体。 
  > 英文分词：I study in imooc.com
  > 中文分词：我在慕课网学习
  ### 😁案例
        POST /_analyze
        {
            "analyzer": "standard",
            "text": "text文本"
        }
        POST /my_doc/_analyze
        {
            "analyzer": "standard",
            "field": "name",
            "text": "text文本"
        }
  ### 📼es内置分词器
        standard：默认分词，单词会被拆分，大小会转换为小写。
        simple：按照非字母分词。大写转为小写。
        whitespace：按照空格分词。忽略大小写。
        stop：去除无意义单词，比如the/a/an/is…
        keyword：不做分词。把整个文本作为一个单独的关键词。
        POST /_analyze
        {
            "analyzer": "standard",
            "text": "My name is Peter Parker,I am a Super Hero. I don't like the Criminals."
        }
## 🔨建立ik中文分词器
   ### 🚬下载ik中文分词器
   #### 📞官方提供:[ik-7.2.4中文分词器](https://github.com/medcl/elasticsearch-analysis-ik)
   #### 🤝作者提供:[ik-7.2.4中文分词器](https://shushun.oss-cn-shenzhen.aliyuncs.com/software/elasticsearch-analysis-ik-7.4.2.zip)
   ### 📦解压
         unzip elasticsearch-analysis-ik-7.4.2.zip -d /usr/local/elasticsearch-7.4.2/plugins/ik/
   ### ⚗测试
        POST /_analyze
        {
            "analyzer": "ik_max_word",
            "text": "上下班车流量很大"
        }
## 🗄自定义中文词库
   ### 📄在/usr/local/elasticsearch-7.4.2/plugins/ik/config/ 创建custom.dic
        vim custom.dic
   ### 📄在custom.dic添加内容
        sue
        df
   ### 📄配置自定义扩展词典
        cd elasticsearch-analysis-ik-7.4.2.zip -d /usr/local/elasticsearch-7.4.2/plugins/ik/config
        vim IKAnalyzer.cfg.xml
            <entry key="ext_dict">custom.dic</entry>
   ### 👌重启
## 🔍DSL搜索-数据准备
   ### 💾数据准备
   #### 😊自定义词库
        慕课网
        慕课
        课网
        慕
        课
        网
   #### 🔑建立索引shop(名字随意)
   ##### 🤚手动建立mappings
        POST        /shop/_mapping
        {
            "properties": {
                "id": {
                    "type": "long"
                },
                "age": {
                    "type": "integer"
                },
                "username": {
                    "type": "keyword"
                },
                "nickname": {
                    "type": "text",
                    "analyzer": "ik_max_word"
                },
                "money": {
                    "type": "float"
                },
                "desc": {
                    "type": "text",
                    "analyzer": "ik_max_word"
                },
                "sex": {
                    "type": "byte"
                },
                "birthday": {
                    "type": "date"
                },
                "face": {
                    "type": "text",
                    "index": false
                }
            }
        }
   
   ##### 📼录入数据
        POST         /shop/_doc/1001
        
        {
            "id": 1001,
            "age": 18,
            "username": "imoocAmazing",
            "nickname": "慕课网",
            "money": 88.8,
            "desc": "我在慕课网学习java和前端，学习到了很多知识",
            "sex": 0,
            "birthday": "1992-12-24",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1002,
            "age": 19,
            "username": "justbuy",
            "nickname": "周杰棍",
            "money": 77.8,
            "desc": "今天上下班都很堵，车流量很大",
            "sex": 1,
            "birthday": "1993-01-24",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1003,
            "age": 20,
            "username": "bigFace",
            "nickname": "飞翔的巨鹰",
            "money": 66.8,
            "desc": "慕课网团队和导游坐飞机去海外旅游，去了新马泰和欧洲",
            "sex": 1,
            "birthday": "1996-01-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1004,
            "age": 22,
            "username": "flyfish",
            "nickname": "水中鱼",
            "money": 55.8,
            "desc": "昨天在学校的池塘里，看到有很多鱼在游泳，然后就去慕课网上课了",
            "sex": 0,
            "birthday": "1988-02-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1005,
            "age": 25,
            "username": "gotoplay",
            "nickname": "ps游戏机",
            "money": 155.8,
            "desc": "今年生日，女友送了我一台play station游戏机，非常好玩，非常不错",
            "sex": 1,
            "birthday": "1989-03-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1006,
            "age": 19,
            "username": "missimooc",
            "nickname": "我叫小慕",
            "money": 156.8,
            "desc": "我叫凌云慕，今年20岁，是一名律师，我在琦䯲星球做演讲",
            "sex": 1,
            "birthday": "1993-04-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1007,
            "age": 19,
            "username": "msgame",
            "nickname": "gamexbox",
            "money": 1056.8,
            "desc": "明天去进货，最近微软处理很多游戏机，还要买xbox游戏卡带",
            "sex": 1,
            "birthday": "1985-05-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1008,
            "age": 19,
            "username": "muke",
            "nickname": "慕学习",
            "money": 1056.8,
            "desc": "大学毕业后，可以到imooc.com进修",
            "sex": 1,
            "birthday": "1995-06-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1009,
            "age": 22,
            "username": "shaonian",
            "nickname": "骚年轮",
            "money": 96.8,
            "desc": "骚年在大学毕业后，考研究生去了",
            "sex": 1,
            "birthday": "1998-07-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1010,
            "age": 30,
            "username": "tata",
            "nickname": "隔壁老王",
            "money": 100.8,
            "desc": "隔壁老外去国外出差，带给我很多好吃的",
            "sex": 1,
            "birthday": "1988-07-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1011,
            "age": 31,
            "username": "sprder",
            "nickname": "皮特帕克",
            "money": 180.8,
            "desc": "它是一个超级英雄",
            "sex": 1,
            "birthday": "1989-08-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }
        
        {
            "id": 1012,
            "age": 31,
            "username": "super hero",
            "nickname": "super hero",
            "money": 188.8,
            "desc": "BatMan, GreenArrow, SpiderMan, IronMan... are all Super Hero",
            "sex": 1,
            "birthday": "1980-08-14",
            "face": "https://www.imooc.com/static/img/index/logo.png"
        }