# Docker安装Oracler

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

- 安装

```shell
docker run -d  \
-p 1524:1521 -p 5501:5500 \
-e ORACLE_SID=ORCLCDB \
-e ORACLE_PDB=ORCLPDB1 \
-e ORACLE_PWD=root \
-e ORACLE_EDITION=standard \
-e ORACLE_CHARACTERSET=AL32UTF8 \
-e ROOT_PASSWORD=root \
-v /home/lsh/mydata/oracle/oradata:/opt/oracle/oradata \
--name orcl19c_01 \
registry.cn-hangzhou.aliyuncs.com/zhuyijun/oracle:19c
```

```sh
docker run -d  \
# 映射端口号 1524:1521是数据库连接的映射 5501:5500是网络监视的映射
-p 1524:1521 -p 5501:5500 \
# SID用于Navicate连接Oracle
-e ORACLE_SID=ORCLCDB \
-e ORACLE_PDB=ORCLPDB1 \
# 密码
-e ORACLE_PWD=root \
# 版本
-e ORACLE_EDITION=standard \
# 字符集
-e ORACLE_CHARACTERSET=AL32UTF8 \
# 指定root密码
e ROOT_PASSWORD=root
# 文件保存路径
-v /home/lsh/mydata/oracle/oradata:/opt/oracle/oradata \
# 名字
--name orcl19c_01 \
registry.cn-hangzhou.aliyuncs.com/zhuyijun/oracle:19c
```



- **查看是否安装成功**

```
# 查看启动日志
docker logs -ft orcl19c_01
```

- **错误处理**

```
注意：
如果安装失败 ,执行以下命令
docker stop orcl19c_01
docker rm orcl19c_01
```

- **连接**

```
docker exec -it orcl19c_01 /bin/bash

sqlplus / as sysdba

show pdbs;
```

