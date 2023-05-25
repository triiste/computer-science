



### Nginx技术介绍

**本文介绍nginx相关技术，Nginx 是一种网络服务器软件，它可以用于网络管理中的一些方面。在网络管理中，一些常见的任务包括网络配置、性能监控、安全管理、流量管理等，Nginx 在这些任务中发挥一定的作用。**

#### 一.Nginx基础概念

##### 1.什么是nginx?

​		Nginx是一款轻量级的[Web](https://baike.baidu.com/item/Web/150564?fromModule=lemma_inlink) 服务器/[反向代理](https://baike.baidu.com/item/反向代理/7793488?fromModule=lemma_inlink)服务器及[电子邮件](https://baike.baidu.com/item/电子邮件/111106?fromModule=lemma_inlink)（IMAP/[POP3](https://baike.baidu.com/item/POP3/175122?fromModule=lemma_inlink)）代理服务器，在[BSD](https://baike.baidu.com/item/BSD/3794498?fromModule=lemma_inlink)-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。Nginx以其出色的性能和高并发处理能力而闻名。它采用事件驱动、非阻塞的架构，可以同时处理大量的并发连接，而不会像传统的多线程服务器那样消耗过多的系统资源。这使得Nginx成为一个理想的解决方案，能够在高负载环境下提供快速、可靠的服务。

##### 2.反向代理

（1）正向代理

正向代理 是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。

<img src="C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20230524222308345.png" alt="image-20230524222308345" style="zoom:50%;" />

（2）反向代理

反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器的IP地址。

<img src="C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20230524223115964.png" alt="image-20230524223115964" style="zoom: 80%;" />

##### 3.负载均衡

​	客户端发送多个请求到服务器，服务器处理请求，有一些可能要与数据库进行交互，服务器处理完毕后，再将结果返回给客户端。

​		这种架构模式对于早期的系统相对单一，并发请求相对较少的情况下是比较适合的，成本也低了。但是随着信息数量的不断增长，访问量和数据量的飞速增长，以及系统业务的复杂度增加，这种架构会造成服务器相应客户端的请求日益缓慢，并发量特别大的时候，还容易造成服务器直接崩溃。很明显这是由于服务器性能的瓶颈造成的问题，那么如何解决这种情况呢？

​		我们首先想到的可能是升级服务器的配置，比如提高CPU执行频率，加大内存等提高机器的物理性能来解决此问题，但是我们知道摩尔定律的日益失效，硬件的性能提升已经不能满足日益提升的需求了。最明显的一个例子，天猫双十一当天，某个热销商品的瞬时访问量是极其庞大的，那么类似上面的系统架构，将机器都增加到现有的顶级物理配置，都是不能够满足需求的。那么怎么办呢？

​		上面的分析我们去掉了增加服务器物理配置来解决问题的办法，也就是说纵向解决问题的办法行不通了，那么横向增加服务器的数量呢？这时候集群的概念产生了，单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。

![image-20230524224818224](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20230524224818224.png)

##### 4.动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度，降低原来单个服务器的压力。

![image-20230525102133469](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20230525102133469.png)

#### 二.nginx的安装

- 进入nginx官网，下载 http://nginx.org/

- 安装nginx相关依赖

~~~shell
第一步，安装pcre
	wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz
	解压文件 tar -xzvf 路径
	pcre主目录执行命令 ./configure 
	完成后、回到pcre目录下执行 make && make install
	查看版本 pcre-config --version
第二步，安装其他依赖
	zlib openssl
	yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
第三步 安装nginx
	解压nginx-xx.tar.zg包
	进入解压目录，执行./configure
	make && make install
安装成功之后，在usr多出来一个文件夹local/nginx,在nginx有sbin有启动脚本

启动nginx
cd /usr/local/nginx/sbin
./nginx
ps -ef | grep nginx 查看nginx进程
~~~

~~~shell
打开nginx.conf文件
listen默认端口为80
linux默认端口不能访问有防火墙
#查看开发的端口号
firewall-cmd --list-all
#设置开发的端口号
firewall-cmd --add-service=http --premanent
sudo firewall-cmd --add-port=80/tcp --permanent #开放新的端口
#重启防火墙
firewall-cmd --reload
~~~

#### 三.nginx操作的常用命令

~~~shell
#1、使用nginx操作命令前提条件：
#必须进入nginx的目录
/usr/local/nginx/sbin
#2、查看nginx的版本
./nginx -v
#3、启动nginx
./nginx
#4、关闭nginx
./nginx -s stop
#5、重新加载nginx
./nginx -s reload
~~~

#### 四.Nginx配置文件

~~~shell
1.配置文件的位置
vim /usr/local/nginx/conf/nginx.conf
~~~

~~~nginx

  2 #user  nobody;
  3 worker_processes  1;
  4 
  5 #error_log  logs/error.log;
  6 #error_log  logs/error.log  notice;
  7 #error_log  logs/error.log  info;                                                                                 8 
  9 #pid        logs/nginx.pid;
 10 
 11 
 12 events {
 13     worker_connections  1024;
 14 }
 15 
 16 
 17 http {
 18     include       mime.types;
 19     default_type  application/octet-stream;
 20 
 21     #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
 22     #                  '$status $body_bytes_sent "$http_referer" '
 23     #                  '"$http_user_agent" "$http_x_forwarded_for"';
 24 
 25     #access_log  logs/access.log  main;
 26 
 27     sendfile        on;
 28     #tcp_nopush     on;
 29 
 30     #keepalive_timeout  0;
 31     keepalive_timeout  65;
 32 
 33     #gzip  on;       
 34         
 35     server {
 36         listen       80;
 37         server_name  localhost;
 38 
 39         #charset koi8-r;
 40 >---charset utf-8; 
 41         #access_log  logs/host.access.log  main;
 42             
 43         location / {
 44             root   /home/user/server/dist;
 45             index  index.html index.htm;
 46 >---    try_files $uri $uri/ /index.html;
 47         }
 48 
 49 >---location /api{
 50 >---    proxy_pass http://localhost:9090/;
 51 >---}
52                                                                                                                                                                                                                                                                          
 53         #error_page  404              /404.html;
 54 
 55         # redirect server error pages to the static page /50x.html
 56         #
 57         error_page 405 = 200@405 ;
 58         location @405{
 59             proxy_method GET;
 60             proxy_pass http://182.92.125.156:9090;
 61         }
 62 
 63         # proxy the PHP scripts to Apache listening on 127.0.0.1:80
 64         #
 65         #location ~ \.php$ {
 66         #    proxy_pass   http://127.0.0.1;
 67         #}
 68 
 69         # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
 70         #
 71         #location ~ \.php$ {
 72         #    root           html;
 73         #    fastcgi_pass   127.0.0.1:9000;
 74         #    fastcgi_index  index.php;
 75         #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
 76         #    include        fastcgi_params;
 77         #}
 78 
 79         # deny access to .htaccess files, if Apache's document root
 80         # concurs with nginx's one
 81         #
 82         #location ~ /\.ht {
 83         #    deny  all;
 84         #}
 85     }
 86     server {
 87             listen       81;
 88             server_name  localhost2;
 89 
 90             #charset koi8-r;
 91     >---charset utf-8;
 92             #access_log  logs/host.access.log  main;
 93 
 94             location / {
 95                 root   /home/user/cf1014/vue/dist;
 96                 index  index.html index.htm;
 97     >---    try_files $uri $uri/ /index.html;
98 
 99             }
100 
101     >---location /api/ {
102     >---    proxy_pass http://localhost:9096/;
103     >---}
104 
105             error_page 405 = 200@405 ;
106             location @405{
107                 proxy_method GET;
108                 proxy_pass http://182.92.125.156:9096;
109             }
110 
111         }
112 
113 
114 
115 
116 
117 
118 
119     # another virtual host using mix of IP-, name-, and port-based configuration
120     #
121     #server {
122     #    listen       8000;
123     #    listen       somename:8080;
124     #    server_name  somename  alias  another.alias;
125 
126     #    location / {
127     #        root   html;
128     #        index  index.html index.htm;
129     #    }
130     #}
131 
132 
133     # HTTPS server
134     #
135     #server {
136     #    listen       443 ssl;
137     #    server_name  localhost;
138 
139     #    ssl_certificate      cert.pem;
140     #    ssl_certificate_key  cert.key;
141 
142     #    ssl_session_cache    shared:SSL:1m;
143     #    ssl_session_timeout  5m;
144 
145     #    ssl_ciphers  HIGH:!aNULL:!MD5;
146     #    ssl_prefer_server_ciphers  on;
147 
148     #    location / {
149     #        root   html;
150     #        index  index.html index.htm;
151     #    }
152     #}
153 
154 }     
~~~



Nginx 配置文件有三部分组成

##### 第一部分 全局块

​		从配置文件开始到 events 块之间的内容，主要会设置一些影响 Nginx 服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的worker process 数、进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。

​		比如上面第一行配置的

~~~nginx
worker_processes  1;
~~~

这是 Nginx 服务器并发处理服务的关键配置，work_processes值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约。

##### 第二部分 events块

~~~nginx
 12 events {
 13     worker_connections  1024;
 14 }
~~~

~~~
events块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多work process 下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个word process 可以同时支持的最大连接数等。
上述例子就表示每个work process支持的最大连接数为1024
~~~

##### 第三部分 http块

~~~
这部分的配置对Nginx的性能影响较大，在实际中应该灵活配置。
这算是Nginx服务器配置中最频繁的部分，代理、缓存和日志等绝大多数功能和第三方模块的配置都在这里。
需要注意的是：http块也可以包括http全局块、server块。
~~~

- http全局块配置的指令包括文件引入、MIME-TYPE定义、日志自定义、连接超时时间、单链接请求数上限等。

~~~nginx
 18     include       mime.types;
 19     default_type  application/octet-stream;
 20 
 21     #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
 22     #                  '$status $body_bytes_sent "$http_referer" '
 23     #                  '"$http_user_agent" "$http_x_forwarded_for"';
 24 
 25     #access_log  logs/access.log  main;
 26 
 27     sendfile        on;
 28     #tcp_nopush     on;
 29 
 30     #keepalive_timeout  0;
 31     keepalive_timeout  65;
 32 
 33     #gzip  on;   
~~~



- server块

  这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。

  每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。

  而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

~~~nginx
 86     server {
 87             listen       81;
 88             server_name  localhost2;
 89 
 90             #charset koi8-r;
 91     >---charset utf-8;
 92             #access_log  logs/host.access.log  main;
 93 
 94             location / {
 95                 root   /home/user/cf1014/vue/dist;
 96                 index  index.html index.htm;
 97     >---    try_files $uri $uri/ /index.html;
98 
 99             }
100 
101     >---location /api/ {
102     >---    proxy_pass http://localhost:9096/;
103     >---}
104 
105             error_page 405 = 200@405 ;
106             location @405{
107                 proxy_method GET;
108                 proxy_pass http://182.92.125.156:9096;
109             }
110 
111         }
~~~

#### 五.Nginx配置实例-反向代理

##### Nginx 反向代理实例1

~~~
1、实现效果
（1）打开浏览器，在浏览器地址栏输入地址www.123.com,跳转到linux系统tomcat主页面中

2、准备工作
（1）在linux系统安装tomcat，使用默认端口8080
		tomcat安装文件放到linux系统中，解压
		进入tomcat的bin目录中，./startup.sh启动tomcat服务器
（2）对外开放访问的端口		
	firewall-cmd --add-port=8080/tcp --permanent
	firewall-cmd --reload

查看已经开放的端口号
firewall-cmd --list-all	
~~~

**访问过程**

![image-20230525121129641](C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20230525121129641.png)

修改server内容

~~~nginx
 86     server {
 87             listen       81;
 88             server_name  192.168.17.129; #改成主机ip
 89 
 90             #charset koi8-r;
 91     >---charset utf-8;
 92             #access_log  logs/host.access.log  main;
 93 
 94             location / { #转发的一个路径       		
 95                 proxy_pass http://localhost:8080/;#访问192.168.17.129 默认跳转http://localhost:8080/
 96                 index  index.html index.htm;
 97     >---    try_files $uri $uri/ /index.html;
 98 
 99             }
 100      }
~~~

##### Nginx反向代理实例2

~~~
1.实现效果
	访问 http://127.0.0.1:9091/edu/ 直接跳转到 127.0.0.1：8080/edu/
	访问 http://127.0.0.1:9091/vod/ 直接跳转到 127.0.0.1：8082/vod/
2.准备工作
	（1）准备两个tomcat服务器，一个8080端口，一个8081端口
	（2）准备文件夹和测试页面

~~~

**具体配置**
	找到nginx配置文件，进行反向代理配置

<img src="C:\Users\wcf\AppData\Roaming\Typora\typora-user-images\image-20230525131203576.png" alt="image-20230525131203576" style="zoom:50%;" />

- =：用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
- ~：用于表示uri包含正则表达式，并且区分大小写

~~~
对外开放访问的端口		
	firewall-cmd --add-port=9001/tcp --permanent
	firewall-cmd --reload
	firewall-cmd --list-all	
~~~

#### 六.Nginx配置实例-负载均衡

~~~
1.实现效果
	（1）浏览器地址栏输入地址 http://192.168.17.129/edu/a.html,负载均衡效果，平均8080和8081端口中
2.准备工作
	（1）准备两台tomcat服务器，一台8080，一台8081
	（2）在两台tomcat里面webapps目录中，创建名称是edu文件夹，在edu文件夹中创建页面a.html，用于测试	
~~~

**在nginx的配置文件中进行负载均衡的配置**

~~~nginx
#在http块加以下内容
upstraeam myserver{
    server 192.168.17.129:8080;
    server 192.168.17.129:8081;
}
#在server模块
 location / { #转发的一个路径       		
       proxy_pass http://myserver;
       root html;
       index  index.html index.htm;
}
~~~

**Nginx 分配服务器策略**

~~~
1.轮询（默认）
	每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
2.weight
	weight代表权重，默认为1，权重越高，被分配的客户端越多
	指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。例如：
	upstream server_pool{
	server 192.168.5.21 weight=10
	server 192.168.5.21 weight=10
	} 
3.ip_hash
	每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session问题。例如
	upstream server _pool{
	ip_hash
	server 192.168.5.21:80
	server 192.168.5.22:80
	}
4.fair
	按后端服务器的响应时间来分配请求，响应时间短的优先分配
	upstream server _pool{
	server 192.168.5.21:80
	server 192.168.5.22:80
	fair
	}
~~~

#### 七.Nginx配置实例-动静分离

1.动静分离

~~~
    Nginx 动静分离简单来说就是把动态跟静态请求分开，不能理解成只是单纯的把动态页面和静态页面物理分离。严格意义上说应该是动态请求跟静态请求分开，可以理解成使用 Nginx处理静态页面， Tomcat 处理动态页面。
    动静分离从目前实现角度来讲大致分为两种，
  	  一种是纯粹把静态文件独立成单独的域名，放在独立的服务器上，也是目前主流推崇的方案；
  	  一种就是动态跟静态文件混合在一起发布，通过 nginx 来分开。通过 location 指定不同的后缀名实现不同的请求转发。
    通过 expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，不建议使用 Expires 来缓存），我这里设置 3d，表示在这 3 天之内访问这个 URL，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304，如果有修改，则直接从服务器重新下载，返回状态码 200。
~~~

2.准备工作

​	在Linux系统中准备静态资源，用于进行访问

3.具体配置

 ~~~nginx
 86     server {
 87             listen       81;
 88             server_name  localhost2;
 89 
 90             #charset koi8-r;
 91          charset utf-8;
 92             #access_log  logs/host.access.log  main;
 93 
 94             location / {
 95                 root   /home/user/cf1014/vue/dist; #访问的静态资源路径
 96                 index  index.html index.htm;
 97         try_files $uri $uri/ /index.html;
98 
 99             }
100 
101      location /api/ {
102        proxy_pass http://localhost:9096/;
103      }
104 
105             error_page 405 = 200@405 ;
106             location @405{
107                 proxy_method GET;
108                 proxy_pass http://182.92.125.156:9096;
109             }
110 
111         }
 ~~~

#### 八.Nginx配置高可用集群

![graphic](data:application/octet-stream;base64,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)

~~~
 准备工作：
 （1）需要两台服务器 192.168.17.129 192.168.17.131
 （2）在两台服务器安装nginx
  (3) 在两台服务器安装keepalived 
  		yum install keepalived -y
安装之后，在etc里面生成目录keepalived,有文件keepalived.conf,修改配置文件，启动keepalived,进行访问测试
~~~

#### 九.Nginx原理

在Nginx中，"master"和"worker"是两个重要的概念，它们在Nginx的进程管理和并发处理中扮演着不同的角色。下面对它们进行详细解释：

1. Master进程：
   - Master进程是Nginx的主进程，负责管理工作进程（worker进程）的创建、终止和信号处理。
   - 当Nginx启动时，Master进程首先读取配置文件，加载并解析配置指令。
   - Master进程会创建并维护一个或多个工作进程。
   - 它接收并分派来自客户端的连接请求给工作进程处理。
   - Master进程负责监听系统信号，例如重新加载配置、优雅地关闭服务器等。
   - 如果发生工作进程的异常退出，Master进程会自动重新创建新的工作进程来替代。
2. Worker进程：
   - Worker进程是由Master进程创建的子进程，用于处理实际的客户端请求。
   - 每个Worker进程都是相互独立的，它们之间不共享状态。
   - 每个Worker进程都维护一个事件驱动的循环，通过事件模型处理来自客户端的连接、请求和响应。
   - Worker进程通过与客户端进行交互，处理HTTP请求、执行反向代理、提供静态文件、缓存等功能。
   - 每个Worker进程都可以处理多个并发连接，并且通过事件驱动的方式处理请求，提高了性能和并发处理能力。
   - Worker进程之间可以通过共享内存进行通信，以提高性能和效率，但它们不会共享具体的请求处理信息。

总的来说，Master进程在Nginx中负责管理工作进程的创建和终止，以及信号处理和配置文件加载。Worker进程负责处理实际的客户端请求和执行相应的功能。Master进程和Worker进程之间通过合作来实现高性能的并发处理和请求分发。

#### 十.总结

总结一下，Nginx是一款高性能、开源的Web服务器软件，具有以下主要特点和功能：

1. 高性能和高并发处理能力：Nginx采用事件驱动、非阻塞的架构，能够处理大量的并发连接和请求，适用于高流量的Web应用和网站。
2. 反向代理和负载均衡：作为反向代理服务器，Nginx可以将传入的请求转发给后端的应用服务器，实现负载均衡和高可用性。
3. 静态文件服务和缓存：Nginx能够高效地提供静态文件的服务，减轻后端服务器的负载，并支持HTTP缓存来加速响应。
4. SSL/TLS支持和安全性：Nginx支持配置SSL证书和密码套件，提供安全的通信通道，保护数据的安全性和完整性。
5. 灵活的配置和可扩展性：Nginx使用文本配置文件进行配置，可以根据需求定义服务器块、代理规则、缓存设置等，具有很高的灵活性和可扩展性。
6. 轻量级和资源占用低：Nginx具有较小的内存占用和CPU负载，能够在资源有限的环境中运行，并提高系统的效率和性能。

Nginx在现代互联网架构中具有重要的意义，它作为一个高性能的Web服务器和反向代理，能够提供快速、可靠和安全的服务。其广泛应用于许多大型网站和Web应用程序中，并成为构建可靠基础设施的关键组件之一。







