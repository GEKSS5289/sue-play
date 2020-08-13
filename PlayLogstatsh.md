# 🤝🏻logstatsh同步数据库数据到ElasticSearch中
### 📄同步简介
   - 💿安装logstatsh
   - 🍪在logstatsh目录里创建sync文件夹
   - 🍩在sync文件夹下创建logstatsh-db-sync.conf配置文件
   - 🍭上传mysql-connector-java-x.x.x.jar(根据自己环境需要引入数据库驱动)到Sync文件夹里
   - 🍥上传需要同步的数据库执行脚本(如:xx.sql)
##  ⚙logstatsh-db-sync.conf配置文件
        input {
            jdbc {
                # 设置 MySql/MariaDB 数据库url以及数据库名称
                jdbc_connection_string => "jdbc:mysql://127.0.0.1:3306/foodie-shop-dev?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=UTC"
                # 用户名和密码
                jdbc_user => "root"
                jdbc_password => "123456"
                # 数据库驱动所在位置，可以是绝对路径或者相对路径
                jdbc_driver_library => "/usr/local/logstash-6.4.3/sync/mysql-connector-java-8.0.18.jar"
                # 驱动类名
                jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
                # 开启分页
                jdbc_paging_enabled => "true"
                # 分页每页数量，可以自定义
                jdbc_page_size => "1000"
                # 执行的sql文件路径
                statement_filepath => "/usr/local/logstash-6.4.3/sync/foodie-items.sql"
                # 设置定时任务间隔  含义：分、时、天、月、年，全部为*默认含义为每分钟跑一次任务
                schedule => "* * * * *"
                # 索引类型
                type => "_doc"
                # 是否开启记录上次追踪的结果，也就是上次更新的时间，这个会记录到 last_run_metadata_path 的文件
                use_column_value => true
                # 记录上一次追踪的结果值
                last_run_metadata_path => "/usr/local/logstash-6.4.3/sync/track_time"
                # 如果 use_column_value 为true， 配置本参数，追踪的 column 名，可以是自增id或者时间
                tracking_column => "updated_time"
                # tracking_column 对应字段的类型
                tracking_column_type => "timestamp"
                # 是否清除 last_run_metadata_path 的记录，true则每次都从头开始查询所有的数据库记录
                clean_run => false
                # 数据库字段名称大写转小写
                lowercase_column_names => false
            }
        }
        output {
            elasticsearch {
                # es地址
                hosts => ["127.0.0.1:9200"]
                # 同步的索引名
                index => "foodie-items"
                # 设置_docID和数据相同
                #document_id => "%{id}"
                document_id => "%{itemId}"
            }
            # 日志输出
            stdout {
                codec => json_lines
            }
        }
 ## 📼同步数据库脚本
        SELECT
            i.id as itemId,
            i.item_name as itemName,
            i.sell_counts as sellCounts,
            ii.url as imgUrl,
            tempSpec.price_discount as price,
            i.updated_time as updated_time
        FROM
            items i
        LEFT JOIN
            items_img ii
        on
            i.id = ii.item_id
        LEFT JOIN
            (SELECT item_id,MIN(price_discount) as price_discount from items_spec GROUP BY item_id) tempSpec
        on
            i.id = tempSpec.item_id
        WHERE
            ii.is_main = 1
            and
            i.updated_time >= :sql_last_value
 ## 🍻启动
     cd /usr/local/logstatsh-6.4.3/bin
     ./logstash -f /usr/local/logstash-6.4.3/sync/logstash-db-sync.conf