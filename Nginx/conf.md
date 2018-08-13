
``` conf
# 运行用户
user www-data; 

# 工作进程的数量 最优值取决于许多因素，包括（但不限于）CPU核的数量、存储数据的硬盘数量及负载模式
worker_processes auto;

# 主进程号
pid /run/nginx.pid;

# 包含文件
include /etc/nginx/modules-enabled/*.conf;

# 事件模块
events {
    # 最大连接数
    worker_connections 768;

    # 收到一个新连接通知后接受尽可能多的连接
    # multi_accept on;

    # 复用客户端线程的轮询方法
    use epoll;
}

# http模块
http {
        #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，
        #对于普通应用，必须设为 on,
        #如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，
        #以平衡磁盘与网络I/O处理速度，降低系统的uptime.
        sendfile on;

        #在启用了 sendfile 之后才生效。启用它之后，数据包会累计到一定大小之后才会发送，减小了额外开销，提高网络效率
        tcp_nopush on;

        # 启用后会禁用 Nagle 算法，尽快发送数据，某些情况下可以节约 200ms（Nagle 算法原理是：在发出去的数据还未被确认之前，新生成的小数据先存起来，凑满一个 MSS 或者等到收到确认后再发送）
        tcp_nodelay on;

        # 连接超时时间
        keepalive_timeout 65;

        # 影响散列表的冲突率。types_hash_max_size越大，就会消耗更多的内存，但散列key的冲突率会降低，检索速度就更快。types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升。
        types_hash_max_size 2048;

        # 响应头中nginx的版本号
        # server_tokens off;

        # 保存服务器名字的hash表的大小
        # server_names_hash_bucket_size 64;

        # 如果server_name_in_redirect为off时，那么将会以当前服务器的IP地址进行拼接URL；如果该命令为on，那么首先查找server_name，如果没有找到，查找请求头的HOST字段，如果没有，则以当前服务器的IP进行拼接。如果是完整的URL，那么直接以给出的URL返回
        # server_name_in_redirect off;

        # 设定mime类型,类型由mime.type文件定义
        include /etc/nginx/mime.types;

        # 默认响应的mime类型
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        # ssl 加密协议
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE

        # 设置协商加密算法时，优先使用服务器端协议
        ssl_prefer_server_ciphers on;

        # 访问日志
        access_log /var/log/nginx/access.log;

        # nginx错误日志
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        # 开启gzip压缩
        gzip on;

        # 告诉接收方发送的数据经过了压缩处理
        # gzip_vary on;

        # Nginx做为反向代理的时候启用
        # gzip_proxied any;

        #  设置gzip压缩等级，等级越低压缩速度越快文件压缩比越小，反之速度越慢文件压缩比越大，占用的cpu资源也与压缩等级正相关
        # gzip_comp_level 6;

        # 设置gzip压缩文件使用缓存空间的大小,其作用是按块大小的倍数申请内存空间 (16块，每个8k)
        # gzip_buffers 16 8k;

        # 用于识别http协议的版本
        # gzip_http_version 1.1;

        # 需要压缩的文件类型
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}
```


``` conf

# 虚拟主机配置
server {

    # 监听端口
    listen 80;

    # 定义使用www.abc.com域名访问
    server_name  www.abc.com;

    # 访问日志
    access_log /data/logs/nginx/access.log access_format;

    # 错误日志
    error_log /data/logs/nginx/error.log debug;

    # 根目录
    root /data/webserver/www.abc.com/public;

    # 默认索引文件
    index index.php index.html index.htm;

    # 当文件不存在时 重写访问为/index.php?a=$1 $1为正则第一个匹配的内容
    if (!-e $request_filename){
        rewrite ^(.*)$ /index.php?a=$1 last;
    }

    # 404, 403, 500等错误页面
    error_page 404 = /;
    error_page 403 http://g.pps.tv/404.html;
    error_page 500 502 503 504 /50x.html;

    location = /50x.html {
        root html;
    }

    # 不区分大小写匹配以jpg、jpeg等后缀的文件。
    location ~* ^.+\.(jpg|jpeg|gif|png|ico)$
    {
        # 重写 例/app.jpg -> /g/app.jpg
        rewrite ^(.*) /g/$1 break;

        # 通过代理访问
        proxy_pass http://static.abc.com;

        # 过期时间为1年
        expires 1y;
    }

    location ~* ^.+\.(js|css)$
    {
        rewrite ^(.*) /g/$1 break;
        proxy_pass http://static.abc.com;
    }

    expires 1h;

    # 区分大小写的以.php为结尾的匹配
    location ~ \.php$ {

        # 文件不存在是返回404
        if (!-e $request_filename) {
            return 404;
        }

        # nginx 与 php-fpm通信的方式 有两种方式：Unix sock 与 TCP方式
        # Unix socket适合同一服务器， TCP 适合多服务器场景。
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;

        # 如果URI以斜线结尾，文件名将追加到URI后面
        fastcgi_index index.php;

        # 脚本文件请求的路径
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        include fastcgi_params;
        index index.php index.html index.htm;
    }

}

```

