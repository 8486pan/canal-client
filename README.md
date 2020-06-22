### 项目说明

使用Canal Server监听数据库变动，使用客户端把接收到的数据变化投递到NSQ消息队列！

#### 数据格式：

`{"logfileOffset":2740229,"dbName":"db_user","logfileName":"mysql-bin.000001","eventType":"UPDATE","rawData":{"app_key":"70bd9b0a7efbae6d68f261207ee815b8","card_no":"90011160","updated_at":"0","module":"","created_at":"1508083200","id":"1","source":"90011160","operator":"800000000425","content":"日志内容","username":"张三丰"},"newData":{"app_key":"70bd9b0a7efbae6d68f261207ee815b8","card_no":"90011160","updated_at":"0","module":"","created_at":"1508083200","id":"1","source":"90011160","operator":"800000000425","content":"日志内容","username":"张无忌"},"executeTime":1557798930000,"tableName":"operation_log"}`

### 安装 Docker

curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

usermod -aG docker  root

systemctl start docker

### Canal Server使用说明

https://github.com/alibaba/canal/wiki/Docker-QuickStart

### 构建容器

docker build -t canal-client:1.0 ./app/canal-client

### 配置说明

https://github.com/alibaba/canal/wiki/AdminGuide

### NSQ 消息队列

docker run --name nsqlookupd -p 4160:4160 -p 4161:4161 -d nsqio/nsq /nsqlookupd

docker run --name nsqd -p 4150:4150 -p 4151:4151 -v /data/var/lib/nsqd:/data -d nsqio/nsq /nsqd -broadcast-address=172.17.0.1 -lookupd-tcp-address=172.17.0.1:4160 -max-req-timeout=86400s -data-path=/data

docker run --name nsqadmin -p 4171:4171 -d nsqio/nsq /nsqadmin -lookupd-http-address=172.17.0.1:4161

请注意：如果不是本地请指定内网或者外网IP地址(nsqd和nsqadmin容器), max-req-timeout延迟消息最大值！！！

### 容器运行方法

Canal Server:

docker run --name canal-server -p 11111:11111 -e canal.destinations=canal-server -e canal.instance.master.address=172.17.0.1:3306 -e canal.instance.dbUsername=username -e canal.instance.dbPassword=password -d canal/canal-server:v1.1.3

canal.destinations配置项和客户端配置文件必须一致，username用户必须有Replication远程访问权限,同一个客户端只能匹配一个服务端！！！

Canal client:

docker run --name canal-client -v /data/var/log/canal-client:/canal/logs -v /data/var/etc/canal-client/:/canal/config -d canal-client:1.0 -jar /canal/canal_client.jar /canal/config/config.properties

/canal/config/config.properties为配置文件,路径可以自定义（文件名后缀需要一致）!!!
