---
created: 2025-03-22T23:10
updated: 2024-05-28T14:55
创建时间: 2025-03-22T23:10
更新时间: 2024-05-28T14:55
---
# 引言

由于单点节点部署在业务量增长、流量增大的情况下访问压力较高，需要引入负载均衡技术来进行调整，主要的优点有：

- 系统的高可用：当某个节点宕机后可以迅速将流量转移至其他节点。
- 系统的高性能：多台服务器共同对外提供服务，为整个系统提供了更高规模的吞吐。
- 系统的拓展性：当业务再次出现增长或萎靡时，可再加入/减少节点，灵活伸缩。

负载均衡的方案主要有两种：硬件层面和软件层面。在硬件层面，比较常用的有硬件负载器A10、F5等，较贵；软件层面有nginx等代表可以使用。



# Nginx 简介

## 定义

Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行，使用C语言开发。与`Redis`同样具备**资源占用少、并发支持高**的特点，在理论上单节点的`Nginx`同时支持`5W`并发连接。

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/nginx前后对比.webp" alt="nginx前后对比" style="zoom:67%;" />

原本客户端是直接请求目标服务器，由目标服务器直接完成请求处理工作，但加入`Nginx`后，所有的请求会先经过`Nginx`，再由其进行分发到具体的服务器处理，处理完成后再返回`Nginx`，最后由`Nginx`将最终的响应结果返回给客户端。



## 核心特性

**事件驱动**：Nginx采用高效的异步事件模型，利用 I/O 多路复用技术。这种模型使 Nginx 能在占用最小内存的同时处理大量并发连接。

**高度可扩展**：Nginx能够支持数千乃至数万个并发连接，非常适合大型网站和高并发应用。例如：为不同的虚拟主机设置不同的 worker 进程数，以增加并发处理能力：

```
http {
  worker_processes auto; # 根据系统CPU核心数自动设置worker进程数
}
```

**轻量级**：相较于传统的基于进程的Web服务器（如Apache），Nginx的内存占用更低，得益于其事件驱动模型，每个连接只占用极小的内存空间。

**热部署**：Nginx支持热部署功能，允许在不重启服务器的情况下更新配置和模块。例如：在修改了 Nginx 配置文件后，可以快速热部署 Nginx 配置：

```
sudo nginx -s reload
```

**负载均衡**：Nginx内置负载均衡功能，通过`upstream`模块实现客户端请求在多个后端服务器间的分配，从而提高服务的整体处理能力。以下是一个简单的upstream配置，它将请求轮询分配到三个后端服务器：

