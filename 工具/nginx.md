#### Why Nginx

> Nginx 在于优秀的网络 IO 模型

- 常见的 IO 模型
	- 阻塞模型
	- 非阻塞模型
	- IO 多路复用
	- 异步 IO

#### http 状态码

1. 信息相应（100-199）
	- 101: 表明服务器已收到并正在处理该请求，但当前没有可用的响应
2. 成功响应（200-299）
	- 
3. 重定向消息（300-399）
	- 301 Moved Permanently: 请求的 URL 已永久更改，在响应中给出了新的 URL
	- 302 Found: 请求的 URI 暂时更改
	- 304 Not Modified
1. 客户端错误（400-499）
	-  
2. 服务端错误（500-599）
	- 

#### Nginx 编译安装

```shell
# 在官网下载 Nginx

# 解压文件
tar -zxvf <...>

# 进入解压目录
cd <...>

# 查看 configure 支持的编译参数
./configure --help

# 编译并指定安装目录
./configure --prefix=/usr/local
```

#### Nginx 二进制文件配置到系统环境变量

```shell
# 编辑文件
vim /etc/profile

# 在 /etc/profile 中添加配置
export PATH=$PATH:<nginx 二进制文件所在的目录>

# 重新加载配置文件
source /etc/profile
```

#### Ningx 配置成系统服务

```shell
# 在一定目录下（根据系统会有差异），创建 nginx.servce

# 更变 nginx.service 的权限
chmod 755 /.../nginx.service

# 编辑nginx.service

# 使用系统命令来操作 Nginx 服务
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
systemctl status nginx
systemctl enable nginx
systemctl disable nginx
```

- nginx.service

```nginx.service
[Unit]
Description=nginx web service
Documentation=http://nginx.org/en/docs/
After=network.target

[Service]
Type=forking
PIDFile=/.../nginx.pid
ExecStartPre=/.../nginx -t -c /.../nginx.conf
ExecStart=/.../nginx
ExecReload=/.../nginx -s reload
Execstop=/.../nginx -s stop
PrivateTmp=true

[Install]
wantedBy=default.target
```

#### Nginx 常用功能

- 静态资源部署
- 地址重写
- 正则表达式
- 反向代理
- 负载均衡
- web 缓存
- 环境部署
- 用户认证模块

#### Nginx 命令

```shell
nginx -v  # 查看 nginx 版本信息
nginx -V  # 查看 nginx 版本信息与编译信息
nginx -h  # 查看帮助信息
nginx -?  # 查看帮助信息

nginx -t  # 测试 nginx 配置文件是否存在语法错误
nginx -t -c </.../xxx.config>  # 测试特定文件
nginx -c </.../xxx.config>  # 以特定配置文件启动 nginx
nginx -T  # 测试并列出详细内容
nginx -tq # 测试并且只输出错误信息

nginx -p <dir>  # 指定 nginx 文件的安装路径

nginx -g "pid logs/abc.pid"  # 指定启动时的参数配置，一般都直接在 nginx.conf 中指定
```

#### Nginx 的核心组成

- nginx 二进制可执行文件
- nginx.conf 配置文件
- error.1og 错误日志记录
- access.1og 访问日志记录

#### nginx.conf

