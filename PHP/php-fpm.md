### PHP-FPM 配置

``` conf
[global]
pid = run/php-fpm.pid                     # pid文件存放位置
error_log = log/php-fpm.log               # 错误日志位置

[webid]
listen = /dev/shm/php-fpm-5.sock          # 接受 FastCGI 请求的地址
listen.owner = webid                      # 所有者
listen.group = webid                      # 所有用户组
user = webid                              # FPM 进程运行的Unix用户
group = webid                             # FPM 进程运行的Unix用户组
pm = dynamic                              # 选择进程池管理器如何控制子进程的数量
pm.max_children = 120                     # 最大子进程
pm.start_servers = 50                     # 起始php-fpm数量
pm.min_spare_servers = 20                 # 空闲服务进程的最低数目
pm.max_spare_servers = 80                 # 空闲服务进程的最大数目
pm.max_requests = 0                       # 设置每个子进程重生之前服务的请求数
pm.status_path = /status                  # FPM 状态页面的网址
request_terminate_timeout = 0             # 单个请求的超时中止时间
request_slowlog_timeout = 5               # 慢日志
slowlog = /data/logs/www/php/php_slow.log # 慢日志存放位置

```

见 [http://php.net/manual/zh/install.fpm.configuration.php](http://php.net/manual/zh/install.fpm.configuration.php)