```
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    location / {
        proxy_pass http://backend; # 将请求转发到upstream定义的backend组
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

在这个负载均衡的案例中，Nginx 将请求分发给多个后端服务器。通过 `upstream` 指令定义了一个服务器组，然后在 `location` 块中使用 `proxy_pass` 指令将请求代理到这个服务器组。Nginx 支持多种负载均衡策略，如轮询（默认）、IP 哈希等。



**高性能**：Nginx 在多项 Web 性能测试中表现卓越，能快速处理静态文件、索引文件及代理请求。比如：配置 Nginx 作为反向代理服务器，为大型静态文件下载服务：

```
location /files/ {
  alias /path/to/files/; # 设置实际文件存储路径
  expires 30d; # 设置文件过期时间为30天
}
```

**安全性**：Nginx支持SSL/TLS协议，能够作为安全的Web服务器或反向代理使用。

```
server {
  listen 443 ssl;
  ssl_certificate /path/to/fullchain.pem; # 证书路径
  ssl_certificate_key /path/to/privatekey.pem; # 私钥路径
  ssl_protocols TLSv1.2 TLSv1.3; # 支持的SSL协议
}
```



## 什么是代理

### 正向代理

客户端知道要访问的服务器地址，但服务器只知道请求来自某个代理，而不清楚具体的客户端。==正向代理隐藏了真实客户端的信息==。例如，当无法直接访问国外网站时，我们通过代理服务器访问特定网址。

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/正向代理.webp" alt="正向代理" style="zoom:50%;" />



### 反向代理

多个客户端向反向代理服务器发送请求，Nginx 根据一定的规则将请求转发至不同的服务器。客户端不知道具体请求将被转发至哪台服务器，==反向代理隐藏了后端服务器的信息==。

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/反向代理.webp" alt="反向代理" style="zoom:50%;" />



## 应用场景

- http服务器：

	Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。

- 反向代理：

	 反向代理是针对服务器来说的，一般来说是 客户端直接访问服务器，反向代理是客户端->Nginx->后端接口

- 负载均衡：

	当网站并发量大时，一台服务器已经无法承受，此时需要部署多个服务器来分担压力，这时候可以通过Nginx配置来将请求，通过一定分发规则，分发到不同的服务器来达到负载的作用。Nginx可以通过反向代理来实现负载均衡。

- 虚拟主机：

	有的网站访问量大，需要负载均衡。然而并不是所有网站都如此出色，有的网站，由于访问量太小，需要节省成本，将多个网站部署在同一台服务器上。

	例如将www.abc.com和www.bca.com两个网站部署在同一台服务器上，两个域名解析到同一个IP地址，但是用户通过两个域名却可以打开两个完全不同的网站，互相不影响，就像访问两个服务器一样，所以叫两个虚拟主机。

- 解决跨域问题

	项目开发过程中，有时候前端需要同时访问两个服务的后端接口，两个服务的端口肯定不一样，这时候通过Nginx配置转发就可以实现跨域访问。



## 对比

Nginx 和 Node.js 在某些理念上有相似之处，比如都支持 HTTP 服务、事件驱动和异步非阻塞操作，但两者并不冲突，各有各自擅长的领域：

- Nginx：擅长处理底层的服务器端资源，如静态资源处理、反向代理和负载均衡。
- Node.js：更擅长处理上层的具体业务逻辑。





# 搭建 Nginx 服务

## 配置指令

| 指令 |           说明            |
| :--: | :-----------------------: |
|  =   |   精准匹配，优先级最高    |
|  ^~  | 表示uri以某常规字符串开头 |
|  ~   |   区分大小写的正则匹配    |
|  ~*  |  不区分大小写的正则匹配   |
| ! ~  |   区分大小写不匹配正则    |
| ! ~* | 不区分大小写不匹配的正则  |
|  /   |  通用匹配，匹配任何请求   |

优先级：

表格依次向下，也就是“=”最高 “/” 最低



## 如何安装

### 快速安装

在 Linux 系统中，可以使用包管理器来安装 Nginx。例如，在基于 Debian 的系统上，可以使用 `apt`：

```
sudo apt update
sudo apt install nginx
```

在基于 Red Hat 的系统上，可以使用 `yum` 或 `dnf`：

```
sudo yum install epel-release
sudo yum install nginx
```

在安装完成后，通常可以通过以下命令启动 Nginx 服务：

```
sudo systemctl start nginx
```

设置开机自启动：

```
sudo systemctl enable nginx
```

启动成功后，在浏览器输入服务器 ip 地址或者域名，如果看到 Nginx 的默认欢迎页面，说明 Nginx 运行成功。



### 编译安装^[3]^

以一台初始化的CentOS系统为例

1. 安装依赖： `yum -y install gcc pcre pcre-devel zlib zlib-devel openssl openssl-devel make wget`

2. 创建 nginx 用户

	```
	# 创建名为nginx的新用户组
	groupadd nginx
	
	# 创建名为nginx的用户 指定用户的主目录为/home/nginx 并添加到nginx用户组
	useradd nginx -m  -d /home/nginx -g nginx
	
	# 设置用户 nginx的密码为 nginx 生产环境可以设置的再复杂一些
	echo nginx:nginx|chpasswd
	```

3. 创建 nginx 相关目录

	```
	# 创建相关目录
	mkdir -p /usr/local/nginx/{cache,log,conf/conf.d}
	
	# 授权/usr/local/nginx目录 所属nginx用户
	chown -R nginx:nginx /usr/local/nginx
	```

4. 下载 nginx 包并解压

	```
	# 创建目录 用于保存下载的nginx压缩包
	mkdir /opt/software/
	
	# 切换到 /opt/software/ 目录
	cd /opt/software/
	
	# 下载 nginx-1.20.1.tar.gz
	wget http://nginx.org/download/nginx-1.20.1.tar.gz
	
	# 解压
	tar -zxvf nginx-1.20.1.tar.gz
	```

5. 编译并安装 nginx

	```
	# 切换到刚解压的 nginx-1.20.1目录
	cd nginx-1.20.1
	
	# 设置预编译
	./configure \
	
	# 开始编译并安装
	make  && make install
	```



## 常用命令

1. 启动 Nginx：`nginx`
2. 停止 Nginx：`nginx -s stop`
3. 重新加载 Nginx：`nginx -s reload`
4. 检查 Nginx 配置文件：`nginx -t`（检查配置文件的正确性）
5. 查看 Nginx 版本：`nginx -v`
6. 预编译：`nginx -t`
7. 停止所有服务：`taskkill /f /im nginx`  // 有模块新增时，需要先停止后启动才能生效

其他常用的配合脚本命令：

1. 查看进程命令：`ps -ef | grep nginx`
2. 查看日志，在logs目录下输入指令：`more access.log`



## 配置文件组成

Nginx配置文件主要由指令组成，这些指令可以分布在多个上下文中，主要上下文包括：

1. main: 全局配置，影响其他所有上下文。
2. events: 配置如何处理连接。
3. http: 配置HTTP服务器的参数。
	- location: 基于请求的URI来配置特定的参数。
	- server: 配置虚拟主机的参数。

```yaml
worker_processes auto;   # worker_processes定义Nginx可以启动的worker进程数，auto表示自动检测 