```nginx
# 全局块

# master 进程开关
# =on
# workers 也不会打开，但是要进程 stop（reload 也不行） 才能生效
master_process on;

# 子进程个数
# =auto
# 建议与 CPU 内核数一致
worker_processes 12;

# 设置是否以守护进程的方式启动（后台启动）
# =on
daemon on;

# nginx.pid 文件的存储位置，示例仅作参考
# 编译时可通过 --pid-path 指定
pid /usr/local/nginx/logs/nginx.pid;

# error.log 文件的存储位置，示例仅作参考
# 编译时可通过 --error-log-path 指定
# 可以配置在全局、http、server、location
# 日志级别（升序）：debug, info, notice, warn, error, crit, alert, emerg
error_log <文件路径> [日志级别];

# 引入其他的配置文件
include <文件路径>;

# event 块
events {

	# 用来设置网络连接序列化
	# =on
	# 用来解决“惊群问题”
	# 如果开启，将会对多个 worker 的连接进行序列化，一个个来唤醒接收，防止了多个进程对连接的争抢
	# 对性能的影响不是绝对的，如果请求相对多可能设置为 off 更好
	accept_mutex: on;

	# =off
	# 用来设置是否允许同时接收多个网络连接
	# 一般建议设置为 on，效率较高
	multi_accept: on;

	# 配置单个 worker 的最大连接数
	# =512
	# 这里的连接数不仅仅包括和前端用户建立的连接数，而且包括所有可能的连接数
	# number 不能大于操作系统支持打开的最大文件句柄数
	worker_connections: <number>;

	# 设置 Nginx 服务器选择哪种事件驱动来处理网络消息
	# 默认值与操作系统有关
	# 可选 select/poll/epoll/kqueue
	# linux 内核 > 2.6 才可以使用最为推荐的 epoll
	# 这些值的选择也可以在编译的时候使用以下
	# --with-select_module、--without-select_module、--with-poll_module、--without-poll_module
	# 来设置是否需要将对应的事件驱动模块编译到 Nginx 内核
	use epoll;
}

# http 块
http {
	# include <要引入的另一个配置文件>;
	# 为了让浏览器识别繁多种类的文件，就需要使用 MIME Type
	include mime.types;
	
	# 配置 Nginx 响应前端请求默认的 MIME 类型
	# 可以 http, server, location 块中设置
	# application/octet-stream: 二进制文件，会触发客户端下载
	# text/plain: 纯文本
	# text/html: html
	# application/json: json
	default_type application/octet-stream;

	# =75
	# 支持的块：http、server、location
	# HTTP 是一种无状态协议，客户端向服务端发送一个 TCP 请求，服务端响应完毕后断开连接
	# 客户端向服务端发送多个请求，每个请求都需要重新创建一次连接，效率相对来说比较低
	# keepalive_timeout 使服务端在处理完一个请求后保持一定时间的 TCP 连接，之后若接收到来自这个客户端的其他请求，服务端就会利用这个连接
	keepalive_timeout 75;

	# =100
	# 用来设置一个 keep-alive 连接使用的次数
	# 支持的块：http、server、location
	keepalive_requests 100;

	# 用于配置运行 Nginx 服务器的 worker 进程的用户和用户组
	# 可以在编译时通过 --user --group 指定
	# 如果编译时确定了，最终以 nginx.conf 文件为准
	# 默认是 nobody
	# 如果使用普通用户，则根目录不能设置为没有权限的目录，如 root 的目录
	# user 用户 [用户组];
	user hu260456 大牛逼家族;

	# 一个 http 块可以配置多个 server 块
	server {
		# 支持的块 server
		# 监听 80 端口
		listen 80;
		# 监听特定 ip 的所有端口
		listen <ip>;
		# 监听特定 ip 的 80 端口
		listen <ip>:80;
		# default_server 用来将此虚拟主机设置成默认主机
		# 如果没有匹配到对应的 address:port，则会默认执行此 server，如果不指定默认使用的是第一个server
		listen 80 default_server;

		# 用来设置虚拟主机、域名
		# 多个名称使用空格分隔
		server_name localhost;
		server_name www.nginx520.com;
		# 通配符方式只能出现在域名的首和尾
		server_name *.nginx520.com;
		# 正则表达式方式，以 ~ 标识一段正则表达式
		# 可以使用 $1 获取到 () 中的内容：\w+ 匹配到的内容
		server_name ~^www\.(\w+)\.com$;
		# 使用 defaul_server 时可以直接配置成 _
		server_name _;
		# server 的匹配顺序：精确匹配 -> 前通配符匹配 -> 后通配符匹配 -> 正则表达式匹配 -> 被 default_server 匹配 -> 被第一个 server 匹配

		# 一个 server 块可以配置多个 location 块
		# location 是匹配 URI 的（而非 URL）

		# 匹配根路径
		location / {
			# root 的处理结果：root 路径 + location 路径
			root html;
			# alias 的处理结果：直接使用 alias 路径
			# 因为是直接替换，所以 location 以 / 结尾时 alias 也需要以 / 结尾
			alias /home/www/static/html;

			# index 用来设置默认页
			# 支持多个文件，以空格分割，从前往后找到第一个为止
			# 支持的块：http, server, location
			index index.html;
		}

		# 匹配 /hello 开头 URI
		location /hello {
			default_type text/plain;
			return 200 "hello, world!";
		}

		# 用于不包含正则表达式的 URI 前
		# 功能和 /hello 一致
		# 唯一不同的是，如果模式匹配，那么就停止搜索其他模式了
		location ^~/hello {}

		# 精确匹配 /hello
		location =/hello {}

		# 区分大小写的正则表达式匹配
		location ~^/hello\w+$ {}

		# 不区分大小写的正则表达式匹配
		location ~*^/hello\w+$ {}

		# 可以配置多个 error_page 块
		# 用来设置网站的错误页面
		# 支持的块：http, server, location
		
		# 发生 404 时跳转到 google
		error_page 404 http://www.google.com;
		
		# 当服务端响应 500 502 503 504 时候，请求跳转到 /50x.html
		error_page 500 502 503 504 /50x.html;

		# @ 符的使用
		error_page 404 @jump_to_error;
		location @jump_to_error {}

		# 发生 404 错误的时候，请求跳转到 /50x.html，并且返回 200
		error_page 404 =200 /50x.html;
	}
}
```

#### Nginx 服务启停

```shell
# 方式1：信号控制
# 信号：
# TERM/INT 立即关闭整个服务
# QUIT     “优雅”地关闭整个服务
# HUP      重读配置文件并使用服务对新配置项生效，master 不改变，worker pid 改变
# USR1     重新打开日志文件，可以用来进行日志切割
# USR2     平滑升级到最新版的nginx
# WINCH    所有子进程不在接收处理新连接，相当于给work进程发送QUIT指令
# 优雅停止服务示例：
kill -QUIT <master 进程号>

# 方式2：命令行
# s: signal
nginx -s stop  # 快速关闭，TERM/INT
nginx -s quit  # 优雅关闭，QUIT
nginx -s reopen  # USR1
nginx -s reload  # HUP
```

#### 自定义服务日志

> nginx 只有两类日志：access.log, error.log

- access.log: 来记录用户所有的访问请求
- error.log: 记录 nginx 本身的运行时错误

