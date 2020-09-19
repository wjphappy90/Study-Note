##课程标题

为并发量高的优质网站量身定制高可用的并发解决方案nginx+keepalived


##课程引导语
在这个互联网飞速发展的时代，人们已经离不开网络，其中网购最为常见。在家网购，上班路上网购，吃饭也购物，下班还网购，2017年双十一天猫支付峰值达到了25.6万笔/秒。热点网站中频繁出现的大量并发如何去解决？

那么在此次课程中，大家就会领略到，使用目前市面上处理并发能力非常强悍的nginx及keepalived如何快速的搭建一个处理高并发并的高可用的服务。


## 课程介绍 ##
 本课程介绍了目前处理并发能力非常强悍的开源软件nginx快速入门及使用，介绍nginx+tomcat集群处理并发解决方案，带大家认识虚拟路由，了解虚拟路由的工作流程并安装keepalived，实现nginx+keepalived主备配置，达到nginx高可用的效果。

## 课程知识点与大纲 ##
 - nginx keepalived tomcat
 - nginx 介绍
 - nginx 快速入门
 - nginx+tomcat集群
 - keepalived 介绍
 - keepalived安装
 - keepalived+nginx 搭建高可用服务 

## Nginx介绍 ##
Nginx ("engine x") 是一个高性能的 HTTP 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器 。 Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的Rambler.ru 站点开发的，它已经在该站点运行超过四年多了。Igor 将源代码以类BSD许可证的形式发布。自Nginx 发布以来，Nginx 已经因为它的稳定性、丰富的功能集、 示例配置文件和低系统资源的消耗而闻名了。目前国内各大门户网站已经部署了Nginx，如新浪、网易、腾讯。
## Nginx下载和安装 ##
Nginx官网http://nginx.org/ 

