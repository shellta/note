## awk 数据流处理工具

* awk 脚本结构  
    ``` shell
        awk ' BEGIN{ statements1 } statements2 END{ statements3 } '
    ```

* 工作流程
    1. 先执行 BEGIN 语句块 statements1
    2. 从文件或 stdin 中读入一行，然后执行 statements2，重复这个过程，直至全部读取完毕。
    3. 执行 END 语句块 statements3

### Print 打印当前行

* 使用不带参数的print时，会打印当前行
    ``` shell
        echo -e "aaa\nbbb"  | awk 'BEGIN {print "START"} {print} END {print "END"}' 

        > START
        > aaa
        > bbb
        > END
    ```

* 以逗号分隔时， 参数以空格为分界
    ``` shell
        echo | awk '{v1 = "v11"; v2 = "v22"; print v1, v2;}'

        > v11 v22
    ```

* 使用 - 进行分隔
    ``` shell
     echo | awk '{v1 = "v11"; v2 = "v22"; print v1"-"v2;}'

     > v11-v22
    ```

### 特殊变量  
NR: 表示记录数量，执行过程中对应当前行号;  
NF: 表示字段数量，执行过程中对应当前行的字段数;  
$0: 当前行的文本内容
$1: 第一个字段的文本内容
$2: 第二个字段的文本内容

* 打印当前行号及第一个字段
    ``` shell
        echo -e "a b\nc d"  | awk '{print NR": "$1}'

        > 1: a
        > 2: b
    ```

* 统计文件行数
    ``` shell
        echo -e "a\nb\n"  | awk 'END{print NR}'

        > 3
    ```

* awk 结合 grep 找到指定服务，将其 kill 掉
    ``` shell
        ps -ef | grep nginx |  awk '{print $2}' | xargs kill -9
    ```

* awk 实现head, tail命令
    ``` shell
        awk 'NR<=10{print}' file
    ```

    ``` shell
        awk '{buffer[NR%10] = $0;} END{for(i=0; i<11; i++){ print buffer[i %10]} } ' filename
    ```


更多用法可参考awk manual.
