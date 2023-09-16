# Docker安装Oracle

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

    

  



