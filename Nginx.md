# Nginx应用指南

## Nginx基本概述
Nginx是一个开源且高性能、可靠的HTTP中间件、代理服务。开源：直接获取源代码高性能：支持海量并发

常⻅的HTTP服务:
- HTTPD -> Apache基⾦会
- IIS -> 微软
- GWS -> Google
- openrestry  基于 Nginx 与 Lua 的高性能 Web 平台
- tengline -> 淘宝基于Nginx开发   http://tengine.taobao.org/

Nginx应⽤场景
- 静态处理
- 反向代理
- 负载均衡
- 资源缓存
- 安全防护 (nginx+lua)
- 访问限制
- 访问认证

## Nginx优秀特性
Nginx基于IO多路复⽤, IO复⽤解决的是并发性的问题，Socket作为复⽤
![image](https://user-images.githubusercontent.com/34932312/80306251-a4826380-87f4-11ea-85c3-d4aae93f2ae8.png)

IO多路复⽤(主动上报)
- 多个描述符的I/O操作都能在⼀个线程内并发交替地顺序完成，这就叫I/O多路复⽤，这⾥的 "复 ⽤"指的是复⽤同⼀个线程。

IO多路复⽤的实现⽅式有select、poll、Epoll

![image](https://user-images.githubusercontent.com/34932312/80326543-b8b57780-886b-11ea-9acc-2fe1cf96ce71.png)
select缺点：
- 1.能够监视文件描述符的数量存在最大限制
- 2.线性遍历扫描效率低下

epoll模型
- 每当FD就绪，采⽤系统的回调函数之间将fd放⼊,效率更⾼。
- 最⼤连接⽆限制

这块IO多路复用讲的非常不清晰！建议看一下其他视频


轻量级
- 功能模块少
- 代码模块化 

CPU亲和
将CPU核⼼和Nginx⼯作进程绑定⽅式，把每个worker进程固定在⼀个cpu上执⾏，减少切换cpu 的 cache miss，获得更好的性能。
![image](https://user-images.githubusercontent.com/34932312/80326788-793b5b00-886c-11ea-9618-b3efc05f9b10.png)

sendﬁle

传统⽂件传输, 在实现上其实是⽐较复杂的, 其具体流程细节如下：
```
1.调⽤read函数，⽂件数据被复制到内核缓冲

2.read函数返回，⽂件数据从内核缓冲区复制到⽤户缓冲区

3.write函数调⽤，将⽂件数据从⽤户缓冲区复制到内核与socket相关的缓冲区。

4.数据从socket缓冲区复制到相关协议引擎。 传统⽂件传输数据实际上是经过了四次复制操作: 硬盘—>内核buf—>⽤户buf—>socket缓冲区(内核)—>协议引擎
```
也就是说传统的⽂件传输需要经过多次上下⽂的切换(内核态用户态转换)才能完成拷⻉贝或读取, 效率不⾼。
![image](https://user-images.githubusercontent.com/34932312/80389033-2d74ca00-88dd-11ea-90e5-8684d185306f.png)

sendﬁle⽂件传输是在内核中操作完成的, 函数直接在两个⽂件描述符之间传递数据, 从⽽避免了内核缓冲区数据和⽤户缓冲区数据之间的拷贝, 操作效率很⾼, 被称之为`零拷⻉`。
```
1.系统调⽤sendﬁle函数通过 DMA 把硬盘数据拷⻉到 kernel buﬀer，

2.数据被 kernel 直接拷贝到另外⼀个与 socket 相关的 kernel buﬀer。

3.DMA把数据从kernel buﬀer直接拷⻉给协议栈。
```
这⾥没有⽤户空间和内核空间之间的切换，在内核中直接完成了从⼀个buﬀer到另⼀个 buﬀer的拷贝。
![image](https://user-images.githubusercontent.com/34932312/80389482-b68c0100-88dd-11ea-92a9-a5c8a3a0d870.png)


## Nginx快速安装
Mainline version 开发版 Stable version 稳定版 Legacy version 历史版本

http://nginx.org/

vagrant https://blog.csdn.net/weixin_39686421/article/details/83029674

vagrant ubuntu 创建 root用户并使用ssh连接  https://blog.csdn.net/u013218443/article/details/80968630

基础环境准备:
```
//确认系统⽹络
[root@Nginx	~]#	ping	baidu.com

// 检查防火墙状态
// systemctl status firewalld

//关闭firewalld
[root@Nginx	~]#	systemctl	stop	firewalld
[root@Nginx	~]#	systemctl	disable	firewalld

//临时关闭selinux
[root@Nginx	~]#	setenforce	0

//初始化基本⽬录
[root@Nginx	~]#	mkdir /soft/{code,logs,package/src} -p
[root@Nginx	~]# tree /soft/

//基本安装包
yum install -y gcc gcc-c++ autoconf pcre pcre-devel make auto make wget httpd-tools vim tree

http://nginx.org/en/linux_packages.html#stable

//配置Nginx官⽅Yum源
[root@Nginx	~]#	vim	/etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1

//安装Nginx
[root@Nginx	~]#	yum	install	nginx -y

//查看Nginx当前版本
[root@Nginx	~]#	nginx -v
nginx	version:	nginx/1.12.2

// 查看nginx编译参数
nginx -V
nginx version: nginx/1.18.0
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'

查看nginx装了哪些东西
[root@localhost etc]# rpm -ql nginx
/etc/logrotate.d/nginx
/etc/nginx
/etc/nginx/conf.d
/etc/nginx/conf.d/default.conf
/etc/nginx/fastcgi_params
/etc/nginx/koi-utf
/etc/nginx/koi-win
/etc/nginx/mime.types
/etc/nginx/modules
/etc/nginx/nginx.conf
/etc/nginx/scgi_params
/etc/nginx/uwsgi_params
/etc/nginx/win-utf
/etc/sysconfig/nginx
/etc/sysconfig/nginx-debug
/usr/lib/systemd/system/nginx-debug.service
/usr/lib/systemd/system/nginx.service
/usr/lib64/nginx
/usr/lib64/nginx/modules
/usr/libexec/initscripts/legacy-actions/nginx
/usr/libexec/initscripts/legacy-actions/nginx/check-reload
/usr/libexec/initscripts/legacy-actions/nginx/upgrade
/usr/sbin/nginx
/usr/sbin/nginx-debug
/usr/share/doc/nginx-1.18.0
/usr/share/doc/nginx-1.18.0/COPYRIGHT
/usr/share/man/man8/nginx.8.gz
/usr/share/nginx
/usr/share/nginx/html
/usr/share/nginx/html/50x.html
/usr/share/nginx/html/index.html
/var/cache/nginx
/var/log/nginx
```

## Nginx安装⽬录
为了让⼤家更清晰的了解 Nginx 软件的全貌，有必要介绍下 Nginx 安装后整体的⽬录结构及⽂件功能。
```
[root@Nginx	~]#	rpm	-ql	nginx
```

如下表格对 Nginx 安装⽬录做详细概述:
|路径|类型|作用|
|---|---|---|
|/etc/nginx<br>/etc/nginx/nginx.conf<br>/etc/nginx/conf.d<br>/etc/nginx/conf.d/default.conf|配置⽂件|Nginx主配置文件|
|/etc/nginx/fastcgi_params<br>/etc/nginx/scgi_params/etc/nginx/uwsgi_params|配置⽂件|Cgi、Fastcgi、Uwcgi配置⽂件|
|/etc/nginx/win-utf<br>/etc/nginx/koi-utf<br>/etc/nginx/koi-win|配置⽂件|Nginx编码转换映射⽂件|
|/etc/nginx/mime.types|配置⽂件|http协议的Content-Type与扩展名|
|/usr/lib/systemd/system/nginx.service|配置⽂件|配置系统守护进程管理器|
|/etc/logrotate.d/nginx|配置⽂件|Nginx⽇志轮询,⽇志切割|
|/usr/sbin/nginx<br>/usr/sbin/nginx-debug/命令|Nginx终端管理命令|
|/etc/nginx/modules<br>/usr/lib64/nginx<br>/usr/lib64/nginx/modules|⽬录|Nginx模块⽬录|
|/usr/share/nginx<br>/usr/share/nginx/html<br>/usr/share/nginx/html/50x.html<br>/usr/share/nginx/html/index.html|⽬录|Nginx默认站点⽬录|
|/usr/share/doc/nginx-1.12.2<br>/usr/share/man/man8/nginx.8.gz|⽬录|Nginx的帮助⼿册|
|/var/cache/nginx|⽬录|Nginx的缓存⽬录|
|/var/log/nginx|⽬录|Nginx的⽇志⽬录|

























