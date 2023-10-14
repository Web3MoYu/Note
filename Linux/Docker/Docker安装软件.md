# Mysql

- 拉取mysql镜像

  ```bash
  docker pull mysql:latest
  ```

- 运行容器

  ```bash
  docker run -d --name mysql_01 -p 3306:3306 -v /home/lsh/mydata/mysql:/var/lib/mysql/ -e MYSQL_ROOT_PASSWORD=root mysql --lower_case_table_names=1
  ```

  ```
  参数解释
  -v：挂载宿主机目录和 docker容器中的目录，前面是宿主机目录，后面是容器内部目录。
  -d：后台运行容器。
  -p：映射容器端口号和宿主机端口号，后者是需要映射的端口号，前者时要映射到哪
  -e：环境参数，MYSQL_ROOT_PASSWORD设置root用户的密码。
  –lower_case_table_names： 表名在硬盘上以小写保存，名称比较对大小写不敏感。
  ```

二、修改密码并配置远程连接
1、进入容器

```
docker exec -it mysql bash
```

2、连接并切换数据库

```bash
mysql -uroot -proot
use mysql
```

3、修改密码，设置所有主机可以访问

```bash
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '新密码'
```

4、刷新权限

```bash
flush privileges
```

# Oracle

- **下载镜像**

```
# 下载镜像
docker pull registry.cn-hangzhou.aliyuncs.com/zhuyijun/oracle:19c
```

- **创建挂载文件**

```
# 创建文件
mkdir -p /home/lsh/mydata/oracle/oradata

# 授权，不授权会导致后面安装失败
chmod 777 /home/lsh/mydata/oracle/oradata
```

- **启动容器**

```shell
docker run -d  \
-p 1521:1521 -p 5500:5500 \
-e ORACLE_SID=ORCLCDB \
-e ORACLE_PDB=MYPDB \
-e ORACLE_PWD=root \
-e ORACLE_EDITION=standard \
-e ORACLE_CHARACTERSET=AL32UTF8 \
-v /home/lsh/mydata/oracle/oradata:/opt/oracle/oradata \
--name oracle_19c \
registry.cn-hangzhou.aliyuncs.com/zhuyijun/oracle:19c
```

```sh
docker run -d  \
# 映射端口号 1524:1521是数据库连接的映射 5501:5500是网络监视的映射
-p 1524:1521 -p 5501:5500 \
# SID用于Navicate连接Oracle
-e ORACLE_SID=ORCLCDB \
# PDB是第一个PDB服务器
-e ORACLE_PDB=MYPDB \
# 密码
-e ORACLE_PWD=root \
# 版本
-e ORACLE_EDITION=standard \
# 字符集
-e ORACLE_CHARACTERSET=AL32UTF8 \
# 文件保存路径
-v /home/lsh/mydata/oracle/oradata:/opt/oracle/oradata \
# 名字
--name oracle_19c \
registry.cn-hangzhou.aliyuncs.com/zhuyijun/oracle:19c
```



- **查看是否启动成功**

```
# 查看启动日志
docker logs -ft oracle_19c
```

- **错误处理**

```
注意：
如果安装失败 ,执行以下命令
docker stop oracle_19c
docker rm oracle_19c
```

- **连接**

```shell
docker exec -it oracle_19c /bin/bash
docker exec -u root -it oracle_19c /bin/bash # 以root身份进入
sqlplus / as sysdba

show pdbs;
```

## 错误处理

### 使用Navicate连接不上Oracle

- 检查服务名字在上面的ORACLE_SID中

- 检查用户登录的角色，如果使用sys角色登录则要指定登录角色为sysdba

- 如果使用普通角色登录，在命令行状态下使用下方的方式进行登录

  ```sql
  sqlplus username/password@PDBName  # PDBName使用自己需要的
  ```

### 无法创建角色和删除角色

- 如果无法创建角色需要先进行PDB容器的选择，使用以下命令

  ```sql
  alter session set container=PDBName # PDBName使用自己需要的
  ```

- 如果无法删除角色需要先查询角色在那个容器中,然后进入该容器才能删除

  ```sql
  show con_name
  ```

### 无法连接普通用户

- 如果无法切换普通用户则采取以下方法

  - 在sqlplus中进行切换

    ```sql
    sqlplus username/password@PDBName  # PDBName使用自己需要的
    ```

  - 使用conn进行连接

    ```sql
    conn username/password@PDBName	 # PDBName使用自己需要的
    ```


# Redis

- 拉取redis镜像

  ```bash
  docker pull redis:7.2
  ```

- Docker挂载redis配置文件(一定要下载相同版本的redis配置文件)
    将redis的配置文件进行挂载，以配置文件方式启动redis容器

  挂载：将宿主机的文件和容器内部目录相关联，相互绑定，在宿主机内修改文件的话也随之修改容器内部文件

  挂载文件的位置可以自己随便选择

  1. 挂载redis的配置文件:redis.conf
  (1) 建立配置文件放置目录，执行命令：mkdir 目录/redis/conf/
  (2) 生成配置文件，执行命令：touch redis.conf
  (3) 写入内容：redis.conf的标准文件在redis官网也可以找到


  2. 挂载redis的持久化文件
  建立数据文件放置目录，执行命令：mkdir 目录/redis/data/

- ```
  关闭保护模式-->protected-mode no
  关闭禁止其他访问--> bind
  开启aof-->appappendonly yes
  开启混合方式设置-->aof-use-rdb-preamble  yes
  ```

- 运行容器

  ```bash
  docker run -p 6379:6379 --restart=always \
  --log-opt max-size=100m \
  --log-opt max-file=2 \
  --name myredis \
  -v /home/lsh/mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
  -v /home/lsh/mydata/redis/data:/data \
  -d redis:7.2 redis-server /etc/redis/redis.conf \
  --appendonly yes --requirepass 123456
  ```

  ```bash
  docker run -p 6379:6379 \
  --name redis \
  -v /home/lsh/mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
  -v /home/lsh/mydata/redis/data:/data \
  -d redis:7.2 redis-server /etc/redis/redis.conf \
  --appendonly yes redis:7.2
  ```

  ```
  参数解释
  1.-restart=always 总是开机启动
  2.-log 记录日志
  3.-p 端口映射 后者为容器内的端口，前者为映射的端口
  4.-name 容器名
  5.-v 数据卷挂载
  6.-d redis 后台启动redis
  7.redis-server /etc/redis/redis.conf 以配置文件启动redis，加载容器内的conf文件，最终找到的是挂载的目录 /etc/redis/redis.conf,也就是linux下的 /home/redis/myredis/myredis.conf
  8.-appendonly yes 开启持久化
  9.-requirepass 123456 设置密码
  ```

二、修改密码并配置远程连接
1、进入容器

```
docker exec -it mysql bash
```

2、连接并切换数据库

```bash
mysql -uroot -proot
use mysql
```

3、修改密码，设置所有主机可以访问

```bash
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '新密码'
```

4、刷新权限

```bash
flush privileges
```