```nginx
# 指定日志的输出格式
# 支持的块：http
# http_user_agent: 用户代理
log_format <some_name> '--- $http_user_agent ---';

# 设置用户访问日志的相关属性
# 支持的块：http, server, location
# 格式名称要和上述 some_name 对应
access_log <设置日志的存放路径> [format_name];

```

#### sendfile

```nginx
# =off
# 来设置 Nginx 服务器是否使用 sendfile() 传输文件
# 可以大大提高 Nginx 处理静态资源的性能
# 支持的块：http、server、location
sendfile: on;
```

##### sendfile 和 gzip 的冲突

> 问题：gzip 在应用程序缓冲区内工作，而开启 sendfile 后，文件直接在内核区流转

- 解决办法：
	- ==事先压缩文件==
	- 可以使用 ngxhttp_gzip_static_module 模块的 gzip_static 指令，这个模块不是默认安装的模块
	- gzip_static 寻找与资源同名的 .gz 文件
	- `gzip_static =off | on | always;`
	- 开启 gzip_static 后把 gzip 关了
	- always：无论客户端支不支持，都启用

#### tcp_nopush

```nginx
# =off
# 打开 sendfile 后才能生效
# 用来提升网络包的传输效率
# 支持的块：http、server、location
tcp_nopush on;
```

#### tcp_nodelay

> tcp_nopush 和 tcp_nodelay 看起来是互斥的，在 linux2.5.9 以后的版本中两者是可以兼容的

```nginx
# =on
# 必须在 keep-alive 连接开启的情况下才生效
# 用来提高网络包传输的实时性
# 支持的块：http、server、location
tcp_nodelay on;
```

#### 静态资源压缩

> gzip 指令是由 ngx_http_gzip_module 模块提供的，该模块是内置的

```nginx
# =off
# 打开 gzip
# 支持的块：http、server、location
gzip on;

# =text/html（也就是说 gzip 默认只压缩 html）
# 根据响应页的 MIME 类型选择性地开启 Gzip 压缩功能
# 支持的块：http、server、location
gzip_types text/plain text/html;

# =1
# 设置 gzip 的压缩等级
# 值域 1（最低） ~ 9（最高）
# 支持的块：http、server、location
gzip_comp_level 5;

# =off
# 主要是告诉接收方，所发送的数据经过了 Gzip 压缩处理
# 用于设置 header 是否携带 Vary: Accept-Encoding 信息
# 支持的块：http、server、location
gzip_vary on;

# 指定 Ningx 向系统申请几个多大的缓存空间
# 这个值的设定一般和操作系统有关，所以建议此项不设置，使用默认值即可
# 支持的块：http、server、location
gzip_buffers 4 19K;

# 一些客户端不支持 gzip 压缩
# gzip_disable <正则表达式匹配 User-Agent>
# 支持的块：http、server、location
# 设置对 IE1~6 客户端不适用 gzip
gzip_disable "MSIE [1-6]\.";

# 指定使用 Gzip 的 HTTP 最低版本
# 一般采用默认值即可
# =1.1
# 支持的块：http、server、location
gzip_http_version 1.1;

# =20
# 针对传输数据的大小，选择性地开启和关闭 Gzip 功能
# 数据大小对应 header 中的 Content-Length
# Ningx 默认单位 Byte，可选单位 K|k, M|m
# 支持的块：http、server、location
gzip_min_length 256;

# =off
# 与反向代理有关
# 用来设置是否对服务端返回的结果进行 Gzip 压缩
# off - 关闭Ngnx服务器对后台服务器返回结果的Gzip压缩
# expired - 启用压缩，如果header头中包含"Expires"头信息
# no-cache - 启用压缩，如果header头中包含"Cache-Control:no-cache"头信息
# no-store - 启用压缩，如果header头中包含"Cache-Control:no-store”头信息
# private - 启用压缩，如果header头中包含"Cache-Control:private"头信息
# no_last_modified - 启用压缩，如果header头中不包含"Last-Modified"头信息
# no_etag - 启用压缩，如果header头中不包含"ETag"头信息
# auth - 启用压缩，如果header头中包含"Authorization"头信息
# any - 无条件启用压缩
# 支持的块：http、server、location
gzip_proxied off;
```

#### 静态资源缓存处理

- HTTP 协议中和页面缓存相关的字段，我们先来认识下

|    header     | 说明                      |
| :-----------: | :---------------------- |
|    Expires    | 缓存过期的日期和时间              |
| Cache-Control | 设置和缓存相关的配置信息            |
| Last-Modified | 请求资源最后修改时间              |
|     ETag      | 请求变量的实体标签的当前值，比如文件的MD5值 |

- Cache-Control 的说明

| 指令               | 说明                      |
| ---------------- | ----------------------- |
| must-revalidate  | 可缓存但必须再向源服务器进行确认        |
| no-cache         | 缓存前必须确认其有效性             |
| no-store         | 不缓存请求或响应的任何内容           |
| no-transform     | 代理不可更改媒体类型              |
| public           | 可向任意方提供响应的缓存            |
| private          | 仅向特定用户返回响应              |
| proxy-revalidate | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| max-age=<秒>      | 响应最大Age值                |
| S-maxage=<秒>     | 公共缓存服务器响应的最大Age值        |

- 浏览器缓存流程

![](attachments/Pasted%20image%2020240722163544.png)

- 浏览器缓存相关指令

