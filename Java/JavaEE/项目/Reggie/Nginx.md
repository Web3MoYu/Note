# Nginx

## 安装

1.在nginx官网上下载稳定版本，然后解压

```bash
tar -zxvf nginx-1.22.1.tar.gz
```

2.指定安装目录

```bash
# 需要提前建立安装目录
./configure --prefix=/home/lsh/local/nginx
# 这句话会检查依赖 出现./configure: error: the HTTP rewrite module requires the PCRE library.安装以下依赖
sudo apt-get install libpcre3 libpcre3-dev

# ./configure: error: the HTTP gzip module requires the zlib library.
 sudo apt install zlib1g-dev
```

3.安装

```bash
# 如果没有报错可以就可以执行以下命令进行安装了
make && make install
```

## 目录结构

| 重点目录/文件   | 说明                                |
| --------------- | ----------------------------------- |
| conf/nginx.conf | nginx配置文件                       |
| html            | 存放静态文件(html,css,js等)         |
| logs            | 日志目录，存放日志文件              |
| sbin/nginx      | 二进制文件，用于启动，停止nginx服务 |

## 命令

| 命令                | 描述                                                |
| ------------------- | --------------------------------------------------- |
| nginx -s reopen     | 重启Nginx                                           |
| nginx -s reload     | 重新加载Nginx配置文件，然后以优雅的方式重启Nginx    |
| nginx -s stop       | 强制停止Nginx服务                                   |
| nginx -s quit       | 优雅地停止Nginx服务（即处理完所有请求后再停止服务） |
| nginx -t            | 检测配置文件是否有语法错误，然后退出                |
| nginx -?,-h         | 打开帮助信息                                        |
| nginx -v            | 显示版本信息并退出                                  |
| nginx -V            | 显示版本和配置选项信息，然后退出                    |
| nginx -t            | 检测配置文件是否有语法错误，然后退出                |
| nginx -T            | 检测配置文件是否有语法错误，转储并退出              |
| nginx -q            | 在检测配置文件期间屏蔽非错误信息                    |
| nginx -p prefix     | 设置前缀路径(默认是:/usr/share/nginx/)              |
| nginx -c filename   | 设置配置文件(默认是:/etc/nginx/nginx.conf)          |
| nginx -g directives | 设置配置文件外的全局指令                            |
| killall nginx       | 杀死所有nginx进程                                   |

### 检查配置文件正确性

```bash
nginx -t
```

```bash
nginx: the configuration file /home/lsh/local/nginx//conf/nginx.conf syntax is ok
nginx: configuration file /home/lsh/local/nginx//conf/nginx.conf test is successful
```

### 启动

```bash
./nginx 
# 发现报403 forbidden错误
# 1.vim nginx.conf 
# 2.将#user root注释删掉
# 3.重启nginx -s reload即可
```
