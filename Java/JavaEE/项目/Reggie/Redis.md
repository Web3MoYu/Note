# Redis

## 配置文件

### 设置密码

```shell
requirepass yourpassword # 在redis.conf中加入这个来确保连接需要密码
# 然后在执行 redis-cli -h locathost -p 6379后，进入redis的终端后，执行auth yourpasswd
# 或者redis-cli -h locathost -p 6379 -a yourpasswd
```

### AOF

```shell
appendonly no # 默认没有开启aof
```

### RDB

默认开启的持久化

```shell
save <seconds> <changes> # 表示在多少秒内进行多少次操作就保存一次
save 900 1  	# 900s有一个key被修改时保存
save 300 10 	# 300s内有10个key被修改时保存
save 60 10000 	# 60s内有10000个key被修改时保存
```

### 后台启动

```shell
daemonize no # 默认不是后台运行 改成yes改为后台运行
```

### 远程连接

```shell
bind 127.0.0.1 # 默认是打开的，将其注释掉即可
```