```nginx
# =off
# 通过该指令控制 Expires 和 Cache-Control
# off: 不缓存
# 支持的块：http、server、location
# 缓存 10 days
expires 10d;
# 缓存 1000s
expires 1000;
# 改成负数，Cache-Control 会设置成 no-cache
expires -1;
# Cache-Control 会设置成十年
expires max;
```

- add_header 指令添加头信息

```nginx
# 支持的块：http、server、location
# add_header <name> <value> [always];
add_header Cache_Control no-store;
```

#### 跨域问题

```nginx
add_header Access-Control-Allow-Origin http://192.168.x.x;
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE;
```

#### 静态资源防盗链

- HTTP 头信息的 Referer
	当浏览器向 web 服务器发送请求的时候，一般都会带上 Referer，来告诉服务器该网页是从哪个页面链接过来的
	后台服务器可以根据获取到的这个 Referer 信息来判断是否为自己信任的网站地址，如果是则放行继续访问，如果不是则可以返回 403

```nginx
# 支持的块 server, location
# referer 为空允许访问
valid_referers none;
# referer 为指定 ip 允许访问
valid_referers 192.168.66.6;
# 其他
valid_referers blocked www.google.com;
valid_referers <regex>

# 如果匹配到了 $invalid_referer 自动置 0，否则置 1
# if 的位置：server, location
# 如果发生盗链，直接返回 403
if ($invalid_referer) {
	return 403;
}
# 如果发生盗链，重写地址
if ($invalid_referer) {
	rewrite ^/ /images/滚.jpg break;
}


# 如果 user agent 含有 Safari 字样
if ($http_user_agent ~ Safari) {...}
# 如果 user agent 含有 Safari 字样，不区分大小写
if ($http_user_agent ~* Safari) {...}
# 判断文件是否存在
if (-f $request_filename) {...}
# 判断文件是否不存在
if (!-f $request_filename) {...}
# 判断目录是否存在
if (-d $request_filename) {...}
# 判断目录或文件是否存在
if (-e $request_filename) {...}
# 判断文件是否可执行
if (-x $request_filename) {...}

# block 的位置：server、location、if
# 会终止当前的匹配并把 URI 重定向


# 设置变量
# 位置：server、location、if
set $name hu;
set $age 18;

# Rewrite 的预设变量
# 请求参数
$arg
$query_string
# 用户代理
$http_user_agent
# 用户访问的服务器
$host
# 用户访问的 URI
$uri
$document_uri
# 当前请求对应的 location 的 root，默认指向 nginx 自带的 html 目录
$document_root
# 请求头中的 Content-Length
$content_length
# 请求头中的 Content-Type
$content_type
# 客户端的 Cookie
$http_cookie
# Nginx 服务器对网络连接速率的限制，默认 0（不限制）
$limit_rate
# 客户端的IP地址
$remote_addr
# 客户端与服务端建立连接的端口号
$remote_port
# 客户端的用户名，需要有认证模块才能获取
$remote_user
# 访问协议
$scheme
# 服务端的地址
$server_addr
# 客户端请求到达的服务器的名称
$server_name
# 客户端请求到达服务器的端口号
$server_port
# 客户端请求协议的版本，比如"HTTP/1.1"
$server_protocol
# 发给后端服务器的本地文件资源的名称
$request_body_file
# 客户端的请求方式
$request_method
# 当前请求的资源文件的路径名
$request_filename
# 当前请求的 URI，携带请求参数
$request_uri

# return
return <状态码> <text>;
return <状态码> <URL>;
return <URL>;
```

#### Rewrite

```nginx
# 用于 URL 重写
# URL 重写和重定向不同
# 依赖于 PCRE
# 模块: nginx_http_rewrite_module
# 位置：server、location、if
# rewrite regex replacement [flag]


# /rewrite/urlabc 将重写成谷歌
rewrite ^/rewrite/url\w*$ https://www.google.com;
# /rewrite/testabc 将重写到 location /test
rewrite ^/rewrite/(test)\w*$ /$1;
# last
# 是默认的，好像是我猜的
rewrite ^/rewrite/(test)\w*$ /$1 last;
# break
# 重写的 URI 不切换 location，继续在本 location 执行
rewrite ^/rewrite/(test)\w*$ /$1 break;
# redirect
# 直接重定向到目标
# 返回 302
rewrite ^/rewrite/(test)\w*$ /$1 redirect;
# permanent
# 直接重定向到目标
# 返回 301
rewrite ^/rewrite/(test)\w*$ /$1 redirect;


# rewrite_log
# 用来开启 URL 重写日志
# 开启后，相关日志将以 notice 级别输出到 error_log 指令配置的日志文件汇总
# =off
# 位置 http、server、location、if
rewrite_log on
# 打开后需要配置 error log 的日志级别
error_log logs/error.log notice;


# 访问 www.xxx.com/xxx 与 www.xxx.com/xxx/ 的区别
# www.xxx.com/xxx 会返回 301 并自动重定向到 www.xxx.com/xxx/
# server_name_in_redirect 指令可以决定重定向的地址
# nginx 0.8.48 以前默认值 =on
# 重定向到 http://<server_name>/xxx/
server_name_in_redirect on;
# 重定向到 http://www.xxx.com/xxx/
server_name_in_redirect off;


# 合并目录
# 有利于 SEO
# 将 www.xxx.com/11-22-33-44-55.html 重写到 www.xxx.com/11/22/33/44/55.html
```

