## PHP 生成器

[生成器总览](http://php.net/manual/zh/language.generators.overview.php)  
[生成器类](http://php.net/manual/zh/class.generator.php)

生成器提供了一种更容易的方法来实现简单的对象迭代。相比于类实现 Iterator 接口的方式，性能开销和复杂性大大降低。


示例：

``` php
    function y() {
        for ($i = 0; $i < 10; $i++) {
            yield $i;
        }
    }

    foreach (y() as $key => $value) {
        echo $key, " => ", $value, PHP_EOL;
    }
```

最重要的就是 **yield** 关键字，所以这个函数 **y** 就是一个生成器。  
生成器不能返回值，这样做的话会产生一个编译错误。但是可以为 *return;* 的空语句。


可以调用 [send方法](http://php.net/manual/zh/generator.send.php) 来向生成器中传人一个值
``` php
    function y() {
        while (1) {
            echo yield, PHP_EOL;
        }
    }

    $y = y();
    $y->send('1');
    $y->send('2');
    $y->send('3');
    $y->send('4');
    $y->send('5');
```

### 生成器返回值
如果生成器遇到 return 或着被迭代完成，这个生成器是会有一个返回值的。  
有两种方法可以获取到这种返回值  
* $result = Generator::getReturn();
* $result = yield from Generator();

例如：
``` php
function x() {
    for ($i=0; $i < 10; $i++) {
        echo $i, PHP_EOL;
        yield;
    }

    return '=> '. $i . PHP_EOL;
}

function y() {
    $a = yield from x();
    echo $a;

    $b = x();
    yield from $b;
    echo $b->getReturn();
}


foreach (y() as $y) {
}
```