![](https://i.imgur.com/zF2qMn7.png)

Nginx目前已经更新到了1.13.10版本，我们可以直接到官网下载，由于外面服务器大多都使用linux环境作为服务器，所以我们也弄一台linux环境的虚拟机。

虚拟机我们已经装好了，IP：192.168.211.129，由于nginx是用C语言写的,而且还支持地址栏重写等功能，所以我们需要安装一下相关的依赖包。

    yum install gcc-c++
    yum install -y pcre pcre-devel
    yum install -y zlib zlib-devel
    yum install -y openssl openssl-devel



安装流程如下：

1. 我们把刚才从官网下载的Nginx，文件上传到centos的/usr/local/server目录去。
2. 紧接着我们在server目录下创建nginx目录 把压缩文件解压，进入解压的文件夹。
3. 首先配置nginx安装信息./configure
4. 执行编译和安装make && make install

安装流程如下：

    nginx-1.13.10.tar.gz
    [root@localhost server]# tar -xf nginx-1.13.10.tar.gz
    [root@localhost server]# mkdir nginx
    [root@localhost server]# cd nginx-1.13.10
    [root@localhost nginx-1.13.10]# ./configure --prefix=/usr/local/server/nginx
	[root@localhost nginx-1.13.10]# make && make install
    

上面./configure后面的一些常用参数配置参考如下

    –prefix
    指定部署根目录，默认是/usr/local/nginx.此设置会更改其他配置目录的相对路径
    –sbin-path
    可执行文件的路径，默认为/sbin/nginx
    –conf-path
    配置文件的路径，默认为/conf/nginx.conf
    –pid-path
    pid文件的存放路径，默认存放在/logs/nginx.pid，是一个存放nginx的master进程ID的纯文本文件，刚安装的时候不会生成，nginx启动的时候会自动生成。
    –http-log-path
    access日志存放位置，每个http的请求在结束的时候都会访问的日志。
    –with-ld-opt
    加入第三方链接时需要的参数。编译之后nginx最终的可执行二进制文件是由编译后的目标文件和一些第三方的库链接生成的。如果想要将某个库链接到nginx中，就需要指定–with-ld-opt=目标库名-目标库路径
    –with-debug
    将nginx需要打印debug调试级别日志的代码编译进nginx，这样才可以通过修改配置文件将调试日志打印出来，便于定位服务问题 


安装第三方模块

     ./configure --prefix=/usr/local/server/nginx --add-module=/usr/local/server/nginx_module/echo-nginx-module-0.61 --with-debug


这时候Nginx已经安全完成，我们进入/usr/local/server/nginx目录查看

    [root@localhost nginx-1.13.10]# cd ../nginx
    [root@localhost nginx]# ls
    conf  html  logs  sbin
    [root@localhost nginx]# 


Nginx安装完成后不要忘了防火墙开放80端口

	[root@localhost sbin]# vi /etc/sysconfig/iptables
    # Firewall configuration written by system-config-firewall
    # Manual customization of this file is not recommended.
    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
    -A INPUT -j REJECT --reject-with icmp-host-prohibited
    -A FORWARD -j REJECT --reject-with icmp-host-prohibited
    COMMIT
	[root@localhost sbin]# service iptables restart
	iptables: Setting chains to policy ACCEPT: filter          [  OK  ]
	iptables: Flushing firewall rules:                         [  OK  ]
	iptables: Unloading modules:                               [  OK  ]
	iptables: Applying firewall rules:                         [  OK  ]
	[root@localhost sbin]# 

访问http://192.168.211.129

![](https://i.imgur.com/8RdTpwI.png)


## Nginx常用命令 ##

	nginx常用命令  
		nginx -c /usr/local/server/nginx/conf/nginx.conf  启动nginx(windows下start nginx);  
		nginx -s quit   停止ngix  
		nginx -s reload 重新载入nginx(当配置信息发生修改时)  
		nginx -v 查看版本  
		nginx -t 查看nginx的配置文件的目录  
		nginx -h 查看帮助信息  
  

## 模块介绍 ##
### 主模块 ###
####基本指令 ####

##### daemon #####
语法：daemon on | off<br/>
默认值：on<br/>
是否以守护进程的方式运行nginx，守护进程是指脱离终端并且在后头运行的进程，关闭守护进程执行的方式可以让我们方便调试nginx

##### master_process #####
语法：on | of 默认on<br/>
是否以master/worker方式进行工作，在实际的环境中 nginx是以一个master进程管理多个worker进程的方式运行的，关闭后 nginx就不会fork出worker子进程来处理请求，而是用master进程自身来处理请求worker_processes number; 默认1，在master/worker运行方式下 worker进程的数目，一般情况下用户要配置与CPU内核数相等的worker进程。


##### error_log #####
语法：error_log file [ debug | info | notice | warn | error | crit ] 


##### include #####
语法：include file | *
默认值：none
你可以包含一些其他的配置文件来完成你想要的功能。<br/>
0.4.4版本以后，include指令已经能够支持文件通配符：

    include vhosts/*.conf;

##### pid #####
语法：pid file<br/>
默认值：编译时指定<br/>

    pid /var/log/nginx.pid;

指定pid文件，可以使用kill命令来发送相关信号，例如你如果想重新读取配置文件，则可以使用：

    kill -HUP `cat /var/log/nginx.pid`



##### user #####
语法：user user [group]<br/>
默认值：nobody nobody<br/>
如果主进程以root运行，Nginx将会调用setuid()/setgid()来设置用户/组，如果没有指定组，那么将使用与用户名相同的组，默认情况下会使用nobody用户与nobody组（或者nogroup），或者在编译时指定的--user=USER和--group=GROUP的值。


#### worker_processes ####
语法：worker_processes number 默认值：1

由于以下几点原因，Nginx可能需要运行不止一个进程
·使用了SMP（对称多处理技术）。
·当服务器在磁盘I/O出现瓶颈时为了减少响应时间。
·当使用select()/poll()限制了每个进程的最大连接数时。
在事件模块这一章中我们将使用worker_processes和worker_connections来计算理论最大连接数（max_clients）：
max_clients = worker_processes * worker_connections 



### Nginx处理HTTP的核心功能模块 ###
####基本指令 ####
##### alias #####
语法：alias file-path|directory-path;<br/>
默认值：no<br/>
使用字段：location
这个指令指定一个路径使用某个某个，注意它可能类似于root，但是document root没有改变，请求只是使用了别名目录的文件。

    location  /i/ {
      alias  /spool/w3/images/;
    }

上个例子总，请求"/i/top.gif"将返回这个文件: "/spool/w3/images/top.gif"。
Alias同样可以用于带正则表达式的location，如：

    location ~ ^/download/(.*)$ {
      alias /home/website/files/$1;
    }




##### keepalive_timeout #####
语法：keepalive_timeout [ time ] [ time ]<br/>
默认值：keepalive_timeout 75<br/>
使用字段：http, server, location<br/>
参数的第一个值指定了客户端与服务器长连接的超时时间，超过这个时间，服务器将关闭连接。<br/>
参数的第二个值（可选）指定了应答头中Keep-Alive: timeout=time的time值，这个值可以使一些浏览器知道什么时候关闭连接，以便服务器不用重复关闭，如果不指定这个参数，nginx不会在应答头中发送Keep-Alive信息。（但这并不是指怎样将一个连接“Keep-Alive”）<br/>
参数的这两个值可以不相同<br/>
下面列出了一些服务器如何处理包含Keep-Alive的应答头：<br/>
·MSIE和Opera将Keep-Alive: timeout=N头忽略。<br/>
·MSIE保持一个连接大约60-65秒，然后发送一个TCP RST。<br/>
·Opera将一直保持一个连接处于活动状态。<br/>
·Mozilla将一个连接在N的基础上增加大约1-10秒。
·Konqueror保持一个连接大约N秒。<br/>



##### listen #####
语法(0.7.x)：listen address:port [ default [ backlog=num | rcvbuf=size | sndbuf=size | accept_filter=filter | deferred | bind | ssl ] ]<br/>
语法(0.8.x)：listen address:port [ default_server [ backlog=num | rcvbuf=size | sndbuf=size | accept_filter=filter | deferred | bind | ssl ] ]<br/>
默认值：listen 80<br/>
使用字段：server<br/>
listen指令指定了server{...}字段中可以被访问到的ip地址及端口号，可以只指定一个ip，一个端口，或者一个可解析的服务器名。<br/>

    listen 127.0.0.1:8000;
    listen 127.0.0.1;
    listen 8000;
    listen *:8000;
    listen localhost:8000;


##### location #####
语法：location [=|~|~*|^~|@] /uri/ { ... }<br/>
默认值：no<br/>
使用字段：server<br/>
这个参数根据URI的不同需求来进行配置，可以使用字符串与正则表达式匹配，如果要使用正则表达式，你必须指定下列前缀：<br/>
1、~* 不区分大小写。<br/>
2、~ 区分大小写。<br/>

    location  = / {
      # 只匹配 / 的查询.
      [ configuration A ]
    }
    location  / {
      # 匹配任何以 / 开始的查询，但是正则表达式与一些较长的字符串将被首先匹配。
      [ configuration B ]
    }
    location ^~ /images/ {
      # 匹配任何以 /images/ 开始的查询并且停止搜索，不检查正则表达式。
      [ configuration C ]
    }
    location ~* \.(gif|jpg|jpeg)$ {
      # 匹配任何以gif, jpg, or jpeg结尾的文件，但是所有 /images/ 目录的请求将在Configuration C中处理。
      [ configuration D ]
    }




##### resolver_timeout #####
语法：resolver_timeout time<br/>
默认值：30s<br/>
使用字段：http, server, location<br/>
解析超时时间。如：<br/>
resolver_timeout 5s;<br/>


##### root #####
语法：root path<br/>
默认值：root html<br/>
使用字段：http, server, location ,location中的if字段
请求到达后的文件根目录。<br/>
下例中：

    location  /i/ {
      root  /spool/w3;
    }

如果请求"/i/top.gif"文件，nginx将转到"/spool/w3/i/top.gif"文件。你可以在参数中使用变量。<br/>
注意：在请求中root会添加这个location到它的值后面，即"/i/top.gif"并不会请求"/spool/w3/top.gif"文件，如果要实现上述类似于apache alias的功能，可以使用alias指令。


##### server #####
语法：server {...}<br/>
默认值：no<br/>
使用字段：http<br/>
server字段包含虚拟主机的配置。<br/>
没有明确的机制来分开基于域名（请求中的主机头）和基于IP的虚拟主机。<br/>
可以通过listen指令来指定必须连接到这个server块的所有地址和端口，并且在server_name指令中可以指定所有的域名。

##### server_name #####
语法：server_name name [... ]<br/>
默认值：server_name hostname<br/>
使用字段：server<br/>
这个指令有两个作用：<br/>
·将HTTP请求的主机头与在nginx配置文件中的server{...}字段中指定的参数进行匹配，并且找出第一个匹配结果。这就是如何定义虚拟主机的方法，域名遵循下述优先级规则：<br/>
1、完整匹配的名称。<br/>
2、名称开始于一个文件通配符：*.example.com。<br/>
3、名称结束于一个文件通配符：www.example.*。<br/>
4、使用正则表达式的名称。<br/>
如果没有匹配的结果，nginx配置文件将安装以下优先级使用[#server server { ... }]字段：<br/>
1、listen指令被标记为default的server字段。<br/>
2、第一个出现listen（或者默认的listen 80）的server字段。<br/>
·如果server_name_in_redirect被设置，这个指令将用于设置HTTP重定向的服务器名。<br/>
例：<br/>

    server {
      server_name   example.com  www.example.com;
    }

第一个名称为服务器的基本名称，默认名称为机器的hostname。
当然，可以使用文件通配符：

    server {
      server_name   example.com  *.example.com  www.example.*;
    }

上述例子中的前两个名称可以合并为一个：

    server {
      server_name  .example.com;
    }

同样可以使用正则表达式。名称前面加“~”：

    server {
      server_name   www.example.com   ~^www\d+\.example\.com$;
    }


### HTTP负载均衡模块（HTTP Upstream） ###
#### 摘要 ####
这个模块为后端的服务器提供简单的负载均衡（轮询（round-robin）和连接IP（client IP））
如下例：

    upstream backend  {
      server backend1.example.com weight=5;
      server backend2.example.com:8080;
      server unix:/tmp/backend3;
    }
 
	server {
	  location / {
	    proxy_pass  http://backend;
	  }
	}


#### 指令 ####
##### ip_hash #####
语法：ip_hash<br/>
默认值：none<br/>
使用字段：upstream<br/>
这个指令将基于客户端连接的IP地址来分发请求。<br/>
哈希的关键字是客户端的C类网络地址，这个功能将保证这个客户端请求总是被转发到一台服务器上，但是如果这台服务器不可用，那么请求将转发到另外的服务器上，这将保证某个客户端有很大概率总是连接到一台服务器。<br/>
无法将权重（weight）与ip_hash联合使用来分发连接。如果有某台服务器不可用，你必须标记其为“down”，如下例:<br/>

    upstream backend {
      ip_hash;
      server   backend1.example.com;
      server   backend2.example.com;
      server   backend3.example.com  down;
      server   backend4.example.com;
    }

##### server #####
语法：server name [parameters]<br/>
默认值：none<br/>
使用字段：upstream<br/>
指定后端服务器的名称和一些参数，可以使用域名，IP，端口，或者unix socket。如果指定为域名，则首先将其解析为IP。<br/>
·weight = NUMBER - 设置服务器权重，默认为1。<br/>
·max_fails = NUMBER - 在一定时间内（这个时间在fail_timeout参数中设置）检查这个服务器是否可用时产生的最多失败请求数，默认为1，将其设置为0可以关闭检查，这些错误在proxy_next_upstream或fastcgi_next_upstream（404错误不会使max_fails增加）中定义。<br/>
·fail_timeout = TIME - 在这个时间内产生了max_fails所设置大小的失败尝试连接请求后这个服务器可能不可用，同样它指定了服务器不可用的时间（在下一次尝试连接请求发起之前），默认为10秒，fail_timeout与前端响应时间没有直接关系，不过可以使用proxy_connect_timeout和proxy_read_timeout来控制。<br/>
·down - 标记服务器处于离线状态，通常和ip_hash一起使用。<br/>
·backup - (0.6.7或更高)如果所有的非备份服务器都宕机或繁忙，则使用本服务器（无法和ip_hash指令搭配使用）。<br/>
示例配置

    upstream  backend  {
      server   backend1.example.comweight=5;
      server   127.0.0.1:8080  max_fails=3  fail_timeout=30s;
      server   unix:/tmp/backend3;
    }

注意：如果你只使用一台上游服务器，nginx将设置一个内置变量为1，即max_fails和fail_timeout参数不会被处理。<br/>
结果：如果nginx不能连接到上游，请求将丢失。
解决：使用多台上游服务器。<br/>


##### upstream #####
语法：upstream name { ... }<br/>
默认值：none<br/>
使用字段：http<br/>
这个字段设置一群服务器，可以将这个字段放在proxy_pass和fastcgi_pass指令中作为一个单独的实体，它们可以可以是监听不同端口的服务器，并且也可以是同时监听TCP和Unix socket的服务器。
服务器可以指定不同的权重，默认为1。
示例配置<br/>

    upstream backend {
      server backend1.example.com weight=5;
      server 127.0.0.1:8080   max_fails=3  fail_timeout=30s;
      server unix:/tmp/backend3;
    }


### HTTP访问控制模块（HTTP Access） ###

#### 摘要 ####
这个模块提供简单的基于主机的访问控制。<br/>
ngx_http_access_module这个模块可以详细的检查客户端IP，并且按顺序执行第一条匹配的规则。<br/>
如下例：

    location / {
      deny192.168.1.1;
      allow   192.168.1.0/24;
      allow   10.1.1.0/16;
      denyall;
    }

上面的例子中仅允许192.168.1.0/24和10.1.1.0/16网络段访问，但192.168.1.1是个例外。<br/>
如果要实施很多复杂的规则，那么最好使用GeoIP module模块。

#### 指令 ####
##### allow #####
语法：allow [ address | CIDR | all ]<br/>
默认值：no<br/>
使用字段：http, server, location, limit_except
指令指定了允许访问的IP或网络段。

##### deny #####
语法：deny [ address | CIDR | all ]<br/>
默认值：no<br/>
使用字段：http, server, location, limit_except
指令指定了拒绝访问的IP或网络段。<br/>
·提示和技巧<br/>
HttpAccess模块可以和error_page指令搭配使用来重定向一个未经验证的访问请求。<br/>

	error_page  403  http://example.com/forbidden.html;
    location / {
      deny192.168.1.1;
      allow   192.168.1.0/24;
      allow   10.1.1.0/16;
      denyall;
    }



## Nginx+tomcat实现集群 ##
当我们网站并发量高的时候，一台tomcat无法承受大量并发，可以考虑Nginx+Tomcat集群来实现。咱们这就做一个集群演示。

### 配置说明 ###
![](https://i.imgur.com/rMiFftn.jpg)

我们这里准备3台tomcat，端口分别是8081、8082、8083，针对同一个域名，每次用Nginx实现不同的转发，分别在每个tomcat的webapps目录下创建ROOT目录，并创建index.html，分别在html的body里标记1/2/3以示区分。


### Nginx搭建图片服务器 ###
针对任何站点，几乎都要访问图片，而一个网页里面几乎有好些张图片，这时候会占据大量tomcat连接，造成大量并发，我们可以通过Nginx配置直接访问硬盘里的图片，绕开tomcat。

我们在D盘创建一个nginx_images/images目录，然后在images目录放入一些图片，再在nginx的nginx.conf配置里配置一个虚拟机来访问。

	server {
        listen       80;
        server_name  localhost;

		#所有带有images访问的路径直接取D盘:/nginx_images目录下查找
        location / {
            root D:/nginx_images;
        }
  	 }



## keepalived+nginx 集群解决单点故障 ##
再牛逼的软件我们也不能保证它一定不挂，为了防止Nginx挂了导致整个服务无法使用的灾难发生，我们这里可以考虑使用Keepalived+Nginx集群实现高可用。

### keepalived介绍 ###
Keepalived 是一种高性能的服务器高可用或热备解决方案，Keepalived 可以用来防止服务器单点故障的发生，通过配合 Nginx 可以实现 web 前端服务的高可用。<br/>
Keepalived 以 VRRP 协议为实现基础，用 VRRP 协议来实现高可用性(HA)。VRRP(Virtual Router Redundancy Protocol)协议是用于实现路由器冗余的协议，VRRP 协议将两台或多台路由器设备虚拟成一个设备，对外提供虚拟路由器 IP(一个或多个)，而在路由器组内部，如果实际拥有这个对外 IP 的路由器如果工作正常的话就是 MASTER，或者是通过算法选举产生，MASTER 实现针对虚拟路由器 IP 的各种网络功能，如 ARP 请求，ICMP，以及数据的转发等；其他设备不拥有该虚拟 IP，状态是 BACKUP，除了接收 MASTER 的VRRP 状态通告信息外，不执行对外的网络功能。当主机失效时，BACKUP 将接管原先 MASTER 的网络功能。<br/>
VRRP 协议使用多播数据来传输 VRRP 数据，VRRP 数据使用特殊的虚拟源 MAC 地址发送数据而不是自身网卡的 MAC 地址，VRRP 运行时只有 MASTER 路由器定时发送 VRRP 通告信息，表示 MASTER 工作正常以及虚拟路由器 IP(组)，BACKUP 只接收 VRRP 数据，不发送数据，如果一定时间内没有接收到 MASTER 的通告信息，各 BACKUP 将宣告自己成为 MASTER，发送通告信息，重新进行 MASTER 选举状态。


### 方案介绍 ###

	
		VIP          |     IP           |       主机名     |      主从
	                 | 192.168.211.129  |       keep129   |     master
	 192.168.211.131 |------------------|-----------------|--------------
	                 | 192.168.211.130  |      keep130    |    backup


### Nginx安装 ###
在129和130虚拟机上安装nginx，安装过程参考前面学的Nginx。

### keepalived安装 ###
将文件上传到服务器，然后解压安装

    # cd /usr/local/server
	# tar -zxvf keepalived-1.2.18.tar.gz
	# cd keepalived-1.2.18
	# ./configure --prefix=/usr/local/server/keepalived
	# make && make install

将 keepalived 安装成 Linux 系统服务<br/>
因为没有使用 keepalived 的默认路径安装（默认是/usr/local）,安装完成之后，需要做一些工作复制默认配置文件到默认路径

    # mkdir /etc/keepalived
    # cp /usr/local/server/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/
    复制 keepalived 服务脚本到默认的地址
    # cp /usr/local/server/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/
    # cp /usr/local/server/keepalived/etc/sysconfig/keepalived /etc/sysconfig/
    # ln -s /usr/local/sbin/keepalived /usr/sbin/
    # ln -s /usr/local/server/keepalived/sbin/keepalived /sbin/
    设置 keepalived 服务开机启动
    # chkconfig keepalived on



### 配置主节点 ###
找到129注解keepalived的配置文件keepalived.conf

	global_defs {
	   router_id keep129;
	}
	
	vrrp_script chk_nginx {
	 script "/etc/keepalived/nginx_check.sh"
	 interval 2
	 weight -20
	}
	
	
	vrrp_instance VI_1 {
	    state MASTER
	    interface eth0
	    virtual_router_id 129
	    mcast_src_ip 192.168.211.129
	    priority 100
	    nopreempt
	    advert_int 1
	    authentication {
	        auth_type PASS
	        auth_pass 1111
	    }
	    
	    track_script {
		 chk_nginx
	    }
	    virtual_ipaddress {
	        192.168.211.131
	    }
	}


### 配置从节点 ###
找到130注解keepalived的配置文件keepalived.conf

	global_defs {
	   router_id keep130
	}
	
	vrrp_script chk_nginx {
	 script "/etc/keepalived/nginx_check.sh"
	 interval 2
	 weight -20
	}
	
	vrrp_instance VI_1 {
	    state BACKUP
	    interface eth2
	    virtual_router_id 130
	    priority 90
	    mcast_src_ip 192.168.211.130
	    advert_int 1
	    authentication {
	        auth_type PASS
	        auth_pass 1111
	    }
	    track_script {
	 	chk_nginx
	    }
	    virtual_ipaddress {
	        192.168.211.131
	    }
	}


### Nginx检查脚本 ###
在/etc/keepalived目录下创建nginx_check.sh文件

	#!/bin/bash
	A=`ps -C nginx –no-header |wc -l`
	if [ $A -eq 0 ];then
	    /usr/local/server/nginx/sbin/nginx
	    sleep 2
	    if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
	        killall keepalived
	    fi
	fi



### 配置说明 ###
	global_defs {
	 ## keepalived 自带的邮件提醒需要开启 sendmail 服务。建议用独立的监控或第三方 SMTP 
	 router_id keep130 ## 标识本节点的字条串，通常为 hostname
	}
	## keepalived 会定时执行脚本并对脚本执行的结果进行分析，动态调整 vrrp_instance 的优先级。如果
	脚本执行结果为 0，并且 weight 配置的值大于 0，则优先级相应的增加。如果脚本执行结果非 0，并且 weight
	配置的值小于 0，则优先级相应的减少。其他情况，维持原本配置的优先级，即配置文件中 priority 对应
	的值。
	vrrp_script chk_nginx {
	 script "/etc/keepalived/nginx_check.sh" ## 检测 nginx 状态的脚本路径
	 interval 2 ## 检测时间间隔
	 weight -20 ## 如果条件成立，权重-20
	}
	## 定义虚拟路由，VI_1 为虚拟路由的标示符，自己定义名称
	vrrp_instance VI_1 {
	 state MASTER ## 主节点为 MASTER，对应的备份节点为 BACKUP
	 interface eth1 ## 绑定虚拟 IP 的网络接口，与本机 IP 地址所在的网络接口相同，我的是 eth1
	 virtual_router_id 130 ## 虚拟路由的 ID 号，两个节点设置必须一样，可选 IP 最后一段使用, 相
	同的 VRID 为一个组，他将决定多播的 MAC 地址
	 mcast_src_ip 192.168.211.130 ## 本机 IP 地址
	 priority 100 ## 节点优先级，值范围 0-254，MASTER 要比 BACKUP 高
	nopreempt ## 优先级高的设置 nopreempt 解决异常恢复后再次抢占的问题
	advert_int 1 ## 组播信息发送间隔，两个节点设置必须一样，默认 1s
	## 设置验证信息，两个节点必须一致
	authentication {
	 auth_type PASS
	 auth_pass 1111 ## 真实生产，按需求对应该过来
	}
	## 将 track_script 块加入 instance 配置块
	 track_script {
	 chk_nginx ## 执行 Nginx 监控的服务
	}
	## 虚拟 IP 池, 两个节点设置必须一样
	 virtual_ipaddress {
	 	192.168.199.131 ## 虚拟 ip，可以定义多个
	 }
	}