# 定义Nginx如何处理连接 
events {   
    worker_connections 1024;  # worker_connections定义每个worker进程可以同时打开的最大连接数 
}  
  
# 定义HTTP服务器的参数  
http {  
    include mime.types;  # 引入mime.types文件，该文件定义了不同文件类型的MIME类型  
    default_type application/octet-stream;  # 设置默认的文件MIME类型为application/octet-stream  
    sendfile on;  # 开启高效的文件传输模式  
    keepalive_timeout 65;  # 设置长连接超时时间  

    # 定义一个虚拟主机  
    server {  
        listen 80;  # 指定监听的端口
        server_name localhost;  # 设置服务器的主机名，这里设置为localhost  
        
        # 对URL路径进行配置  
        location / {  
            root /usr/share/nginx/html;  # 指定根目录的路径  
            index index.html index.htm;  # 设置默认索引文件的名称，如果请求的是一个目录，则按此顺序查找文件  
        }  

        # 错误页面配置，当请求的文件不存在时，返回404错误页面  
        error_page 404 /404.html;  

        # 定义/40x.html的位置  
        location = /40x.html {  
            # 此处可以配置额外的指令，如代理、重写等，但在此配置中为空  
        }  

        # 错误页面配置，当发生500、502、503、504等服务器内部错误时，返回相应的错误页面  
        error_page 500 502 503 504 /50x.html;  

        # 定义/50x.html的位置  
        location = /50x.html {  
            # 同上，此处可以配置额外的指令  
        }  
    }  
}
```

这个配置文件设置了Nginx监听80端口，使用root指令指定网站的根目录，并为404和50x错误页面提供了位置。其中，`user`和`worker_processes`指令在main上下文中，`events`块定义了事件处理配置，`http`块定义了HTTP服务器配置，包含一个`server`块，该块定义了一个虚拟主机，以及两个`location`块，分别定义了对于404和50x错误的处理。  



## 请求分发原理

  客户端发出的请求`192.168.12.129`最终会转变为：`http://192.168.12.129:80/`，然后再向目标`IP`发起请求，流程如下：

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/请求分发.webp" alt="请求分发" style="zoom:67%;" />

