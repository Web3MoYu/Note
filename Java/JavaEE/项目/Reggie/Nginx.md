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

## Nginx配置文件的结构

- 整体结构介绍

```bash
Nginx配置文件（conf/nginx.conf)整体分为三个部分
- 全局块					# 和Nginx运行相关的全局配置 worker_process 1
- events块				 # 和网络连接相关的配置      events{}
- http块					 # 代理，缓存，日志记录，虚拟主机配置 http{}
  - http全局块
  - Server块				# server
  	- Server全局块
  	- location块
```

注意：http块中可以配置多个server块，每个server块中可以配置多个location块。

## 具体应用

### 部署静态资源

​	Nginx可以作为静态Wb服务器来部署静态资源。静态资源指在服务端真实存在并且能够直接展示的一些文件，比如常见的html页面、css文件、js文件、图片、视频等资源。
​	相对于Tomcat,Nginx:处理静态资源的能力更加高效，所以在生产环境下，一般都会将静态资源部署到Nginx中。
​	将静态资源部署到Nginx非常简单，只需要将文件复制到Nginx安装目录下的html目录中即可。

```bash
server{
    listen 80; 				# 监听端口
    server_name localhost;  # 服务器名称
    location / {			# 匹配客户端请求url
    	root html;			# 指定静态资源根目录
     	index index.html;	# 指定默认页面
	}
}
```

### location具体用法

- 语法

  ```bash
  location [= | ~ | ~* | ^~] uri{
  
  }
  
  
  通配符：
  = ：用于不含正则表达式的 uri 前，要求请求字符串与uri严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
  ~ ：用于表示 uri 包含正则表达式，并且区分大小写。
  ~* ：用于表示 uri 包含正则表达式，并且不区分大小写。
  ^~ ：用于不含正则表达式的uri前要求Nginx服务器找到标识uri和请求字符串匹配度最高的location后，立即使用此location处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配。
  注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~ 标识。*
  ```

  

### 代理

#### 正向代理

​	是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送请求并指定目标（原始服务器），然后代理向原始服务器转交请求并将获得的内容返回给客户端。
​	正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。
​	正向代理一般是在客户端设置代理服务器，通过代理服务器转发请求，最终访问到目标服务器。

![](img/6.png)

#### 反向代理

​	反向代理服务器位于用户与目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源，反向代理服务器负责将请求转发给目标服务器。
​	用户不需要知道目标服务器的地址，也无须在用户端作任何设定。

![](img/7.png)

##### 配置反向代理

```bash
server{
	listen 82;
	server_name localhost;
	location / {
		proxy_pass http://192.168.66.128:8080; # 反向代理配置，将请求转发到指定服务器
	}
}
```

![](img/8.png)

### 负载均衡

​	早期的网站流量和业务功能都比较简单，单台服务器就可以满足基本需求，但是随着互联网的发展，业务流量越来越
大并且业务逻辑也越来越复杂，单台服务器的性能及单点故障问题就凸显出来了，因此需要多台服务器组成应用集群，
进行性能的水平扩展以及避免单点故障出现。

- 应用集群：将同一应用部署到多台机器上，组成应用集群，接收负载均衡器分发的请求，进行业务处理并返回响应
  数据
- 负载均衡器：将用户请求根据对应的负载均衡算法分发到应用集群中的一台服务器进行处理

![](img/9.png)

#### 配置负载均衡

```bash
upstreamm targetserver{				# upstream指令可以定义一组服务器
	server 192.168.66.128:8080;
	server 192.168.66.128.8081;
}

server{
	listen 8080;
	server_name localhost;
	location / {
		proxy_pass http://targetserver;
	}
}
```

#### 负载均衡策略

| 名称       | 说明             |
| ---------- | ---------------- |
| 轮询       | 默认方式         |
| weight     | 权重方式         |
| ip_hash    | 依据ip分配方式   |
| least_conn | 依据最少连接方式 |
| url_hash   | 依据url分配方式  |
| fair       | 依据响应时间方式 |

```bash
upstreamm targetserver{				# upstream指令可以定义一组服务器
	server 192.168.66.128:8080 weight=10;
	server 192.168.66.128.8081 wight=5;
}

server{
	listen 8080;
	server_name localhost;
	location / {
		proxy_pass http://targetserver;
	}
}
```

