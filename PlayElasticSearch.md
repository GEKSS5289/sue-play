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
        重启ElasticSearch
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
## 🏈DSL搜索
   ### 💾数据准备
   #### 😊自定义词库
        慕课网
        慕课
        课网
        慕
        课
        网
   #### 🔑建立索引shop(名字随意)
        使用postman: http://192.168.182.150:9200/自己的索引名
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
## 🧑DSL入门语法
   ### 🍋请求参数的查询(QueryString)
   #### 📃查询[字段]包含[内容]的文档
       GET     /shop/_doc/_search?q=desc:慕课网
       GET     /shop/_doc/_search?q=nickname:慕&q=age:25
       text与keyword搜索对比测试(keyword不会被倒排索引，不会被分词)
       GET     /shop/_doc/_search?q=nickname:super
       GET     /shop/_doc/_search?q=username:super
       GET     /shop/_doc/_search?q=username:super hero
       这种方式称之为QueryString查询方式，参数都是放在url中作为请求参数的。
   ### 🍋DSL基本语法
       QueryString用的很少，一旦参数复杂就难以构建，所以大多查询都会使用dsl来进行查询更好。
       Domain Specific Language
       特定领域语言
       基于JSON格式的数据查询
       查询更灵活，有利于复杂查询
   #### 📃DSL格式语法
        # 查询
        POST     /shop/_doc/_search
        {
            "query": {
                "match": {
                    "desc": "慕课网"
                }
            }
        }
        # 判断某个字段是否存在
        {
            "query": {
                "exists": {
        	        "field": "desc"
        	    }
            }
        }
        语法格式为一个json object，内容都是key-value键值对，json可以嵌套。
        key可以是一些es的关键字，也可以是某个field字段，后面会遇到
        搜索不合法问题定位
        DSL查询的时候经常会出现一些错误查询，出现这样的问题大多都是json无法被es解析，他会像java那样报一个异常信息，根据异常信息去推断问题所在，比如json格式不对，关键词不存在未注册等等，甚至有时候不能定位问题直接复制错误信息到百度一搜就能定位问题了。
## 🏈DSL查询所有与分页
  ### 🔍match_all
       在索引中查询所有的文档
       
       GET     /shop/_doc/_search
       或
       POST     /shop/_doc/_search
       {
           "query": {
               "match_all": {}
           },
           "_source": ["id", "nickname", "age"]
       }
       Head 可视化操作
       
       
  ### 🔍分页查询
       默认查询是只有10条记录，可以通过分页来展示
       
       POST     /shop/_doc/_search
       {
           "query": {
               "match_all": {}
           },
           "from": 0,
           "size": 10
       }
       
       {
       	"query": {
       		"match_all": {}
       	},
       	"_source": [
       		"id",
       		"nickname",
       		"age"
       	],
       	"from": 5,
       	"size": 5
       }
## 🏈DSL-term/match
   ### 📃term精确搜索与match分词搜索
     搜索的时候会把用户搜索内容，比如“慕课网强大”作为一整个关键词去搜索，而不会对其进行分词后再搜索
     
     POST     /shop/_doc/_search
     {
         "query": {
             "term": {
                 "desc": "慕课网"
             }
         }
     }
     对比
     {
         "query": {
             "match": {
                 "desc": "慕课网"
             }
         }
     }
     ⛑注：match会对慕课网先进行分词（其实就是全文检索），在查询，而term则不会，直接把慕课网作为一个整的词汇去搜索。
 ### 📃terms 多个词语匹配检索
    相当于是tag标签查询，比如慕课网的一些课程会打上前端/后端/大数据/就业课这样的标签，可以完全匹配做类似标签的查询
    
    POST     /shop/_doc/_search
    {
        "query": {
            "terms": {
                "desc": ["慕课网", "学习", "骚年"]
            }
        }
    }
