### grep 文本搜索

用法：
``` shell
    grep pattern file
```

常用参数：
* -o 只输出匹配的文本行
* -v 只输出没有匹配的文本行
* -c 统计文件中包含文本的次数
* -n 打印匹配的行号
* -i 搜索时忽略大小写
* -l 只打印文件名

示例:
在多级目录中对文本进行递归搜索
``` shell
    grep "class" . -R -n
```

匹配多个模式
``` shell
    grep -e "class" -e "virtural" file
```