- 由于`Nginx`监听了`192.168.12.129`的`80`端口，所以最终该请求会找到`Nginx`进程；
- `Nginx`首先会根据配置的`location`规则进行匹配，根据客户端的请求路径`/`，会定位到`location /{}`规则；
- 然后根据该`location`中配置的`proxy_pass`会再找到名为`nginx_boot`的`upstream`；
- 最后根据`upstream`中的配置信息，将请求转发到运行`WEB`服务的机器处理，由于配置了多个`WEB`服务，且配置了权重值，因此`Nginx`会依次根据权重比分发请求。



## Nginx 动静分离

以淘宝网站为例分析

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/taobao.webp" alt="taobao" style="zoom: 50%;" />

当浏览器输入`www.taobao.com`访问淘宝首页时，打开开发者调试工具可以很明显的看到，首页加载会出现`100+`的请求数，而正常项目开发时，静态资源一般会放入到`resources/static/`目录下：

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/satic.webp" alt="satic" style="zoom:50%;" />

在项目上线部署时，这些静态资源会一起打成包，那此时思考一个问题：**假设淘宝也是这样干的，那么首页加载时的请求最终会去到哪儿被处理？** 答案毋庸置疑，首页`100+`的所有请求都会来到部署`WEB`服务的机器处理，那则代表着一个客户端请求淘宝首页，就会对后端服务器造成`100+`的并发请求。毫无疑问，这对于后端服务器的压力是尤为巨大的。但此时不妨分析看看，首页`100+`的请求中，是不是至少有`60+`是属于`*.js、*.css、*.html、*.jpg.....`这类静态资源的请求呢？答案是`Yes`。

既然有这么多请求属于静态的，这些资源大概率情况下，长时间也不会出现变动，那为何还要让这些请求到后端再处理呢？能不能在此之前就提前处理掉？当然`OK`，因此经过分析之后能够明确一点：**做了动静分离之后，至少能够让后端服务减少一半以上的并发量。** 到此时大家应该明白了动静分离能够带来的性能收益究竟有多大。

那如何实现动静分离呢？

1. 先在部署`Nginx`的机器，`Nginx`目录下创建一个目录`static_resources`、

2. 将项目中所有的静态资源全部拷贝到该目录下，而后将项目中的静态资源移除重新打包。

3. 稍微修改一下`nginx.conf`的配置，增加一条`location`匹配规则：

	```
	location ~ .*\.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css){
	    root   /soft/nginx/static_resources;
	    expires 7d;
	}
	```

4. 然后照常启动`nginx`和移除了静态资源的`WEB`服务，你会发现原本的样式、`js`效果、图片等依旧有效，其中`static`目录下的`nginx_style.css`文件已被移除，但效果依旧存在。

5. *也可以将静态资源上传到文件服务器中，然后*`location`*中配置一个新的*`upstream`*指向。*



## Nginx 资源压缩

通过`Nginx`对于静态资源实现压缩传输，一方面可以节省带宽资源，第二方面也可以加快响应速度并提升系统整体吞吐。

`Nginx`也提供了三个支持资源压缩的模块`ngx_http_gzip_module、ngx_http_gzip_static_module、ngx_http_gunzip_module`，其中`ngx_http_gzip_module`属于内置模块，代表着可以直接使用该模块下的一些压缩指令，后续的资源压缩操作都基于该模块，先来看看压缩配置的一些参数/指令：