#### 正向代理

```nginx
server {
	listen 81;
	# 设置 DNS 服务器，用来解析 prixy_pass 中的域名
	resolver 8.8.8.8;
	location / {
		proxy_pass http://$host$request_uri;
	}
}
```

#### 反向代理

```nginx
# 模块：ngx_http_proxy_module（内置）

# proxy_pass
# 用来设置被代理服务器的地址
# 位置：location
server {
	listen 80;
	server_name localhost;
	location / {
		# 是一样的
		proxy_pass http://192.168.66.66;
		proxy_pass http://192.168.66.66/;
	}
	location /server {
		# 当客户端访问 localhost/server/index.html
		# 变成 localhost/server/index.html
		proxy_pass http://192.168.66.66;
		# 变成 localhost/index.html
		proxy_pass http://192.168.66.66/;
	}
}


# proxy_set_header
# 用于更改客户端的请求头信息
# 位置：http、server、location
proxy_set_header K V;


# proxy_redirect
# 被代理服务器的 ip 不应该暴露
# 用来重置被代理服务器响应头中的 "Location" 和 "Refresh"
# 在代理服务器中配置
# 位置 http、server、location

# 将 proxy_pass 替换为当前 location 的 uri
proxy_redirect default;
# 关闭此功能
proxy_redirect off;
# 自定义替换
proxy_redirect <目标 Location> <要替换的值>;
```

#### 反向代理调优

```nginx
# buffer 和 cache
# buffer: 主要用来调和不同速度的设备
# cache: 主要用来备份，当客户端请求的数据未修改，代理服务器可以返回已缓存的数据

# proxy_buffering
# 用来设置代理服务器的缓冲区
# in http, server, location
# =on
proxy_buffering on;

# proxy_buffers
# 用来指定单个连接从代理服务器读取响应的缓存区的个数和大小
# in http, server, location
# proxy_buffers <number> <size>;
proxy_buffers 8 32k;

# proxy_buffer_size
# 来设置从被代理服务器获取的第一部分响应数据的大小
# 保持与proxy_buffers中的size一致即可，当然也可以更小
# in http, server, location
proxy_buffer_size 16k;

# proxy_busy_buffers_size
# 来限制同时处于BUSY状态的缓冲总大小
# in http, server, location
proxy_busy_buffers_size 64k;

# roxy_temp_path
# 缓冲区存满后，仍未被Nginx服务器完全接受，响应数据就会被临时存放在磁盘文件上，该指令设置文件路径
# path 最多设置 3 层
# in http, server, location
proxy_temp_path <path>;

# proxy_temp_file_write_size
# 用来设置磁盘上缓冲文件的大小
# in http, server, location
proxy_temp_file_write_size 64k;
```

#### ssl

```nginx
# https == http + SSL
# Nginx 要想使用 SSL 需要添加模块 --with-http_ssl_module
# 该模块在编译的过程中需要 OpenSSL 的支持


# 开启 ssl
# =off
ssl on;
# 相当于
server {
	listen 443 ssl;
}


# ssl_certificate：为当前这个虚拟主机指定一个带有 PEM 格式证书的证书
# ssl_certificate_key：指定 PEM secretkey 文件的路径
# 位置：http、server
ssl_certificate <path>;
ssl_certificate_key <path>;


# 用来配置用于SSL会话的缓存
# 位置 http、server
# =none
# off：禁用会话缓存，客户端不得重复使用会话
# none：禁止使用会话缓存，客户端可以重复使用，但是并没有在缓存中存储会话参数
# builtin：内置OpenSSL缓存，仅在一个工作进程中使用
# shared：所有工作进程之间共享缓存，缓存的相关信息用name和size来指定
# ssl_session_cache off | none | [builtin[:size]] [shared:name:size]

# 共享缓存，缓存名称为 SSL，缓存大小为 1M
ssl_session_cache shared:SSL:1m;

# 开启SSL会话功能后，设置客户端能够反复使用储存在缓存中的会话参数时间
# =5m
# 位置：http、server
ssl_session_timeout 5m;


# 指出允许的密码，密码需为OpenSSL支持的格式
# 位置：http、server
# 使用 openssl ciphers 命令查看 openssl 支持的格式
ssl_ciphers HIGH:!aNULL:!MD5


# 是否服务器密码优先客户端密码
# =off
# 位置：http、server
ssl_prefer_server_ciphers on;
```

#### 生成证书

```shell
# 方式一：云服务商购买

# 方式二：使用 openssl 生成，用于测试环境
mkdir /root/cert
cd /root/cert
# 生成 key，des3 是加密算法
openssl genrsa -des3 -out server.key 1024
openssl req -new -key server.key -out server.csr
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key
openss1 x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

#### 负载均衡

- 实现负载均衡的方式

```nginx
# way 1: 用户手动选择（电信下载、联通下载。。。）

# way 2: DNS 轮询
# 因为 DNS 解析的结果会在客户机缓存，所以这种方式可靠性低，简单的轮询也导致负载不够均衡

# way 3: 4/7 层负载均衡
# 4 层指的是 OSI 中的传输层
# 7 层指的是 OSI 中的应用层

