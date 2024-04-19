## 安装
### 一、环境调试确认
#### 1、确认系统网络
```
ping www.baidu.com
```
#### 2、确认 yum 可用
```
yum list | grep gcc
```
#### 3、确认关闭 iptables 规则
```
iptables -L // 查看
iptables -F // 关闭
iptables -t nat -L
iptables -t nat -F
```
#### 4、确认停用 selinux
```
getenforce // 查看
setenforce 0 // 关闭
```
#### 5、安装基本库
```
yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake
```
#### 6、安装基本工具
```
yum -y install wget httpd-tools vim
```
#### 7、初始化目录
```
cd /opt;mkdir app download logs work backup
```
#### 8、安装 nginx
第一种方式
```
yum install epel-release
yum install nginx
```
第二种方式
- 进入官网 https://nginx.org/，点击右侧 download，点击下方 Pre-Built Packages =>  stable and mainline，找到 RHEL/CentOS 复制 [nginx-stable]
- 执行 cd /etc/yum.repos.d && touch nginx.repo，将复制的内容粘贴到 nginx.repo 中，并将 $releasever 修改为 7
- 执行
```
yum list | grep nginx // 查看 nginx 的最新版本
yum install nginx // 安装最新版本
nginx -v // 查看 nginx 版本
nginx -V // 查看 nginx 编译的参数
rpm -ql nginx // 查看 nginx 安装目录
```
## 配置
### 1、request、response
- request - 包括请求行、请求头部、请求数据
- 包括状态行、消息报头、相应正文
### 2、access_log、error.log
log_format
Content: http
### 3、nginx 变量
- HTTP 请求变量 - arg_PARAMERTER、HTTP_HEADER、sent_http_HEADER
- 内置变量 - Nginx内置的
- 自定义变量 - 自己定义
### 4、内置模块
#### http_stub_status_module
```
Syntax: stub_status;
Context: server、location
```
#### random_index_module
```
Syntax: random_index on|off;
Context: location
```
#### http_sub_module
```
Syntax: sub_filter string replacement;
Context: http、server、location

Syntax: sub_filter_last_modified on|off;
Context: http、server、location

Syntax: sub_filter_once on|off
Context: http、server、location
```
#### 连接限制
##### 链接限制 - limit_conn_zone
```
Syntax: limit_conn_zone key zone=name:size;
Context: http

Syntax: limit_conn zone number;
Context: http、server、location
```
##### 请求限制 - limit_req_zone
```
Syntax: limit_req_zone key zone=name:size rate=rate;
Context: http

Syntax: limit_req zone number [burst=number] [nodelay];
Context: http、server、location
```
#### 访问控制
##### 基于 ip 的访问控制 - http_access_module
```
Syntax: allow address|CIDR|unix:|all;
Context: http、server、location、limit_except
```
```
Syntax: deny address|CIDR|unix:|all;
Context: http、server、location、limit_except
```
##### 基于用户的信任登录 - http_auth_basic_module
```
Syntax: auth_basic string|off;
Context: http、server、location、limit_except
```
```
Syntax: auth_basic_user_file file;
Context: http、server、location、limit_except
```
### 5、静态资源
#### 文件读取
```
Syntax: sendfile on|off;
Context: http、server、location、if in location
```
#### tcp_nopush，sendfile 开启的情况下，提高网络包的传输效率
```
Syntax: tcp_nopush on|off;
Context: http、server、location
```
#### tcp_nodelay，keepalive 连接下，提高网络包的传输实时性
```
Syntax: tcp_nodelay on|off;
Context: http、server、location
```
#### gzip 压缩
```
Syntax: gzip on|off;
Context: http、server、location、if in location
```
```
Syntax: gzip_comp_level level;
Context: http、server、location
```
#### http_gzip_static_module - 预读 gzip 功能，会先找 gz 文件
```
Syntax:	gzip_static on | off | always;
Context: http, server, location
```
### 6、浏览器缓存
#### 强缓存
```
Expires(http1.0)、Cache-Control(http1.1)
Syntax: expires [modified] time;
        expires epoch|max|off;
Context: http、server、location、if in location
```
#### 协商缓存
```
If-Modified-Since/Last-Modified
If-None-Match/Etag
```
### 7、跨站访问
```
Syntax: add_header name value [always];
Context: http、server、location、if in location
```
### 8、防盗链
```
Syntax: valid_referers none|blocked|server_names|string...;
Context: server、location
```
### 9、代理
```
Syntax: proxy_pass URL;
Context: location、if in location、limit_except
```
### 10、负载均衡
```
Syntax: upstream name {...}
Context: http
```
### 11、代理缓存
```
Syntax: proxy_cache_path ...;
Context: http
```
```
Syntax: proxy_cache zone | off;
Context: http、server、location
```
```
Syntax: proxy_cache_valid [code...] time;
Context: http、server、location
```
```
Syntax: proxy_cache_key string;
Context: http、server、location
```
```
// 让页面不缓存
Syntax: proxy_no_cache string;
Context: http、server、location
```
### 12、Rewrite
```
Syntax: rewrite regex replacement [flag];
Context: server、location、if
// 举例
rewrite ^(.*)$ /pages/maintain.html break;
```
### 13、secure_link_module
```
Syntax: secure_link expression;
Context: http、server、location
```
```
Syntax: secure_link_md5 expression;
Context: http、server、location
```
### 14、geoip_module
```
yum install nginx-module-geoip
```
### 15、HTTPS、CA 证书
```
Syntax: listen 443 ssl;
Context: server
```
```
Syntax: ssl_certificate file;
Context: http、server
```
```
Syntax: ssl_certificate_key file;
Context: http、server
```
### 16、其他
```
Nginx: 413 Request Entity Too Large // 用户上传文件限制 client_max_body_size
502 bad gateway // 后端服务无响应
504 Gateway Time-out // 后端服务执行超时
```