| 参数项              | 释义                                             | 参数值                      |
| :------------------ | ------------------------------------------------ | --------------------------- |
| `gzip`              | 开启或关闭压缩机制                               | `on/off;`                   |
| `gzip_types`        | 根据文件类型选择性开启压缩机制                   | `image/png、text/css...`    |
| `gzip_comp_level`   | 用于设置压缩级别，级别越高越耗时                 | `1~9`（越高压缩效果越好）   |
| `gzip_vary`         | 设置是否携带`Vary:Accept-Encoding`头域的响应头部 | `on/off;`                   |
| `gzip_buffers`      | 设置处理压缩请求的缓冲区数量和大小               | 数量 大小，如`16 8k;`       |
| `gzip_disable`      | 针对不同客户端的请求来设置是否开启压缩           | 如 `.*Chrome.*;`            |
| `gzip_http_version` | 指定压缩响应所需要的最低`HTTP`请求版本           | 如`1.1;`                    |
| `gzip_min_length`   | 设置触发压缩的文件最低大小                       | 如`512k;`                   |
| `gzip_proxied`      | 对于后端服务器的响应结果是否开启压缩             | `off、expired、no-cache...` |

```
http{
    # 开启压缩机制
    gzip on;
    # 指定会被压缩的文件类型(也可自己配置其他类型)
    gzip_types text/plain application/javascript text/css application/xml text/javascript image/jpeg image/gif image/png;
    # 设置压缩级别，越高资源消耗越大，但压缩效果越好
    gzip_comp_level 5;
    # 在头部中添加Vary: Accept-Encoding（建议开启）
    gzip_vary on;
    # 处理压缩请求的缓冲区数量和大小
    gzip_buffers 16 8k;
    # 对于不支持压缩功能的客户端请求不开启压缩机制
    gzip_disable "MSIE [1-6]\."; # 低版本的IE浏览器不支持压缩
    # 设置压缩响应所支持的HTTP最低版本
    gzip_http_version 1.1;
    # 设置触发压缩的最小阈值
    gzip_min_length 2k;
    # 关闭对后端服务器的响应结果进行压缩
    gzip_proxied off;
}
```

在上述的压缩配置中，最后一个`gzip_proxied`选项，可以根据系统的实际情况决定，总共存在多种选项：

- `off`：关闭`Nginx`对后台服务器的响应结果进行压缩。
- `expired`：如果响应头中包含`Expires`信息，则开启压缩。
- `no-cache`：如果响应头中包含`Cache-Control:no-cache`信息，则开启压缩。
- `no-store`：如果响应头中包含`Cache-Control:no-store`信息，则开启压缩。
- `private`：如果响应头中包含`Cache-Control:private`信息，则开启压缩。
- `no_last_modified`：如果响应头中不包含`Last-Modified`信息，则开启压缩。
- `no_etag`：如果响应头中不包含`ETag`信息，则开启压缩。
- `auth`：如果响应头中包含`Authorization`信息，则开启压缩。
- `any`：无条件对后端的响应结果开启压缩机制。

在原本的`index`页面中引入一个`jquery-3.6.0.js`文件后分别来对比下压缩前后的区别：开启压缩机制前访问时，`js`文件的原始大小为`230K`，当配置好压缩后再重启`Nginx`，会发现文件大小从`230KB→69KB`，效果立竿见影！

> 注意点：
> ①对于图片、视频类型的数据，会默认开启压缩机制，因此一般无需再次开启压缩。
> ②对于`.js`文件而言，需要指定压缩类型为`application/javascript`，而并非`text/javascript、application/x-javascript`。



## Nginx 缓冲区





## Nginx 缓存机制





## Nginx 实现 IP 黑白名单



## Nginx 跨域配置



## Nginx 防盗链设计





## Nginx 大文件传输配置



## Nginx 配置 SSL 证书



## Nginx 的高可用



## Nginx 性能优化





## 配置实战

### 静态界面配置

