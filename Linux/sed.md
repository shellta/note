## sed 文本替换利器

* 首处替换
    ``` shell
        echo -e "abbb" | sed 's/b/c/' # 将首次出现的b替换为c

        > acbb
    ```

* 全局替换
    ``` shell
        cat abbb > a.txt
        sed 's/b/c/g' a.txt

        > accc
    ```

    默认替换后，输出替换后的内容，如果需要替换原文件,使用 -i  
    ``` shell
        sed -i 's/b/c/g' a.txt
    ```

* 子串匹配标记
    第一个匹配的括号内容使用标记1来引用

    ``` shell
        echo hello 123456 | sed 's/hello \([0-9]\)/\1/'

        > 123456
    ```

* 更换 Ubuntu 默认源
    ``` shell
        sed -i 's/cn.archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
    ```
