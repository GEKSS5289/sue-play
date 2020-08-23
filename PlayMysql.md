# 🐬PlayMysql8.0
> 👍mysql主从
# 👍mysql主从
## 🤨master

    编辑msyql:master上的/etc/my.cnf:
        log-bin=imooc_mysql  (MYSQL的bin-log名字)
        server-id=1 (mysql实例全局唯一 并且大于0)    
       
    在mysql主上创建用于备份账号:
        CREATE USER 'repl'@'%' IDENTIFIED BY mysql_native_password 'password'; 
        GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';
        
    mysql主上加锁，阻止所有写的操作:
        FLUSH TABLES WITH READ LOCK;
        
    mysql主上，查看bin-log的文件和位置:
        SHOW MASTER STATUS;
        
    mysql主上dump数据:
        mysqldump --all-databases --master-data > dbdump.db -uroot -p
        
    mysql主上解锁:
        UNLOCK TABLES;
        
## 🙃slave

    编辑msyql:slave上的/etc/my.cnf:
        server-id=2  (mysql实例全局唯一 并且大于0 与master区分开)   
        
    mysql从上导入之前dump数据:
        mysql < dbdump.db -uroot -p
        
    mysql从上配置主从连接信息:
         CHANGE MASTER TO
        	MASTER_HOST='master_host_name', 	
        	MASTER_PORT=port_num 
            MASTER_USER='replication_user_name', 
        	MASTER_PASSWORD='replication_password', 			        
        	MASTER_LOG_FILE='recorded_log_file_name',			   
            MASTER_LOG_POS=recorded_log_position;
            参数解释:
                master_host_name : MySQL主的地址
                port_num : MySQL主的端口（数字型）
                replication_user_name : 备份账户的用户名
                replication_password : 备份账户的密码
                recorded_log_file_name ：bin-log的文件名
                recorded_log_position : bin-log的位置（数字型）
                bin-log的文件名和位置 是 步骤5中的show master status 得到的
                
    mysql从上开启同步:
        mysql> START SLAVE;
        
    查看slave状态:
        show slave status \G;
            