```sh
server {
  listen       10117;
  access_log  logs/host.10117.log  ;
  charset utf-8;
  location / {
      charset utf-8;
      root D:\hbidding\hst-meet\web\meet; #静态界面路径
      index index.html; #默认首页地址
   }
   location ~* \.(jpg|png|gif|jpeg)$ {
      expires 30d;
      add_header Cache-Control "public" 
   }
}
```

在这个案例中，Nginx 配置为服务静态文件，如 HTML、CSS、JavaScript 和图片等。通过设置 `root` 指令，指定了静态文件的根目录。同时，对于图片文件，通过 `expires` 指令设置了缓存时间为 30 天，减少了服务器的负载和用户等待时间。



### 后端服务器转发配置

```
server {
    listen       9006;  #监听端口
    access_log  logs/host.9006.log ; #日志
    client_max_body_size 100m; # 设置客户端请求体的最大大小为 100MB
    index index.html; # 设置默认的索引文件为 index.html
    server_name www.baidu.com; #可以不写，不写默认当前服务器
    root /user/project/admin;  # 设置Web内容的根目录为/user/project/admin
    # 表示以 purchaser 开头的地址
    location /purchaser {
            proxy_pass http://127.0.0.1:7132;
            proxy_set_header   Host    $http_host ;
            proxy_set_header   X-Real-IP   $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    # /表示所有地址
    location / {        
            proxy_pass http://127.0.0.1:8006;
            proxy_set_header   Host    $http_host ;
            proxy_set_header   X-Real-IP   $remote_addr;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        }
        error_page   500 502 503 504  /50x.html;
}
```

### Https配置

```
server {
    listen 443 ssl;
    server_name example.com;
    ssl_certificate /path/to/your/fullchain.pem;
    ssl_certificate_key /path/to/your/private.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';
    ssl_prefer_server_ciphers on;
    location / {
        root /path/to/your/https/static/files;
        index index.html index.htm;
    }
}
```

这个案例展示了如何配置 Nginx 以支持 HTTPS。通过指定 SSL 证书和私钥的路径，以及设置 SSL 协议和加密套件，可以确保数据传输的安全。同时，建议使用 HTTP/2 协议以提升性能。

### 安全防护

```
server {
    listen 80;
    server_name example.com;
    location / {
        # 防止 SQL 注入等攻击
        rewrite ^/(.*)$ /index.php?param=$1 break;
        # 限制请求方法，只允许 GET 和 POST
        if ($request_method !~ ^(GET|POST)$ ) {
            return 444;
        }
        # 防止跨站请求伪造
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-Content-Type-Options "nosniff";
        add_header X-XSS-Protection "1; mode=block";
    }
}
```

通过 `rewrite` 指令，可以防止一些常见的 Web 攻击，如 SQL 注入。这种限制请求方法，可以减少服务器被恶意利用的风险。同时，添加了一些 HTTP 头部来增强浏览器安全，如防止点击劫持和跨站脚本攻击（XSS）等。

### websocket配置

```
location / {
        ...
        #其他配置不变，下面添加这两列配置。
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
}
```

### 负载均衡

```
upstream mkj_pools {
  server 192.168.1.72:8880   weight=1;
  server 192.168.1.73:9990   weight=2;
  server 192.168.1.74:8989   weight=3;
  #weigth参数表示权值，权值越高被分配到的几率越大
}
server { 
  listen 80;
  server_name www.mkj.com;
  location / {   
    proxy_pass http://mkj_pools;
   }
}
```

### 其他配置

```
proxy_connect_timeout 60s; #nginx和后端服务器连接超时时间
proxy_read_timeout 300s; # 连接成功后，后端服务器响应时间
```



# Nginx深入学习^[2]^

## 健康检查

Nginx 能够监测后端服务器的健康状态。如果服务器无法正常工作，Nginx 将自动将请求重新分配给其他健康的服务器。