## 🏈DSL-match_phrase
  ### 📃match_phrase 短语匹配
    match：分词后只要有匹配就返回，match_phrase：分词结果必须在text字段分词中都包含，而且顺序必须相同，而且必须都是连续的。（搜索比较严格）
    
    slop：允许词语间跳过的数量
    
    POST     /shop/_doc/_search
    {
        "query": {
            "match_phrase": {
                "desc": {
                	"query": "大学 毕业 研究生",
                	"slop": 2
                }
            }
        }
    }
## 🏈DSL-match(operator)/ids
   ### 📃match 扩展
       operator
       or：搜索内容分词后，只要存在一个词语匹配就展示结果
       and：搜索内容分词后，都要满足词语匹配
       POST     /shop/_doc/_search
       {
           "query": {
               "match": {
                   "desc": "慕课网"
               }
           }
       }
       等同于
       {
           "query": {
               "match": {
                   "desc": {
                       "query": "xbox游戏机",
                       "operator": "or"
                   }
               }
           }
       }
       相当于 select * from shop where desc='xbox' or|and desc='游戏机'
       minimum_should_match: 最低匹配精度，至少有[分词后的词语个数]x百分百，得出一个数据值取整。举个例子：当前属性设置为70，若一个用户查询检索内容分词后有10个词语，那么匹配度按照 10x70%=7，则desc中至少需要有7个词语匹配，就展示；若分词后有8个，则 8x70%=5.6，则desc中至少需要有5个词语匹配，就展示。
       minimum_should_match 也能设置具体的数字，表示个数
    
       POST     /shop/_doc/_search
       {
           "query": {
               "match": {
                   "desc": {
                       "query": "女友生日送我好玩的xbox游戏机",
                       "minimum_should_match": "60%"
                   }
               }
           }
       }
  ### 📃根据文档主键ids搜索
      GET /shop/_doc/1001
      查询多个
      
      POST     /shop/_doc/_search
      
      {
          "query": {
              "ids": {
                  "type": "_doc",
                  "values": ["1001", "1010", "1008"]
              }
          }
      }
## 🏈DSL搜索-multi_match/boost
  ### 📃multi_match
    满足使用match在多个字段中进行查询的需求
    
    POST     /shop/_doc/_search
    {
        "query": {
            "multi_match": {
                    "query": "皮特帕克慕课网",
                    "fields": ["desc", "nickname"]
    
            }
        }
    }
### 📃boost
     权重，为某个字段设置权重，权重越高，文档相关性得分就越高。通畅来说搜索商品名称要比商品简介的权重更高。
     
     POST     /shop/_doc/_search
     {
         "query": {
             "multi_match": {
                     "query": "皮特帕克慕课网",
                     "fields": ["desc", "nickname^10"]
     
             }
         }
     }
     nickname^10 代表搜索提升10倍相关性，也就是说用户搜索的时候其实以这个nickname为主，desc为辅，nickname的匹配相关度当然要提高权重比例了。
## 🏈DSL-布尔查询
   ### 可以组合多重查询
         must：查询必须匹配搜索条件，譬如 and
         should：查询匹配满足1个以上条件，譬如 or
         must_not：不匹配搜索条件，一个都不要满足
   ### 📃案例1
        POST     /shop/_doc/_search
        
        {
            "query": {
                "bool": {
                    "must": [
                        {
                            "multi_match": {
                                "query": "慕课网",
                                "fields": ["desc", "nickname"]
                            }
                        },
                        {
                            "term": {
                                "sex": 1
                            }
                        },
                        {
                            "term": {
                                "birthday": "1996-01-14"
                            }
                        }
                    ]
                }
            }
        }
        
        {
            "query": {
                "bool": {
                    "should（must_not）": [
                        {
                            "multi_match": {
                                "query": "学习",
                                "fields": ["desc", "nickname"]
                            }
                        },
                        {
                        	"match": {
                        		"desc": "游戏"
                        	}	
                        },
                        {
                            "term": {
                                "sex": 0
                            }
                        }
                    ]
                }
            }
        }
  ### 📃案例2
        
        {
            "query": {
                "bool": {
                    "must": [
                        {
                        	"match": {
                        		"desc": "慕"
                        	}	
                        },
                        {
                        	"match": {
                        		"nickname": "慕"
                        	}	
                        }
                    ],
                    "should": [
                        {
                        	"match": {
                        		"sex": "0"
                        	}	
                        }
                    ],
                    "must_not": [
                        {
                        	"term": {
                        		"birthday": "1992-12-24"
                        	}	
                        }
                    ]
                }
            }
        }
        
        
        为指定词语加权
        特殊场景下，某些词语可以单独加权，这样可以排得更加靠前。
        
        POST     /shop/_doc/_search
        {
            "query": {
                "bool": {
                    "should": [
                    	{
                    		"match": {
                    			"desc": {
                    				"query": "律师",
                    				"boost": 18
                    			}
                    		}
                    	},
                    	{
                    		"match": {
                    			"desc": {
                    				"query": "进修",
                    				"boost": 2
                    			}
                    		}
                    	}
                    ]
                }
            }
        }