Nginx 部分内置变量
``` conf
$arg_name #请求中的的参数名，即"?"后面的arg_name=arg_value形式的arg_name

$args #请求中的参数值

$binary_remote_addr #客户端地址的二进制形式, 固定长度为4个字节

$body_bytes_sent #传输给客户端的字节数，响应头不计算在内；这个变量和Apache的mod_log_config模块中的"%B"参数保持兼容

$bytes_sent #传输给客户端的字节数 

$connection #TCP连接的序列号 

$connection_requests #TCP连接当前的请求数量 

$content_length #"Content-Length" 请求头字段

$content_type #"Content-Type" 请求头字段

$cookie_name #cookie名称

$document_root #当前请求的文档根目录或别名

$document_uri #同 $uri

$host #优先级如下：HTTP请求行的主机名>"HOST"请求头字段>符合请求的服务器名

$hostname #主机名

$http_name #匹配任意请求头字段； 变量名中的后半部分"name"可以替换成任意请求头字段，如在配置文件中需要获取http请求头："Accept-Language"，那么将"－"替换为下划线，大写字母替换为小写，形如：$http_accept_language即可。

$https #如果开启了SSL安全模式，值为"on"，否则为空字符串。

$is_args #如果请求中有参数，值为"?"，否则为空字符串。

$limit_rate #用于设置响应的速度限制，详见 limit_rate。

$msec #当前的Unix时间戳 

$nginx_version #nginx版本

$pid #工作进程的PID

$pipe #如果请求来自管道通信，值为"p"，否则为"." 

$proxy_protocol_addr #获取代理访问服务器的客户端地址，如果是直接访问，该值为空字符串。

$query_string #同 $args

$realpath_root #当前请求的文档根目录或别名的真实路径，会将所有符号连接转换为真实路径。

$remote_addr #客户端地址

$remote_port #客户端端口

$remote_user #用于HTTP基础认证服务的用户名

$request #代表客户端的请求地址

$request_body #客户端的请求主体 #此变量可在location中使用，将请求主体通过proxy_pass, fastcgi_pass, uwsgi_pass, 和 scgi_pass传递给下一级的代理服务器。

$request_body_file #将客户端请求主体保存在临时文件中。文件处理结束后，此文件需删除。如果需要之一开启此功能，需要设置client_body_in_file_only。如果将次文件传递给后端的代理服务器，需要禁用request body，即设置proxy_pass_request_body off，fastcgi_pass_request_body off, uwsgi_pass_request_body off, or scgi_pass_request_body off 。

$request_completion #如果请求成功，值为"OK"，如果请求未完成或者请求不是一个范围请求的最后一部分，则为空。

$request_filename #当前连接请求的文件路径，由root或alias指令与URI请求生成。

$request_length #请求的长度 (包括请求的地址, http请求头和请求主体) 

$request_method #HTTP请求方法，通常为"GET"或"POST"

$request_time #处理客户端请求使用的时间  从读取客户端的第一个字节开始计时。

$request_uri #这个变量等于包含一些客户端请求参数的原始URI，它无法修改，请查看$uri更改或重写URI，不包含主机名，例如："/cnphp/test.php?arg=freemouse"。

$scheme #请求使用的Web协议, "http" 或 "https"

$sent_http_name #可以设置任意http响应头字段； 变量名中的后半部分"name"可以替换成任意响应头字段，如需要设置响应头Content-length，那么将"－"替换为下划线，大写字母替换为小写，形如：$sent_http_content_length 4096即可。

$server_addr #服务器端地址，需要注意的是：为了避免访问linux系统内核，应将ip地址提前设置在配置文件中。

$server_name #服务器名

$server_port #服务器端口

$server_protocol #服务器的HTTP版本, 通常为 "HTTP/1.0" 或 "HTTP/1.1"

$status #HTTP响应代码 

$tcpinfo_rtt, $tcpinfo_rttvar, $tcpinfo_snd_cwnd, $tcpinfo_rcv_space #客户端TCP连接的具体信息

$time_iso8601 #服务器时间的ISO 8610格式 

$time_local #服务器时间（LOG Format 格式） 

$uri #请求中的当前URI(不带请求参数，参数位于$args)，可以不同于浏览器传递的$request_uri的值，它可以通过内部重定向，或者使用index指令进行修改，$uri不包含主机名，如"/foo/bar.html"。

```

部分语法规则

* =   开头表示精确匹配
* ^~  开头表示以某个常规字符串开头
* ~   开头表示区分大小写的正则匹配
* ~*  开头表示不区分大小写的正则匹配
* !~  区分大小写的不匹配的正则
* !~* 不区分大小写的不匹配的正则
* /   通用匹配，任何情况都会匹配到

rewrite语法
* rewrite ^(.*)$ http://www.abc.com/$1 last;         #url重写，发起新的请求
* rewrite ^(.*)$ http://www.abc.com/$1 break;        #url重写后终止当前请求，不在执行location下其余语句
* rewrite ^(.*)$ http://www.abc.com/$1 redirect;     #302临时重定向
* rewrite ^(.*)$ http://www.abc.com/$1 permanent;    #301永久重定向


判断表达式
* -f和!-f 判断文件是否存在
* -d和!-d 判断目录是否存在
* -e和!-e 判断文件或目录是否存在
* -x和!-x 判断文件是否可执行
