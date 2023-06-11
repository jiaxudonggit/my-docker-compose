# docker-compose

## 注意：所有的环境变量配置文件，都需要你自己再重新配置，因为我已经将我自己的敏感信息全部替换掉了（密码、host、用户名等等）

#### 启动服务

```shell
docker-compose up -d
```

#### 停止服务

```shell
docker-compose stop
```

#### 停止并删除服务

```shell
docker-compose down
```

#### 指定docker-compose配置文件

```shell
docker-compose -f docker-compose-nginx.yml up -d
```

#### 注意：要记得给 volumes 挂载的本地文件夹添加执行权限

```shell
chmod -R 777 /data/docker/
```

## Nginx && networks

```shell
docker-compose -f docker-compose-nginx.yml up -d
```

## PostgresSQL

```shell
docker-compose -f docker-compose-pg.yml up -d
```

## MySQL

```shell
docker-compose -f docker-compose-mysql.yml up -d
```

## MongoDb

```shell
docker-compose -f docker-compose-mongo.yml up -d
```

#### 创建用户

```shell
# 进入mongodb shell
docker exec -it mongo mongo admin

# 切换数据库，如果没有的话就是创建库
use admin

# 创建一个名为 admin，密码为 123456 的用户。
db.createUser({ user:'admin',pwd:'123456',roles:[{ role:'root', db: 'admin'}]})

# 尝试使用上面创建的用户信息进行连接。
db.auth('admin', '123456')
```

## Redis

```shell
docker-compose -f docker-compose-redis.yml up -d
```

## RabbitMq

```shell
docker-compose -f docker-compose-rabbitmq.yml up -d
```

## Solr

```shell
docker-compose -f docker-compose-solr.yml up -d
```

#### 首次运行容器之前需要修改共享文件夹的权限

```shell
sudo chown -R 8983:8983 /data/docker/solr
```

#### 创建核心

```shell
# 进入容器
docker exec  -it --user=solr solr /bin/bash

# 设置身份验证
export SOLR_AUTH_TYPE='basic'
export SOLR_AUTHENTICATION_OPTS='-Dbasicauth=username:password'

# 创建核心，创建核心后记得配置分词
solr create_core -c demo_core
```

## Elasticsearch

```shell
docker-compose -f docker-compose-elasticsearch.yml up -d
```

#### 执行设置用户名和密码的命令,这里需要为4个用户分别设置密码，elastic, kibana, logstash_system,beats_system

```shell
# 进入容器
docker exec -it elasticsearch /bin/bash

# 添加密码
bin/elasticsearch-setup-passwords interactive

Initiating the setup of passwords for reserved users elastic,kibana,logstash_system,beats_system.
You will be prompted to enter passwords as the process progresses.
Please confirm that you would like to continue [y/N]y
Enter password for [elastic]:
passwords must be at least [6] characters long
Try again.
Enter password for [elastic]:
Reenter password for [elastic]:
Passwords do not match.
Try again.
Enter password for [elastic]:
Reenter password for [elastic]:
Enter password for [kibana]:
Reenter password for [kibana]:
Enter password for [logstash_system]:
Reenter password for [logstash_system]:
Enter password for [beats_system]:
Reenter password for [beats_system]:
Changed password for user [kibana]
Changed password for user [logstash_system]
Changed password for user [beats_system]
Changed password for user [elastic]
```

#### 安装插件

```shell
# 进入容器
docker exec -it --user=elasticsearch elasticsearch /bin/bash

./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.17.1/elasticsearch-analysis-ik-7.17.1.zip
```

## GitLab

```shell
docker-compose -f docker-compose-gitlab.yml up -d
```

## MaxWell

```shell
docker-compose -f docker-compose-maxwell.yml up -d
```

## Nginx WebUI

```shell
docker-compose -f docker-compose-nginx-webui.yml up -d
```

## Oracle 11g

```shell
chown -R 500.500 /data/docker/oracle_11g  # 500 500 是容器内 oracle 组和用户的 id
docker-compose -f docker-compose-oracle.yml up -d
```

#### 初始化数据库

```shell
docker exec -it oracle_11g bash
cd /home/oracle/
source .bash_profile
sqlplus / as sysdba  # 连接 oracle 数据库
```

```sql
alter user system identified by oracle;                   # 修改 DBA 账号的密码
alter user sys identified by oracle;                      # 修改 DBA 账号的密码
alter profile default limit password_life_time unlimited; # 设置密码为永不过期
create user test identified by oracle;                    # 创建一个 test 用户，密码 oracle
select * from dba_users t where t.username = 'TEST';      # 查询用户是否创建成功
grant connect, resource to test;                          # 给用户授予连接和数据权限
```

#### 解决修改密码时提示“ORA-01109: database not

```
rm -rf /home/oracle/app/oracle/flash_recovery_area/helowin/control02.ctl
cp /home/oracle/app/oracle/oradata/helowin/control01.ctl /home/oracle/app/oracle/flash_recovery_area/helowin/control02.ctl
sqlplus / as sysdba          # 以 dba 身份连接 oracle 数据库
shutdown immediate           # 关闭数据库实例（这里会报错，不用管）
startup                      # 启动实例
```

[https://www.cnblogs.com/mike666/p/13999397.html](https://www.cnblogs.com/mike666/p/13999397.html)
[https://blog.csdn.net/anqixiang/article/details/118296293](https://blog.csdn.net/anqixiang/article/details/118296293)

## Ruby Rvm

```shell
docker-compose -f docker-compose-rvm.yml up --build -d
```

### 通过docker exec -it 快捷执行容器内命令，添加以下方法到~/.bashrc

```shell
# 调用方式：dexec icc-api rails c
dexec() {
  local container_name="/root/workspace/$1"
  local arr=($@)
  local command="${arr[@]:1:$#}"
  echo "cd ${container_name} && ${command}"
  docker exec -it ruby-rvm bash -c "cd ${container_name} && ${command}"
}
```

## Clash

```shell
cp -R ./conf/clash/ /data/docker/clash/
docker-compose -f docker-compose-clash.yml up -d
```

记得将ui静态文件和nginx配置文件copy到nginx对应文件夹

## ChatGPT

```shell
docker-compose -f docker-compose-chat.yml up -d
```