# 4 层负载均衡指的是基于 IP + 端口 的负载均衡
# 实现4层负载均衡的方式
# 硬件：F5，BIG-IP 等，很贵
# 软件：LVS，NGinx 等

# 7 层负载均衡指的是基于 虚拟URL 和 主机IP 的负载均衡
# 实现7层负载均衡的方式
# 软件：NGinx，Hayproxy 等

# 四层和七层负载均衡的区别
# 1. 四层负载均衡数据包是在底层就进行了分发，而七层负载均衡数据包则在最顶端进行分发，所以四层负载均衡的效率比七层负载均衡的要高
# 2. 四层负载均衡不识别域名，而七层负载均衡识别域名

# 一般实际上采用 4(LVS)+7(Nginx)层负载均衡
```

##### nginx 七层负载均衡

```nginx
# nginx 的负载均衡是在 nginx 反向代理的基础上把用户的请求根据指定的算法分发到一组【upstream 虚拟服务池】

# upstream
# 用来定义一组服务器，它们可以是监听不同端口的服务器，并且也可以是同时监听 TCP 和Unixsocket 的服务器。服务器可以指定不同的权重，默认为1
# in http
# 假设服务组叫 backend
upstream backend {
	# 负载均衡策略
	# 1. 轮询，默认方式
	# 2. weight，轮询相当于每个服务器 weight=1
	# 3. ip_hash，依据ip分配方式
	# 4. least_conn，依据最少连接数方式
	# 5. url_hash，依据 URL 分配方式
	# 6. fair，依据响应时间方式

	# 设置负载均衡算法
	# ip_hash;
	# uri_hash 写法如下
	# hash $request_uri;
	# fair 可以根据页面大小、加载时间智能地进行负载均衡
	# fair 属于第三方模块 nginx-upstream-fair，需要安装
	# fair;
	# ...
	# server <...> weight=5;

	# 不参与负载均衡
	server <server>:9001 down;
	# 备用的服务器
	server <server1>:9002 backup;
	# 允许3次请求失败，都失败后此服务器不可用 15 秒
	server <server2>:9003 max_fails=3 fail_timeout=8;
	...;
}

# server
# 用来指定后端服务器的名称和一些参数，可以使用域名、IP、端口或者 unixsocket
# server <servername> [paramerters]
# down: 当前的 server 暂时不参与负载均衡，一般对需要停机维护的服务器设置
# backup: 备用服务器，当主服务器不可用时才可用
# max_fails: 允许请求失败的次数
# fail_timeout: 经过 max_fails 失败后，服务暂停时间
# max_conns: 限制最大的接收连接数
# in upstream
server {
	...;
	location / {
		proxy_pass http://backend;
	}
}
```

##### nginx 四层负载均衡

> 需要添加 stream 模块的支持 --with-stream（available after nginx1.9）

```nginx
# 位置和 http 同级
stream {
	upstream redis_backend {
		server <ipa>:<porta>;
		server <ipa1>:<porta1>;
		...;
	}
	upstream tomcat_backend {
		server <ipb>:<portb>;
		server <ipb1>:<portb1>;
		...;
	}

	server {
		listen 81;
		# 不能书写 server_name，不支持
		proxy_pass redis_backend;
	}
	server {
		listen 82;
		proxy_pass tomcat_backend;
	}
}
```

#### 代理缓存

```nginx
# 代理缓存原理
# 1. url 与相关内容 = key
# 2. 对 key 进行 md5 hash
# 2. hash 值就是缓存的存放目录
# 3. 支持设置过期时间也可以使用 purge 命令手动清除缓存

# 代理缓存功能主要在模块 ngx_http_proxy_module

# proxy+cache_path
# http
# 设置缓存文件的存放路径为 proxy_cache
proxy_cache_path /usr/local/proxy_cache;
# 设置 levels
# 2:1 -> 2 层目录，依次取 hash 尾部的 2 位、1 位
proxy_cache_path /usr/local/proxy_cache levels=2:1;
# 2:2:2 -> 3 层目录（最大支持 3 层），依次取 hash 尾部的 2 位、2 位、2 位
proxy_cache_path /usr/local/proxy_cache levels=2:1;
# 为缓存区设置名称 xxx 和大小 200M
proxy_cache_path /usr/local/proxy_cache levels=2:1 keys_zone=xxx:200m;
# 设置缓存 1d 为访问就删除
proxy_cache_path /usr/local/proxy_cache levels=2:1 keys_zone=xxx:200m inactive=1d;
# 设置最大缓存空间，如果缓存空间存满，默认会覆盖缓存时间最长的资源
proxy_cache_path /usr/local/proxy_cache levels=2:1 keys_zone=xxx:200m inactive=1d max_size=20g;

# proxy_cache
# http、server、location
# =off
# 用来开启或关闭代理缓存
# 开启缓存器 xxx
proxy_cache xxx;

# proxy_cache_key
# http、server、location
# 用来设置代理缓存的 key
# =$scheme$proxy_host$request_uri
proxy_cache_key $scheme$proxy_host$request_uri;

# proxy_cache_valid
# 用来对不同返回状态码的 URL 设置不同的缓存时间
# http、server、location
# 可以多次设置，以顺序匹配的第一个为准
# 对于响应状态码是 301 和 302 的数据缓存 10 min
proxy_cache_valid 301 302 10m;
# 对所有状态码的 URL 都缓存 1 min
proxy_cache_valid any 1m;