## 🚿DSL-过滤器
   > 对搜索出来的结果进行数据过滤。不会到es库里去搜，不会去计算文档的相关度分数，所以过滤的性能会比较高，过滤器可以和全文搜索结合在一起使用。
   > post_filter元素是一个顶层元素，只会对搜索结果进行过滤。不会计算数据的匹配度相关性分数，不会根据分数去排序，query则相反，会计算分数，也会按照分数去排序。
   > 使用场景：
    
    query:根据用户搜索条件检索匹配记录
    post_filter:用于查询后，对结果数据的筛选
   ### 案例:查询账户金额大于80元，小于160元的用户。并且生日在1998-07-14的用户
    
        gte:大于等于
        lte:小于等于
        gt:大于
        lt:小于
        （除此以外还能做其他的match等操作也行）
        POST     /shop/_doc/_search
        
        {
        	"query": {
        		"match": {
        			"desc": "慕课网游戏"
        		}	
            },
            "post_filter": {
        		"range": {
        			"money": {
        				"gt": 60,
        				"lt": 1000
        			}
        		}
        	}	
        }
## DSL-排序
   > es的排序同sql，可以desc也可以asc。也支持组合排序
### 案例
    POST     /shop/_doc/_search
    {
    	"query": {
    		"match": {
    			"desc": "慕课网游戏"
    		}
        },
        "post_filter": {
        	"range": {
        		"money": {
        			"gt": 55.8,
        			"lte": 155.8
        		}
        	}
        },
        "sort": [
            {
                "age": "desc"
            },
            {
                "money": "desc"
            }
        ]
    }
    对文本排序
    由于文本会被分词，所以往往要去做排序会报错，通常我们可以为这个字段增加额外的一个附属属性，类型为keyword，用于做排序。
    
    创建新的索引
    POST        /shop2/_mapping
    {
        "properties": {
            "id": {
                "type": "long"
            },
            "nickname": {
                "type": "text",
                "analyzer": "ik_max_word",
                "fields": {
                    "keyword": {
                        "type": "keyword"
                    }
                }
            }
        }
    }
    
    插入数据
    POST         /shop2/_doc
    {
        "id": 1001,
        "nickname": "美丽的风景"
    }
    {
        "id": 1002,
        "nickname": "漂亮的小哥哥"
    }
    {
        "id": 1003,
        "nickname": "飞翔的巨鹰"
    }
    {
        "id": 1004,
        "nickname": "完美的天空"
    }
    {
        "id": 1005,
        "nickname": "广阔的海域"
    }
    排序
    {
        "sort": [
            {
                "nickname.keyword": "desc"
            }
        ]
    }
## ☀DSL-结果高亮显示
    POST     /shop/_doc/_search
    {
        "query": {
            "match": {
                "desc": "慕课网"
            }
        },
        "highlight": {
            "pre_tags": ["<tag>"],
            "post_tags": ["</tag>"],
            "fields": {
                "desc": {}
            }
        }
    }
