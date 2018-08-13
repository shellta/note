### crontab 定时任务

命令格式：
``` shell
crontab [-u user] file crontab [-u user] [ -e | -l | -r ]
```

命令参数：
* -u user：用来设定某个用户的crontab服务；
file：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入crontab。如果在命令行中没有指定这个文件，crontab命令将接受标准输入（键盘）上键入的命令，并将它们载入crontab。
* -e：编辑某个用户的crontab文件内容。如果不指定用户，则表示编辑当前用户的crontab文件。
* -l：显示某个用户的crontab文件内容，如果不指定用户，则表示显示当前用户的crontab文件内容。
* -r：从/var/spool/cron目录中删除某个用户的crontab文件，如果不指定用户，则默认删除当前用户的crontab文件。
* -i：在删除用户的crontab文件时给确认提示。

crontab文件格式
* 第1列 分钟 0-59
* 第2列 小时 0-23
* 第3列 日   1-31
* 第4列 月   1-12
* 第5列 星期 0-7 (0,7表示星期天)
* 第6列 要运行的命令


示例
``` shell
    0 3 * * * /sbin/reboot # 每天3点重启
```
