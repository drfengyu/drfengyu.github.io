---
title: Nginx配置详解及设置代理服务器
categories:
  - Web服务
tags:
  - Nginx
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: ec296788
date: 2022-05-12 15:45:38
pic:
---

nginx配置详解及设置代理服务器
1. nginx简单介绍
Nginx是⼀款⾃由的、开源的、⾼性能的HTTP服务器和反向代理服务器；同时也是⼀个IMAP、POP3、SMTP代理服务器；Nginx可以作
为⼀个HTTP服务器进⾏⽹站的发布处理，另外Nginx可以作为反向代理进⾏负载均衡的实现。通常我们主要使⽤nginx作为服务代理和负载
均衡。本⽂主要介绍如何使⽤nginx作为服务代理的。
<!--more-->
2. nginx安装
[https://nginx.org/](https://nginx.org/)
版本介绍
![](1.png)
Mainline version 处于开发阶段的版本
Stable version 属于比较稳定的版本,建议使用该版本
Legacy versions 一些旧的版本
下载后进行解压(Windows版本和Linux版本的目录结果都是一样的)
![](2.png)
conf 主要的配置文件
nginx.exe 启动文件
cmd命令行都可以启动nginx服务
3.nginx配置文件详解
Nginx服务代理和负载均衡都可以通过修改conf/nginx.conf文件进行配置
3.1nginx.conf文件配置属性详解
通过记事本或者其他软件打开时的内容如下所示(#号表示注释)
为了方便阅读,我直接在代码旁对各个属性进行解释
```bash
#定义Nginx运⾏的⽤户和⽤户组
user www www;
#nginx进程数，建议设置为等于CPU总核⼼数。
worker_processes 8;
#全局错误⽇志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /usr/local/nginx/logs/error.log info;
#进程pid⽂件
pid /usr/local/nginx/logs/nginx.pid;
#指定进程可以打开的最⼤描述符：数⽬
#⼯作模式与连接数上限
#这个指令是指当⼀个nginx进程打开的最多⽂件描述符数⽬，理论值应该是最多打开⽂件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均
匀，所以最好与ulimit -n 的值保持⼀致。
#现在在linux 2.6内核下开启⽂件打开数为65535，worker_rlimit_nofile就相应应该填写65535。
#这是因为nginx调度时分配请求到进程并不是那么的均衡，所以假如填写10240，总并发量达到3-4万时就有进程可能超过10240了，这时会返回502错误。
worker_rlimit_nofile 65535;
events
{
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型
    #是Linux 2.6以上版本内核中的⾼性能⽹络I/O模型，linux建议epoll，如果跑在FreeBSD上⾯，就⽤kqueue模型。
    #补充说明：
    #与apache相类，nginx针对不同的操作系统，有不同的事件模型
    #A）标准事件模型
    #Select、poll属于标准事件模型，如果当前系统不存在更有效的⽅法，nginx会选择select或poll
    #B）⾼效事件模型
    #Kqueue：使⽤于FreeBSD 4.1+, OpenBSD 2.9+, NetBSD 2.0 和 MacOS X.使⽤双处理器的MacOS X系统使⽤kqueue可能会造成内核崩溃。
    #Epoll：使⽤于Linux内核2.6版本及以后的系统。
    #/dev/poll：使⽤于Solaris 7 11/99+，HP/UX 11.22+ (eventport)，IRIX 6.5.15+ 和 Tru64 UNIX 5.1A+。
    #Eventport：使⽤于Solaris 10。为了防⽌出现内核崩溃的问题，有必要安装安全补丁。
    use epoll;
    #单个进程最⼤连接数（最⼤连接数=连接数*进程数）
    #根据硬件调整，和前⾯⼯作进程配合起来⽤，尽量⼤，但是别把cpu跑到100%就⾏。每个进程允许的最多连接数，理论上每台nginx服务器的最⼤连接数为
。
    worker_connections 65535;
    #keepalive超时时间。
    keepalive_timeout 60;
    #客户端请求头部的缓冲区⼤⼩。这个可以根据你的系统分页⼤⼩来设置，⼀般⼀个请求头的⼤⼩不会超过1k，不过由于⼀般系统分页都要⼤于1k，所以这⾥
设置为分页⼤⼩。
    #分页⼤⼩可以⽤命令getconf PAGESIZE 取得。
    #[root@web001 ~]# getconf PAGESIZE
    #4096
    #但也有client_header_buffer_size超过4k的情况，但是client_header_buffer_size该值必须设置为“系统分页⼤⼩”的整倍数。
    client_header_buffer_size 4k;
    #这个将为打开⽂件指定缓存，默认是没有启⽤的，max指定缓存数量，建议和打开⽂件数⼀致，inactive是指经过多长时间⽂件没被请求后删除缓存。
open_file_cache max=65535 inactive=60s;
    #这个是指多长时间检查⼀次缓存的有效信息。
    #语法:open_file_cache_valid time 默认值:open_file_cache_valid 60 使⽤字段:http, server, location 这个指令指定了何时需要检查open_file_cache中缓存项
⽬的有效信息.
    open_file_cache_valid 80s;
    #open_file_cache指令中的inactive参数时间内⽂件的最少使⽤次数，如果超过这个数字，⽂件描述符⼀直是在缓存中打开的，如上例，如果有⼀个⽂件在ina
ctive时间内⼀次没被使⽤，它将被移除。
    #语法:open_file_cache_min_uses number 默认值:open_file_cache_min_uses 1 使⽤字段:http, server, location  这个指令指定了在open_file_cache指令⽆
效的参数中⼀定的时间范围内可以使⽤的最⼩⽂件数,如果使⽤更⼤的值,⽂件描述符在cache中总是打开状态.
    open_file_cache_min_uses 1;
    #语法:open_file_cache_errors on | off 默认值:open_file_cache_errors off 使⽤字段:http, server, location 这个指令指定是否在搜索⼀个⽂件是记录cache错
误.
    open_file_cache_errors on;
}
#设定http服务器，利⽤它的反向代理功能提供负载均衡⽀持
http
{
    #⽂件扩展名与⽂件类型映射表
    include mime.types;
    #默认⽂件类型
    default_type application/octet-stream;
    #默认编码
    #charset utf-8;
    #服务器名字的hash表⼤⼩
    #保存服务器名字的hash表是由指令server_names_hash_max_size 和server_names_hash_bucket_size所控制的。参数hash bucket size总是等于hash表
的⼤⼩，并且是⼀路处理器缓存⼤⼩的倍数。在减少了在内存中的存取次数后，使在处理器中加速查找hash表键值成为可能。如果hash bucket size等于⼀路处
理器缓存的⼤⼩，那么在查找键的时候，最坏的情况下在内存中查找的次数为2。第⼀次是确定存储单元的地址，第⼆次是在存储单元中查找键值。因此，如果
Nginx给出需要增⼤hash max size 或 hash bucket size的提⽰，那么⾸要的是增⼤前⼀个参数的⼤⼩.
    server_names_hash_bucket_size 128;
    #客户端请求头部的缓冲区⼤⼩。这个可以根据你的系统分页⼤⼩来设置，⼀般⼀个请求的头部⼤⼩不会超过1k，不过由于⼀般系统分页都要⼤于1k，所以这
⾥设置为分页⼤⼩。分页⼤⼩可以⽤命令getconf PAGESIZE取得。
    client_header_buffer_size 32k;
    #客户请求头缓冲⼤⼩。nginx默认会⽤client_header_buffer_size这个buffer来读取header值，如果header过⼤，它会使⽤large_client_header_buffers来读
取。
    large_client_header_buffers 4 64k;
    #设定通过nginx上传⽂件的⼤⼩
    client_max_body_size 8m;
    #开启⾼效⽂件传输模式，sendfile指令指定nginx是否调⽤sendfile函数来输出⽂件，对于普通应⽤设为 on，如果⽤来进⾏下载等应⽤磁盘IO重负载应⽤，可
设置为off，以平衡磁盘与⽹络I/O处理速度，降低系统的负载。注意：如果图⽚显⽰不正常把这个改成off。
    #sendfile指令指定 nginx 是否调⽤sendfile 函数（zero copy ⽅式）来输出⽂件，对于普通应⽤，必须设为on。如果⽤来进⾏下载等应⽤磁盘IO重负载应⽤，
可设置为off，以平衡磁盘与⽹络IO处理速度，降低系统uptime。
    sendfile on;
    #开启⽬录列表访问，合适下载服务器，默认关闭。
    autoindex on;
    #此选项允许或禁⽌使⽤socke的TCP_CORK的选项，此选项仅在使⽤sendfile的时候使⽤
    tcp_nopush on;
    tcp_nodelay on;
    #长连接超时时间，单位是秒
    keepalive_timeout 120;
    #FastCGI相关参数是为了改善⽹站的性能：减少资源占⽤，提⾼访问速度。下⾯参数看字⾯意思都能理解。
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
    #gzip模块设置
    gzip on; #开启gzip压缩输出
    gzip_min_length 1k;    #最⼩压缩⽂件⼤⼩
gzip_buffers 4 16k;    #压缩缓冲区
    gzip_http_version 1.0;    #压缩版本（默认1.1，前端如果是squid2.5请使⽤1.0）
    gzip_comp_level 2;    #压缩等级
    gzip_types text/plain application/x-javascript text/css application/xml;    #压缩类型，默认就已经包含textml，所以下⾯就不⽤再写了，写上去也不会有问题
，但是会有⼀个warn。
    gzip_vary on;
    #开启限制IP连接数的时候需要使⽤
    #limit_zone crawler $binary_remote_addr 10m;
    #负载均衡配置
    upstream piao.jd.com {
        #upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表⽰权值，权值越⾼被分配到的⼏率越⼤。
        server 192.168.80.121:80 weight=3;
        server 192.168.80.122:80 weight=2;
        server 192.168.80.123:80 weight=3;
        #nginx的upstream⽬前⽀持4种⽅式的分配
        #1、轮询（默认）
        #每个请求按时间顺序逐⼀分配到不同的后端服务器，如果后端服务器down掉，能⾃动剔除。
        #2、weight
        #指定轮询⼏率，weight和访问⽐率成正⽐，⽤于后端服务器性能不均的情况。
        #例如：
        #upstream bakend {
        #    server 192.168.0.14 weight=10;
        #    server 192.168.0.15 weight=10;
        #}
        #2、ip_hash
        #每个请求按访问ip的hash结果分配，这样每个访客固定访问⼀个后端服务器，可以解决session的问题。
        #例如：
        #upstream bakend {
        #    ip_hash;
        #    server 192.168.0.14:88;
        #    server 192.168.0.15:80;
        #}
        #3、fair（第三⽅）
        #按后端服务器的响应时间来分配请求，响应时间短的优先分配。
        #upstream backend {
        #    server server1;
        #    server server2;
        #    fair;
        #}
        #4、url_hash（第三⽅）
        #按访问url的hash结果来分配请求，使每个url定向到同⼀个后端服务器，后端服务器为缓存时⽐较有效。
        #例：在upstream中加⼊hash语句，server语句中不能写⼊weight等其他的参数，hash_method是使⽤的hash算法
        #upstream backend {
        #    server squid1:3128;
        #    server squid2:3128;
        #    hash $request_uri;
        #    hash_method crc32;
        #}
        #tips:
        #upstream bakend{#定义负载均衡设备的Ip及设备状态}{
        #    ip_hash;
        #    server 127.0.0.1:9090 down;
        #    server 127.0.0.1:8080 weight=2;
        #    server 127.0.0.1:6060;
        #    server 127.0.0.1:7070 backup;
        #}
        #在需要使⽤负载均衡的server中增加 proxy_pass http://bakend/;
        #每个设备的状态设置为:
        #1.down表⽰单前的server暂时不参与负载
        #2.weight为weight越⼤，负载的权重就越⼤。
        #3.max_fails：允许请求失败的次数默认为1.当超过最⼤次数时，返回proxy_next_upstream模块定义的错误
        #4.fail_timeout:max_fails次失败后，暂停的时间。
        #5.backup：其它所有的⾮backup机器down或者忙的时候，请求backup机器。所以这台机器压⼒会最轻。
nginx⽀持同时设置多组的负载均衡，⽤来给不⽤的server来使⽤。
        #client_body_in_file_only设置为On 可以讲client post过来的数据记录到⽂件中⽤来做debug
        #client_body_temp_path设置记录⽂件的⽬录可以设置最多3层⽬录
        #location对URL进⾏匹配.可以进⾏重定向或者进⾏新的代理负载均衡
    }
    #虚拟主机的配置
    server
    {
        #监听端⼝
        listen 80;
        #域名可以有多个，⽤空格隔开
        server_name www.jd.com jd.com;
        index index.html index.htm index.php;
        root /data/www/jd;
        #对******进⾏负载均衡
        location ~ .*.(php|php5)?$
        {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            include fastcgi.conf;
        }
        #图⽚缓存时间设置
        location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires 10d;
        }
        #JS和CSS缓存时间设置
        location ~ .*.(js|css)?$
        {
            expires 1h;
        }
        #⽇志格式设定
        #$remote_addr与$http_x_forwarded_for⽤以记录客户端的ip地址；
        #$remote_user：⽤来记录客户端⽤户名称；
        #$time_local：⽤来记录访问时间与时区；
        #$request：⽤来记录请求的url与http协议；
        #$status：⽤来记录请求状态；成功是200，
        #$body_bytes_sent ：记录发送给客户端⽂件主体内容⼤⼩；
        #$http_referer：⽤来记录从那个页⾯链接访问过来的；
        #$http_user_agent：记录客户浏览器的相关信息；
        #通常web服务器放在反向代理的后⾯，这样就不能获取到客户的IP地址了，通过$remote_add拿到的IP地址是反向代理服务器的iP地址。反向代理服务器
在转发请求的http头信息中，可以增加x_forwarded_for信息，⽤以记录原有客户端的IP地址和原来客户端的请求的服务器地址。
        log_format access '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" $http_x_forwarded_for';
        #定义本虚拟主机的访问⽇志
        access_log  /usr/local/nginx/logs/host.access.log  main;
        access_log  /usr/local/nginx/logs/host.access.404.log  log404;
        #对 "/" 启⽤反向代理
        location / {
            proxy_pass http://127.0.0.1:88;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            #后端的Web服务器可以通过X-Forwarded-For获取⽤户真实IP
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            #以下是⼀些反向代理的配置，可选。
proxy_set_header Host $host;
            #允许客户端请求的最⼤单⽂件字节数
            client_max_body_size 10m;
            #缓冲区代理缓冲⽤户端请求的最⼤字节数，
            #如果把它设置为⽐较⼤的数值，例如256k，那么，⽆论使⽤firefox还是IE浏览器，来提交任意⼩于256k的图⽚，都很正常。如果注释该指令，使⽤默
认的client_body_buffer_size设置，也就是操作系统页⾯⼤⼩的两倍，8k或者16k，问题就出现了。
            #⽆论使⽤firefox4.0还是IE8.0，提交⼀个⽐较⼤，200k左右的图⽚，都返回500 Internal Server Error错误
            client_body_buffer_size 128k;
            #表⽰使nginx阻⽌HTTP应答代码为400或者更⾼的应答。
            proxy_intercept_errors on;
            #后端服务器连接的超时时间_发起握⼿等候响应超时时间
            #nginx跟后端服务器连接超时时间(代理连接超时)
            proxy_connect_timeout 90;
            #后端服务器数据回传时间(代理发送超时)
            #后端服务器数据回传时间_就是在规定时间之内后端服务器必须传完所有的数据
            proxy_send_timeout 90;
            #连接成功后，后端服务器响应时间(代理接收超时)
            #连接成功后_等候后端服务器响应时间_其实已经进⼊后端的排队之中等候处理（也可以说是后端服务器处理请求的时间）
            proxy_read_timeout 90;
            #设置代理服务器（nginx）保存⽤户头信息的缓冲区⼤⼩
            #设置从被代理服务器读取的第⼀部分应答的缓冲区⼤⼩，通常情况下这部分应答中包含⼀个⼩的应答头，默认情况下这个值的⼤⼩为指令proxy_buffer
s中指定的⼀个缓冲区的⼤⼩，不过可以将其设置为更⼩
            proxy_buffer_size 4k;
            #proxy_buffers缓冲区，⽹页平均在32k以下的设置
            #设置⽤于读取应答（来⾃被代理服务器）的缓冲区数⽬和⼤⼩，默认情况也为分页⼤⼩，根据操作系统的不同可能是4k或者8k
            proxy_buffers 4 32k;
            #⾼负荷下缓冲⼤⼩（proxy_buffers*2）
            proxy_busy_buffers_size 64k;
            #设置在写⼊proxy_temp_path时数据的⼤⼩，预防⼀个⼯作进程在传递⽂件时阻塞太长
            #设定缓存⽂件夹⼤⼩，⼤于这个值，将从upstream服务器传
            proxy_temp_file_write_size 64k;
        }
        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status on;
            access_log on;
            auth_basic "NginxStatus";
            auth_basic_user_file confpasswd;
            #htpasswd⽂件的内容可以⽤apache提供的htpasswd⼯具来产⽣。
        }
        #本地动静分离反向代理配置
        #所有jsp的页⾯均交由tomcat或resin处理
        location ~ .(jsp|jspx|do)?$ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8080;
        }
        #所有静态⽂件由nginx直接读取不经过tomcat或resin
        location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|
        pdf|xls|mp3|wma)$
        {
            expires 15d;
}
        location ~ .*.(js|css)?$
        {
            expires 1h;
        }
    }
}
```
上⾯的⼀些配置详解仅供参考学习
3.2 配置服务代理：
第⼀步：打开nginx.conf⽂件，修改配置⽂件
为了⽅便阅读，我把⼀些不⽤的配置给删除了
```bash
worker_processes  1;
events {
    worker_connections  1024;
}
#http结构下可以有多个server。请求进来确定使⽤哪⼀个 server由 server_name 确定
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    server { #第⼀个server
        listen       81;#nginx的监听端⼝为81
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    server {
        listen       8001;
        server_name  localhost;
        location ~ /PMS/ {
          proxy_pass  http://localhost:8080; 
        }
    }
}
```
4. 配置过程的注意事项：
4.1 nginx启动等命令
1.配置好后，需要重新启动nginx（来到nginx.exe⽂件下打开cmd界⾯）：
关闭的命令：nginx.exe - s stop
2.启动命令：nginx.exe
3.重新加载配置命令：nginx.exe - s reload
4.2 location 详解
4.2.1 location介绍
location :⼀个server下可以有多个location ，⽤来匹配 同⼀个域名下不同uri的访问
root 表⽰本地的根⽬录 ,可以写相对路径 也可以写绝对路径如 e：/nginx/html ；
index :访问主页 后⾯如果跟有多个页⾯ 则会依次判断 如果第⼀个不存在 则向后匹配；
proxy_pass ：转发 后跟系统地址
4.2.2 location匹配原则
location表达式
syntax: location [=|~ | ~* |^~|@] /uri/ { - }
分为两种匹配模式，普通字符串匹配，正则匹配
⽆开头引量字符或以=开头表⽰普通字符电匹配
以~ 或 ~ * 开头表⽰正则匹配，~ * 表⽰不区分⼤⼩写
多个locati on时匹配规则
总体是先普通后正则原则，只识别URI部分，例如请求为/test/1/abc . do?arg=xxx
1.先查找是否有=开头的精确匹配。即Location = /test/1/abc.do {…}
2.再查找普通匹配，以最⼤前缀为规则，如有以下两个location
location /test/ {…}
location /test/1/ {…}
则匹配后⼀项
3.匹配到⼀个普通格式后，搜索并未结束。⽽是暂存当前结果，并继续再搜索正则模式
4.在所有正则模式lacation中找到第⼀个匹配项后，以此四配项为最终结果
所以正则匹配项匹配规则受定义前后顺序影响。但普通匹配不会
5.如果未找到正则匹配项，则以3中缓存的结果为最终结果
6.如果⼀个匹配都没有，返回404
location =/ {…} 与location / {…} 的差别
前⼀个是精确匹配，只响应/请求，所有/xxx类请求不会以前缀匹配形式匹配到它
⽽后⼀个正相反，所有请求必然都是以/开头，所以没有其它匹配结果时⼀⼆定会执⾏到它
location A- 1 {…} A-意思是⾲正则。表⽰匹配到此模式后不再继线正则搜索
#所有如果这样配置，相当于关团了正则匹配功能
#因为⼀个请求在普通匹配规则下没得到其它普通匹配结果时，最終匹配到这⾥
⽽这个^-指令⼜相当于不允许正则，.机当于匹配到此为⽌/test/abc.jsp
**特别强调⼀点
在配置 proxy_pass 记得在url不要加上斜杠/