# proxy_cache_min_uses
# 该用来设置资源被访问多少次后被缓存
# =1
# http、server、location
proxy_cache_min_uses 1;

# proxy_cache_methods
# 设置缓存哪些 HTTP 方法
# http、server、location
# =GET HEAD
# 缓存 GET HEAD 方法
proxy_cache_methods GET HEAD;
# 缓存 GET HEAD POST 方法
proxy_cache_methods GET HEAD POST;

# 变量 $upstream_cache_status 记录的缓存命中状态

# 代理缓存的删除方法 1
# 直接删除整个缓存目录，用于清空所有缓存
# 代理缓存的删除方法 2
# 使用第三方模块 ngx_cache_purge

# 用来定义不将数据进行缓存的条件
# http、server、location
# $cookie_nocache: coockie 中 nocache 对应的 value
# $arg_nocache: 请求参数中 nocache 对应的 value
# $arg_comment: 请求参数中 comment 对应的 value
# 只要以上变量有一个不为空且值不为 0 就满足不缓存条件
proxy_no_cache string $cookie_nocache $arg_nocache $arg_comment;

# 设置不从缓存中获取数据的条件
# http、server、location
proxy_cache_bypass $cookie_nocache $arg_nocache $arg_comment;
# 建议为了不缓存同时使用 proxy_cache_bypass 和 proxy_no_cache
```

#### Keepalived

> Keepalived 软件由 C 编写的，最初是专为 LVS 负载均衡软件设计的，Keepalived 软件主要是通过 VRRP 协议实现高可用功能。

- VRRP 协议

![](attachments/Pasted%20image%2020240727145554.png)

	VRRP（VirtualRouteRedundancyProtocol）协议，翻译过来为虚拟路由冗余协议。VRRP 协议将两台或多台路由器设备虚拟成一个设备，对外提供虚拟路由器 IP，而在路由器组内部，实际拥有这个对外 IP 的路由器如果工作正常的话就是MASTER，MASTER 实现针对虚拟路由器 IP 的各种网络功能。Master 路由和 Backup 路由之间有一个心跳检测，Master 会定时告知Backup 自己的状态，如果在指定的时间内，Backup 没有接收到这个通知内容，Backup 就会替代 Master 成为新的 Master。

- nginx 高可用性方案

![](attachments/Pasted%20image%2020240727150257.png)

- keepalived.conf

```keepalived
-------- nginx 服务器 1 上的 keepalived.conf
global_defs {
	# 主从服务器切换后，使用邮件通知
	notification_email {
		employee1@gmail.com
		employee2@gmail.com
	}
	# 设置邮件的发出者
	notification_email_from hu@gmail.com
	router_id <本主机标识符，使用主机名即可>
}

# 这个配置是指定运行一个脚本，为了当 nginx 服务器宕机时，可以自动杀掉 keepalived 来让 master 转移到另一台 nginx 服务器上
vrrp_srcipt ck_nginx {
	script "<脚本路径>"
	# 每两秒执行一次
	interval 2
	# weight -20
}

vrrp_instance VI_1 {
	state MASTER
	interface <使用网卡名称 eth0 即可>
	virtual_router_id <使用默认即可，一个 vrrp 环境中这个 ip 都使用相同的>
	priority 100  # 竞争 master 的优先级
	virtual_ipaddress {
		192.168.6.6  # 虚拟 ip
	}
	track_script {
		ck_nginx
	}
}

-------- nginx 服务器 2 上的 keepalived.conf
global_defs {
	notification_email {
		employee1@gmail.com
		employee2@gmail.com
	}
	notification_email_from hu@gmail.com
	router_id <本主机标识符，使用主机名即可>
}
vrrp_instance VI_1 {
	state BACKUP
	interface <使用网卡名称 eth0 即可>
	virtual_router_id <使用默认即可，一个 vrrp 环境中这个 ip 都使用相同的>
	priority 80  # 竞争 master 的优先级
	virtual_ipaddress {
		192.168.6.6  # 虚拟 ip
	}
}

```

- ck_nginx.sh

```shell
#! /bin/bash
# num：nginx 相关进程数量
num=`ps -C nginx --no-head | wc -l`
if [ $num -eq 0 ]; then
	/usr/local/nginx/sbin/nginx
	sleep 2
	if [ `ps -C nginx --no-head | wc -l` -eq 0 ]; then
		killall keepalived
	fi
fi
```

#### 目录列表输出

> 使用模块 ngx_http_autoindex_module 实现

```nginx
# autoindex：启用或禁用目录列表输出
# =off
# http、server、location
autoindex on;

# autoindex_exact_size
# 指定是否在目录列表展示文件的详细大小
# on，显示出文件的确切大小，单位是bytes
# off，显示出文件的大概大小，单位是kB或者MB或者GB
# =on
# http、server、location
autoindex_exact_size off;

# autoindex_format
# 设置目录列表的格式
# =html
# http、server、location
# autoindex_format html | xml | json |jsonp;

# autoindex_localtime
# 对 HTML 格式是否在目录列表上显示时间
# =off
# http、server、location
autoindex_localtime on;
```

#### 用户认证

> 使用模块 ngx_http_auth_basic_module 实现

```nginx
# auth_basic
# 使用 "HTTP基本认证" 协议启用用户名和密码的验证
# 开启后，服务端会返回 401，指定的字符串会返回到客户端，给用户以提示信息，但是不同的浏览器对内容的展示不一致
# auth_basic string | off;
# =off
# http,server,location,limit_except
auth_basic "please input your auth";