```
http {
    upstream myapp1 { # 定义了后端服务器组
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
        
        # 健康检查配置。
        # 每10秒进行一次健康检查，如果连续3次健康检查失败，则认为服务器不健康；
        # 如果连续2次健康检查成功，则认为服务器恢复健康。
        check interval=10s fails=3 passes=2;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

> `check interval=10s fails=3 passes=2;` 这样的配置语法在开源版本的 NGINX 上是不支持的。这是 `ngx_http_upstream_check_module` 模块的特有语法，而该模块不包含在 NGINX 的开源版本中，需要自行下载、编译和安装。该模块是开源免费的，具体详情请参见[ ngx_http_upstream_check_module 文档](https://github.com/yaoweibin/nginx_upstream_check_module)。

Nginx 会定期向定义的服务器发送健康检查请求。如果服务器响应正常，则认为服务器健康；如果服务器没有响应或者响应异常，则认为服务器不健康。当服务器被标记为不健康时，Nginx 将不再将请求转发到该服务器，直到它恢复健康。



## 负载均衡算法

Nginx 支持多种负载均衡算法，可以适应不同的应用场景。以下是几种常见的负载均衡算法的详细说明和示例：

**Weight轮询（默认）**：权重轮询算法是 Nginx 默认的负载均衡算法。它按顺序将请求逐一分配到不同的服务器上。通过设置服务器权重（weight）来调整不同服务器上请求的分配率。

```
upstream backend {
  server backend1.example.com weight=3; # 设置backend1的权重为3
  server backend2.example.com; # backend2的权重为默认值1
  server backend3.example.com weight=5; # 设置backend3的权重为5
}
```

如果某一服务器宕机，Nginx会自动将该服务器从队列中剔除，请求代理会继续分配到其他健康的服务器上。

**IP Hash 算法：** 根据客户端IP地址的哈希值分配请求，确保客户端始终连接到同一台服务器。

```
upstream backend {
  ip_hash; # 启用IP哈希算法
  server backend1.example.com;
  server backend2.example.com;
}
```

根据客户端请求的IP地址的哈希值进行匹配，将具有相同IP哈希值的客户端分配到指定的服务器。这样可以确保同一客户端的请求始终被分配到同一台服务器，有助于保持用户的会话状态。

**fair算法：** 根据服务器的响应时间和负载来分配请求。

```
upstream backend {
  fair; # 启用公平调度算法
  server backend1.example.com;
  server backend2.example.com;
  server backend3.example.com;
}
```

它结合了轮询和IP哈希的优点，但Nginx默认不支持公平调度算法，需要安装额外的模块（upstream_fair）来实现。

**URL Hash 算法：** 根据请求的 URL 的哈希值分配请求，每个请求的URL会被分配到指定的服务器，有助于提高缓存效率。

```
upstream backend {
  hash $request_uri; # 启用URL哈希算法
  server backend1.example.com;
  server backend2.example.com;
}
# 根据请求的URL哈希值来决定将请求发送到backend1还是backend2。
```

这种方法需要安装Nginx的hash软件包。



## 开源模块

`Nginx`拥有丰富的开源模块，有很多还有待我们探索，除了一些定制化的需求需要自己开发，大部分的功能都有开源。大家可以在 `NGINX` 社区、`GitHub` 上搜索 "nginx module" 可以找到。





> 参考文章：
>
> [1] [微信公众号-最实用的Nginx配置笔记](https://mp.weixin.qq.com/s/695NMvUquECiKLHIpLVohg)
>
> [2] [微信公众号-作为前端开发，感受下 nginx 带来的魅力！](https://mp.weixin.qq.com/s/ll6KS9fW9O_PZYmRHwuDnA)
>
> [3] [微信公众号-Nginx 生产环境部署的最佳实践](https://mp.weixin.qq.com/s/CFWqTkTE7E4gOkhRlpo9Wg)
>
> [4] [微信公众号-三年前端还不会配置Nginx，被老板打了，今天一口气学完](https://mp.weixin.qq.com/s/tC5EF-vhiBrjXlTQLuXRhw)
>
> 