## DSL-prefix-fuzzy-wildcard
  ### prefix
    根据前缀去查询
    
    POST     /shop/_doc/_search
    {
        "query": {
            "prefix": {
                "desc": "imo"
            }
        }
    }

  ### fuzzy
    模糊搜索，并不是指的sql的模糊搜索，而是用户在进行搜索的时候的打字错误现象，搜索引擎会自动纠正，然后尝试匹配索引库中的数据。
    
    POST     /shop/_doc/_search
    {
      "query": {
        "fuzzy": {
          "desc": "imoov.coom"
        }
      }
    }
    # 或多字段搜索
    {
      "query": {
        "multi_match": {
          "fields": [ "desc", "nickname"],
          "query": "imcoc supor",
          "fuzziness": "AUTO"
        }
      }
    }
    
    {
      "query": {
        "multi_match": {
          "fields": [ "desc", "nickname"],
          "query": "演说",
          "fuzziness": "1"
        }
      }
    }
    官文：https://www.elastic.co/guide/cn/elasticsearch/guide/current/fuzzy-match-query.html
### wildcard
    占位符查询。
    
    ？：1个字符
    
    *：1个或多个字符
    
    POST     /shop/_doc/_search
    {
      "query": {
        "wildcard": {
          "desc": "*oo?"
        }
      }
    }
    {
    	"query": {
        	"wildcard": {
        		"desc": "演*"
        	}
    	}
    }
    官文：https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-wildcard-query.html
## 📄深度分页
  ### 分页查询
    POST     /shop/_doc/_search
    {
        "query": {
            "match_all": {}
        },
        "from": 0,
        "size": 10
    }
 ### 深度分页
    深度分页其实就是搜索的深浅度，比如第1页，第2页，第10页，第20页，是比较浅的；第10000页，第20000页就是很深了。
    
    使用如下操作：
    
    {
        "query": {
            "match_all": {}
        },
        "from": 9990,
        "size": 10
    }
    
    {
        "query": {
            "match_all": {}
        },
        "from": 9999,
        "size": 10
    }
    我们在获取第9999条到10009条数据的时候，其实每个分片都会拿到10009条数据，然后集合在一起，总共是10009*3=30027条数据，针对30027数据再次做排序处理，最终会获取最后10条数据。
    
    如此一来，搜索得太深，就会造成性能问题，会耗费内存和占用cpu。而且es为了性能，他不支持超过一万条数据以上的分页查询。那么如何解决深度分页带来的性能呢？其实我们应该避免深度分页操作（限制分页页数），比如最多只能提供100页的展示，从第101页开始就没了，毕竟用户也不会搜的那么深，我们平时搜索淘宝或者百度，一般也就看个10来页就顶多了。
    
    譬如淘宝搜索限制分页最多100页
## 📄深度分页-提升搜索量
 > 通过设置index.max_result_window来突破10000数据
    GET     /shop/_settings
    
    PUT     /shop/_settings
    { 
        "index.max_result_window": "20000"
    }
## 🍡scroll 滚动搜索
 > 一次性查询1万+数据，往往会造成性能影响，因为数据量太多了。这个时候可以使用滚动搜索，也就是 scroll。
   滚动搜索可以先查询出一些数据，然后再紧接着依次往下查询。在第一次查询的时候会有一个滚动id，相当于一个锚标记，随后再次滚动搜索会需要上一次搜索的锚标记，根据这个进行下一次的搜索请求。每次搜索都是基于一个历史的数据快照，查询数据的期间，如果有数据变更，那么和搜索是没有关系的，搜索的内容还是快照中的数据。
 ### scroll=1m，相当于是一个session会话时间，搜索保持的上下文时间为1分钟。
     POST    /shop/_search?scroll=1m
     {
         "query": { 
         	"match_all": {
         	}
         },  
         "sort" : ["_doc"], 
         "size":  5
     }
     
     POST    /_search/scroll
     {
         "scroll": "1m", 
         "scroll_id" : "your last scroll_id"
     }
     官文地址：https://www.elastic.co/guide/cn/elasticsearch/guide/current/scroll.html 
