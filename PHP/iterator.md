## PHP 迭代器

[官方文档](http://php.net/manual/zh/class.iterator.php)

作用：可在内部迭代自己的外部迭代器或类的接口。

接口的方法：  

``` php
Iterator extends Traversable {
    abstract public mixed current ( void )
    abstract public scalar key ( void )
    abstract public void next ( void )
    abstract public void rewind ( void )
    abstract public bool valid ( void )
}
```

实例：

``` php
<?php

class MyIterator implements Iterator
{
    private $position;

    private $data;

    public function __construct(array $data = [])
    {
        $this->data = $data;
        $this->position = 0;
    }

    // 返回当前元素
    public function current()
    {
        var_dump(__METHOD__);
        return $this->data[$this->position];
    }

    // 返回当前元素的键
    public function key()
    {
        var_dump(__METHOD__);
        return $this->position;
    }

    // 下一个元素
    public function next()
    {
        var_dump(__METHOD__);
        ++$this->position;
    }

    // 回到第一个元素
    public function rewind()
    {
        var_dump(__METHOD__);
        $this->position = 0;
    }

    // 判断当前元素是否有效
    public function valid()
    {
        var_dump(__METHOD__);
        return isset($this->data[$this->position]);
    }
}

$c = new MyIterator(['a', 'b']);

foreach ($c as $key => $value) {
    echo $key, ' => ', $value, PHP_EOL;
}

```

可以看到执行的方法顺序为
```
    rewind -> valid
         -> current -> key -> next -> valid 
         -> current -> key -> next -> valid
```


可以在 [这里](http://php.net/manual/zh/spl.iterators.php) 看到 PHP 官方预定义的迭代器
