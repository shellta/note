## AOP 面向切面编程

AOP利用一种称为"横切"的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为"Aspect"，即切面。
所谓"切面"，简单说就是那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。


### 原理
1. aop底层将采用代理机制进行实现。
2. 接口 + 实现类 ：spring采用 jdk 的动态代理Proxy。
3. 实现类：spring 采用 cglib字节码增强。

### 术语
1. target：目标类，即需要被代理的类。例如：UserService
2. Joinpoint(连接点):所谓连接点是指那些可能被拦截到的方法。例如：所有的方法
3. PointCut 切入点：已经被增强的连接点。例如：addUser()
4. advice 通知/增强，增强代码。例如：after.before
5. Weaving(织入):是指把增强advice应用到目标对象target来创建新的代理对象proxy的过程.
6. proxy 代理类
7. Aspect(切面): 是切入点pointcut和通知advice的结合


## 示例
```java
package org.example.demo.controller;


import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HomeController {

    @GetMapping("/a")
    public String testA() {
        System.out.println("aaa");
        return "a";
    }
}
```

```java 
package org.example.demo.aspect;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TestAspect {

    @Pointcut("execution(* org.example.demo.controller.HomeController.testA())")
    public void point() {}

    @Before("point()")
    public void before(JoinPoint jp) {
        System.out.println("before");
    }

    @After("point()")
    public void after(JoinPoint jp) {
        System.out.println("after");
    }
}

```

打开浏览器 [http://127.0.0.1:8080/a](http://127.0.0.1:8080/a) 运行时我们会发现
```
before
aaa
after
```

解析：
`@Pointcut`注解意思为需要在哪个类的哪个方法上面进行切入
`@Before`注解为在需要切入的点之前执行
`@After`注解为在需要切入的点之后执行

上面的代码可以改为以下
```java
package org.example.demo.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class TestAspect {

    @Pointcut("execution(* org.example.demo.controller.HomeController.testA())")
    public void point() {}

    @Around("point()")
    public String around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("before");
        String result = (String) pjp.proceed();
        System.out.println("after");
        return result;
    }
}
```

其中 `@Around`注解即包含了 `@Before`与`@After`等功能。


### 总结
其实AOP还能完成更多的功能，在次就不一一列出。
AOP把软件系统分为两个部分：核心关注点和横切关注点。
业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处都基本相似。比如权限认证、日志、事务处理。Aop 的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。
