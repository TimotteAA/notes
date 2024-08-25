## nginx学习笔记

抄自维基百科，啥是nginx：

**Nginx**（发音同“engine X”）是异步框架的[网页服务器](https://zh.wikipedia.org/wiki/網頁伺服器)，也可以用作[反向代理](https://zh.wikipedia.org/wiki/反向代理)、[负载平衡器](https://zh.wikipedia.org/wiki/负载均衡)和[HTTP缓存](https://zh.wikipedia.org/wiki/HTTP缓存)。



### 正向代理与反向代理

1. 正向代理：在客户端中配置代理服务器，通过代理服务器将请求转发到目标服务器

   ![image-20240413090409925](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413090409925.png)

2. 反向代理：客户端无感（不需要配置），直接把请求发送到代理服务器，由代理服务器通过负载均衡转发到目标服务器上。

​	下图是直接请求的模式：

​	![image-20240413090709139](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413090709139.png)



​	客户端通过ip + 端口直接请求到服务器，而对于反向代理，客户端把反向代理服务器和业务服务器视为一个服务器：![image-20240413090909193](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413090909193.png)

​	

#### 负载均衡

早期的单一服务器模式：

![image-20240413103457614](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413103457614.png)

当请求增多后，可能会导致服务器（db先扛不住）符合较大，为此，可以使用负载均衡器+多服务器的方式来提高性能：
![image-20240413104343144](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413104343144.png)



nginx可以充当负载均衡器，比如使用轮训算法，让请求尽可能的均摊到各个服务器上。



#### 动静结合

一个服务器由动态资源和静态资源，nginx可以把不同类型的请求代理到不同的服务器上：

![image-20240413104612401](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413104612401.png)









### nginx的安装

此笔记使用ubuntu进行学习，记录下用安装过程

1. 更新镜像源，并安装nginx

   ```bash
   # 更新镜像源
   sudo apt update
   # 安装nginx
   sudo apt install nginx
   # 调整防火墙用
   sudo apt install ufw
   ```

2. 调整防火墙：

   ```BASH
   sudo ufw app list
    Nginx Full # 允许80、433端口放心
     Nginx HTTP # 允许80端口放行
     Nginx HTTPS # 允许433端口放行
     OpenSSH
   
   # 开放80、433
   ufw allow 'Nginx Full'
   # 查看状态
   ufw status
   ```

3. 查看nginx服务状态

   ```bash
   sudo systemctl status nginx
   ```

   在浏览器输入 192.168.1.20（虚拟机ip），如果可以查看到下面的页面，则说明nginx启动成功

   ![image-20240413131222833](https://typora-1316630864.cos.ap-shanghai.myqcloud.com/image-20240413131222833.png)

#### 常用命令

```bash
# 查看版本
nginx -v

# 查看安装位置
where nginx

# 查看配置文件所在路径
root@timotte:/usr/sbin# nginx -t # 本意是用于测试nginx语法的
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

# 关闭nginx
root@timotte:/usr/sbin# ./nginx -s stop
root@timotte:/usr/sbin# ps -ef | grep nginx
root       16278   16162  0 05:23 pts/0    00:00:00 grep --color=auto nginx

# 启动nginx
root@timotte:/usr/sbin# ps -ef | grep ng
root          29       2  0 01:36 ?        00:00:00 [khungtaskd]
root       16280       1  0 05:23 ?        00:00:00 nginx: master process ./nginx
www-data   16281   16280  0 05:23 ?        00:00:00 nginx: worker process
www-data   16282   16280  0 05:23 ?        00:00:00 nginx: worker process
root       16284   16162  0 05:23 pts/0    00:00:00 grep --color=auto ng

# 重新加载配置文件
./nginx -s reload
```



#### 配置文件

```bash
# 查看nginx的配置文件
# 查看配置文件所在路径
root@timotte:/usr/sbin# nginx -t # 本意是用于测试nginx语法的
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

```

配置文件的基本内容如下组成：

```BASH
# main 全局块

# Events 块
events { }

# Http 块
# 配置使用最频繁的部分，代理、缓存、日志定义等绝大多数功能和第三方模块的配置都在这里设置
http {

  # Server 块
  server {

    # Location 块
    location [PATTERN] {}

    location [PATTERN] {}
  }
}


```

Nginx 配置的核心是定义要处理的 `URL` 以及如何响应这些 `URL` 请求，即定义一系列的 **虚拟服务器（Virtual Servers）** 控制对来自特定域名或者 IP 的请求的处理。每一个虚拟服务器定义一系列的 `location` 控制处理特定的 URI 集合。每一个 `location` 定义了对映射到自己的请求的处理场景，可以返回一个文件或者代理此请求。

- `main`：Nginx 的全局配置，对全局生效
- `events`：配置影响 Nginx 服务器或与用户的网络连接
- `http`：可以嵌套多个 Server，配置代理、缓存、日志定义等绝大多数功能和第三方模块的配置
- `server`：配置虚拟主机的相关参数，一个 HTTP 中可以有多个 `server` 块
- `location`：用于配置请求的路由，以及各种页面的处理情况
- `upstream`：配置后端服务器具体地址，负载均衡配置不可或缺的部分

配置文件的语法规则：

- 配置文件由指定的指令控制，指令分为 **简单指令** 与 **指令块** 构成
- 简单指令包含 **指令名称** 和 **指令参数**
- 指令与参数间以空格符号分隔，每条指令以 `;` 分号结尾
- 指令块以 `{}` 大括号将多条指令组织在一起
- `include` 语句允许组合多个配置文件以提升可维护性
- 使用 `#` 符号添加注释，提高可读性
- 使用 `$` 符号使用变量
- 部分指令的参数支持正则表达式



#### main全局块

下面展示了一个基本的全局快配置：

```BASH
# 配置运行 Nginx 服务器用户（组）
user root;
# 错误日志的存放路径
error_log /var/log/nginx/error.log;
# Nginx 进程 PID 存放路径
pid /run/nginx.pid;
# 设置工作进程数量
worker_process 1;
# 配置前台还是后台运行
daemon off;
# 配置文件引入
include /usr/share/nginx/modules/*.conf;
```



#### events

events块主要影响nginx和用户的网络连接

##### use

Nginx 使用何种事件驱动模型。

```nginx
# 不推荐配置它，让 Nginx 自己选择
use <method>;
```

`method` 的可选值：

- `select`
- `poll`
- `kqueue`
- `epoll`
- `/dev/poll`
- `eventport`

##### worker_connections

`worker` 子进程能够处理的最大并发连接数。

```nginx
# 每个子进程的最大连接数为1024
worker_connections 1024;
```

##### accept_mutex

是否打开负载均衡互斥锁。

```nginx
# 默认是 off 关闭的，这里推荐打开
accept_mutex on;
```



### http块

http块中可以嵌套多个server块与全局http块，全局http块中可以定义文件mime-type、日志自定义，是否开启压缩等。

```bash
http {
  # 文件拓展名查找集合
  include   mime.types;
  # 当查找不到对应类型的时候默认值
  default_type    application/octet-stream;
  # 日志格式，定义别名为 main
  log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x"_forwarded_for"';
  # 指定日志输入目录
  access_log logs/access.log main;
  # 调用 sendfile 系统传输文件，开启高效传输模式
  # sendfile 开启的情况下，提高网络包的传输效率（等待，一次传输）
  sendfile      on;
  # 减少网络报文段的数量
  tcp_nopush    on;
  # 客户端与服务器连接超时时间，超时自动断开
  # keepalive_timeout   0;
  keepalive_timeout     65;
  # 开启 Gzip 压缩
  gzip    on;
  # proxy_cache_path /nginx/cache levels=1:2 keys_zone=mycache:16m inactive=24h max_size=1g
  # 当存在多个域名的时候，如果所有配置都写在 nginx.conf 主配置文件中，难免显得杂乱无章
  # 为了方便维护，可以进行拆分配置
  include /etc/nginx/conf.d/*.conf;
  # 虚拟主机
  server {
    # 省略，详细配置看 server 块
    # 位置路由，详细配置看 location 块
    location / {
    }
  }
  # 引入其他的配置文件
  include servers/*;
}
```

#### server块

server块：配置虚拟网络和其url集合

```bash
# 虚拟主机
server {
  # 监听8080端口
  listen          8080;
  # 浏览器访问域名
  server_name     domain.com;

  charset utf-8;
  access_log  logs/domain.access.log  access;

  # 路由
  location / {
    # 访问根目录
    root    www;
    # 入口文件
    index   index.html index.htm;
  }
}

```

###### server_name

指定虚拟主机域名

```bash
server_name <name1>, <name2>
# 示例
server_name www.nginx.com
```

域名匹配的四种写法：

- 精确匹配：`server_name www.nginx.com`
- 左侧通配：`server_name *.nginx.com`
- 右侧通配：`server_name www.nginx.*`
- 正则匹配：`server_name ~^www\.nginx\.*$`

优先级从高到低



###### 测试

```bash
# 这里只列举了 http 端中的 sever 端配置
# 左匹配
server {
  listen         80;
  server_name    *.nginx-test.com;
  root            /usr/share/nginx-test/;
  location / {
    index index.html;
  }
}
# 正则匹配
server {
  listen         80;
  server_name    ~^.*\.nginx-test\..*$;
  root           /usr/share/nginx-test/;
  location / {
    index index.html;
  }
}
# 右匹配
server {
  listen        80;
  server_name   www.nginx-test.*;
  root          /usr/share/nginx-test/;
  location / {
    index index.html;
  }
}

# 完全匹配
server {
  listen        80;
  server_name   www.nginx-test.com;
  root           /usr/share/nginx-test/;
  location / {
    index index.html;
  }
}

```



然后配置本地 DNS 解析 `vim ~/hosts`（macOS 系统）(windows上验证失败)

```bash
# 添加如下内容，其中 121.42.11.34 是云服务器的 IP 地址
121.42.11.34 www.nginx-test.com
121.42.11.34 mail.nginx-test.com
121.42.11.34 www.nginx-test.org
121.42.11.34 doc.nginx-test.com
121.42.11.34 www.nginx-test.cn
121.42.11.34 fe.nginx-test.club
```



### root

指定静态资源目录位置，它可以写在 `http`、`servr`、`location` 块等配置中。

`root` 与 `alias` 的区别主要在于 Nginx 如何解释 `location` 后面的路径的 URI，这会使两者分别以不同的方式将请求映射到服务器文件上。具体来看：

- `root` 的处理结果是：`root` 路径 + `location` 路径
- `alias` 的处理结果是：使用 `alias` 路径替换 `location` 路径

```nginx
root path;

# 例如
location /image {
  root /opt/nginx/static;
}
```

当用户访问 `www.test.com/image/1.png` 时，实际在服务器找的路径是 `/opt/nginx/static/image/1.png`。

另一个例子：

```nginx
server {
  listen        9001;
  server_name   localhost;
  location /hello {
    root        /usr/local/var/www;
  }
}
```

在请求 `http://localhost:9001/hello` 时，服务器返回的路径地址应该是 `/usr/local/var/www/hello/index.html`。

**注意**：`root` 会将定义路径与 `URI` 叠加，`alias` 则只取定义路径。