## 🚌批量操作bulk
   ### 基本语法
    bulk操作和以往的普通请求格式有区别。不要格式化json，不然就不在同一行了，这个需要注意。
    
    { action: { metadata }}\n
    { request body        }\n
    { action: { metadata }}\n
    { request body        }\n
    ...
    { action: { metadata }}代表批量操作的类型，可以是新增、删除或修改
    \n是每行结尾必须填写的一个规范，每一行包括最后一行都要写，用于es的解析
    { request body }是请求body，增加和修改操作需要，删除操作则不需要
  ### 批量操作的类型
    action 必须是以下选项之一:
    
    create：如果文档不存在，那么就创建它。存在会报错。发生异常报错不会影响其他操作。
    index：创建一个新文档或者替换一个现有的文档。
    update：部分更新一个文档。
    delete：删除一个文档。
    metadata 中需要指定要操作的文档的_index 、 _type 和 _id，_index 、 _type也可以在url中指定
    
  ### 案例
   #### create新增文档数据，在metadata中指定index以及type
    
    POST    /_bulk
    {"create": {"_index": "shop2", "_type": "_doc", "_id": "2001"}}
    {"id": "2001", "nickname": "name2001"}
    {"create": {"_index": "shop2", "_type": "_doc", "_id": "2002"}}
    {"id": "2002", "nickname": "name2002"}
    {"create": {"_index": "shop2", "_type": "_doc", "_id": "2003"}}
    {"id": "2003", "nickname": "name2003"}
   #### create创建已有id文档，在url中指定index和type
    
    POST    /shop/_doc/_bulk
    {"create": {"_id": "2003"}}
    {"id": "2003", "nickname": "name2003"}
    {"create": {"_id": "2004"}}
    {"id": "2004", "nickname": "name2004"}
    {"create": {"_id": "2005"}}
    {"id": "2005", "nickname": "name2005"}
   #### index创建，已有文档id会被覆盖，不存在的id则新增
    
    POST    /shop/_doc/_bulk
    {"index": {"_id": "2004"}}
    {"id": "2004", "nickname": "index2004"}
    {"index": {"_id": "2007"}}
    {"id": "2007", "nickname": "name2007"}
    {"index": {"_id": "2008"}}
    {"id": "2008", "nickname": "name2008"}
    
   #### update跟新部分文档数据
    
    POST    /shop/_doc/_bulk
    {"update": {"_id": "2004"}}
    {"doc":{ "id": "3004"}}
    {"update": {"_id": "2007"}}
    {"doc":{ "nickname": "nameupdate"}}
   #### delete批量删除
    POST    /shop/_doc/_bulk
    {"delete": {"_id": "2004"}}
    {"delete": {"_id": "2007"}}
   #### 综合批量各种操作
    
    POST    /shop/_doc/_bulk
    {"create": {"_id": "8001"}}
    {"id": "8001", "nickname": "name8001"}
    {"update": {"_id": "2001"}}
    {"doc":{ "id": "20010"}}
    {"delete": {"_id": "2003"}}
    {"delete": {"_id": "2005"}}
    官文：https://www.elastic.co/guide/cn/elasticsearch/guide/current/bulk.html
## ElastSearch 6.4.3 👨‍❤️‍💋‍👨 SpringBoot 2.2.2
> 🙃勿谓言之不预:SpringBoot2.2.2支持的ElasticSearch的版本为6.4.3
### `🧊`创建SpringBoot工程，引入依赖
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    	<!--<version>2.1.5.RELEASE</version>-->
    	<version>2.2.2.RELEASE</version>
    </dependency>
    
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-test</artifactId>
    	<scope>test</scope>
    </dependency>
### `🩹`配置application.yml
    spring:
      data:
        elasticsearch:
          cluster-name: es6
          cluster-nodes: 192.168.1.187:9300
### `🧊`Netty issue fix (解决启动报错)
     创建ESConfig配置类
     @Configuration
     public class ESConfig {
     
         /**
          * 解决netty引起的issue
          */
         @PostConstruct
         void init() {
             System.setProperty("es.set.netty.runtime.available.processors", "false");
         }
     } 