# auth_basic_user_file
# 指定用户名和密码所在文件
# 该文件可以使用 htpasswd 自动生成
# http,server,location,limit_except
auth_basic_user_file <文件路径>;
```

- htpasswd

```shell
# 安装以使用 htpasswd
apt -y install httpd-tools

# 创建一个文件并添加一个用户
htpasswd -c <文件的路径> <username>
# 指定文件新增用户
htpasswd -b <文件的路径> <username> <password>
# 指定文件删除用户
htpasswd -D <文件的路径> <username>
# 验证一个用户
htpasswd -v <文件的路径> <username>
```

#### openresty

```nginx
# set_by_lua
# 需求：根据参数提供的性别显示 x 先生或女士或只显示 x
location /getByGender{
	default_type 'text/html';
	# 使用 lua 设置变量
	set_by_lua $param "
		local uri_args = ngx.req.get_uri_args()
		local name = uri_args['name']
		local gender = uri_args['gender']
		if gender=='1' then
			return name..'先生'
		else if gender=='0' then
			return name..'女士'
		else
			return name
		end
	";
	charset utf-8;
	return 200 $param;
}
```

- 操作 redis 案例

> 使用 lua-resty-redis 库

```nginx
location /testRedis{
	default_type 'text/html';
	content_by_lua_block {
		-- 引入 resty.redis 模块
		local redis = require 'resty.redis'
		
		-- 创建一个 redis 连接对象
		local redis_obj = redis:new()
		
		-- 设置超时时间
		redis_obj:set_timeout(1000)
		
		-- 获取连接
		local ok, err = redis_obj:connect("192.168.6.6", 6379)
		if not ok then
			ngx.say("failed to connect redis...")
			return
		end
		
		-- 往 redis 中存储数据
		ok, err = redis_obj:set("username", "rose")
		if not ok then
			ngx.say("failed to set username")
			return
		end
		
		-- 从 redis 中获取数据
		local res, err = redis_obj:get("username")
		ngx.say(res)
		
		-- 关闭连接
		redis_obj:close()
	}
}
```

- 操作 mysql 案例

> 使用 lua-resty-mysql 库

```nginx
# global
init_by_lua_block {
	-- 引入 cjson 用于将 table 类型的数据转化成 json
	cjson = require 'cjson'
}

# server
location /testMySQL{
	default_type 'text/html';
	content_by_lua_block {
		-- 引入 resty.mysql 模块
		local mysql = require 'resty.mysql'
		
		-- 创建一个 mysql 连接对象
		local db = mysql:new()
		
		-- 建立连接
		local ok, err = db:connect({
			host="192.168.6.6",
			port=3306,
			user="root",
			password="123456",
			database="XBase"
		})
		if not ok then
			ngx.say("failed to connect mysql...")
			return
		end
		
		-- 设置超时时间
		db:set_timeout(1000)
		
		-- 查询数据库
		-- db:send_query("<SQL statements>")
		-- 读取返回的结果
		-- local res, err, errcode, sqlstate = db:read_result()

		-- 可以直接使用 query，是 send_query 和 read_result 的组合
		local res, err, errcode, sqlstate = db:query("SQL 语句")

		-- 返回数据
		ngx.say(cjson.encode(res))
		
		-- 关闭连接
		db:close()
	}
}
```

- redis 缓存预热案例

```nginx
# global
init_by_lua_block {
	mysql = require 'resty.mysql'
	redis = require 'resty.redis'
	cjson = require 'resty.cjson'
}

# server
location / {
	default_type "text/html";
	content_by_lua_block {
		local args = ngx.req.get_uri_args()["username"]
		local db = mysql:new()
		local ok,err = db:connect{
			host="192.168.6.6",
			port=3306,
			user="root",
			password="123456",
			database="XBase"
		}
		if not ok then
			ngx.say('failed connection to mysql: ', err)
			return
		end
		db.set_timeout(1000)
		
		local sql = ''
		if not args then
			sql = 'select * from users'
		else
			sql = "select * from users where username = '"..args.."'"

		local res, err, errcode, sqlstate = db.query(sql)
		if not res then
			ngx.say('failed to query from mysql: ', err)
			return
		end

		local redis = redis:new()
		ok, err = redis:connect("192.168.8.8", 6379)
		if not ok then
			ngx.say('redis connection failed: ', err)
			return
		end

		redis:set_timeout(1000)

		for i, v in ipairs(res) do
			redis:set('user_'..v.username, cjson.encode(v))
		end
		ngx.say('success')
		redis:close()
		db:close()

		
	}
}
```

#### 添加模块

```shell
# 1. 更名 nginx 二进制文件，做备份用
cd /.../sbin
mv nginx nginx.old

# 2. 进入安装目录
cd /.../nginx/core/...

# 3. 清空之前的编译内容
make clean

# 4. 配置参数
./configure --with-http_gzip_static_module

# 5. 编译
make

# 6. 将生成的二进制文件移动到 sbin 中
mv /.../nginx /.../sbin

# 7. 执行命令，自动完成模块的添加操作
make upgrade
```
