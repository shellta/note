## Spring 集成 redis
1. 添加依赖到 pom.xml
``` xml
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
```

2. 更新application.yml
``` yml
  spring:
    redis:
      host: 127.0.0.1
      port: 6379
      password: 
      database: 0
```

3. 编写测试类
``` java
    package org.example.demo.service;

    import org.junit.jupiter.api.Test;
    import org.junit.runner.RunWith;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.data.redis.core.RedisTemplate;
    import org.springframework.test.context.junit4.SpringRunner;

    import javax.annotation.Resource;

    import static org.junit.jupiter.api.Assertions.*;

    @SpringBootTest
    @RunWith(SpringRunner.class)
    class RedisServiceTest {

        @Resource
        RedisTemplate redisTemplate;

        String k = "aaa";

        String v = "bbb";

        @Test
        void set() {
            redisTemplate.opsForValue().set(k, v);
        }

        @Test
        void get() {
            assertEquals(v, redisTemplate.opsForValue().get(k));
        }
    }
```

4. 执行set get测试方法发现测试通过。

## 自定义Cache Manager
1. 打开redis monitor, 发现在执行set的时候实际上执行的命令是
``` shell
    SET \xac\xed\x00\x05t\x00\x03aaa \xac\xed\x00\x05t\x00\x03bbb
```
这是什么原因呢，原来spring-data-redis 对 key 和 value 都进行了序列化 变成byte[]进行存储的，所以它的key和value都变化了。那我们自定义一下它的序列化策略.

2. 自定义序列化策略
``` java
    package org.example.demo.config;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.data.redis.cache.RedisCacheManager;
    import org.springframework.data.redis.connection.RedisConnectionFactory;
    import org.springframework.data.redis.core.RedisTemplate;
    import org.springframework.data.redis.serializer.StringRedisSerializer;

    @Configuration
    public class RedisConfiguration {

        @Bean
        public RedisCacheManager cacheManager(RedisConnectionFactory redisConnectionFactory) {
            return RedisCacheManager.create(redisConnectionFactory);
        }

        @Bean
        public RedisTemplate redisTemplate(RedisConnectionFactory factory) {
            RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
            redisTemplate.setConnectionFactory(factory);
            redisTemplate.setKeySerializer(new StringRedisSerializer()); // 指定 key 的序列化策略
            redisTemplate.setValueSerializer(new StringRedisSerializer()); // 指定 value 的序列化策略
            return redisTemplate;
        }
    }
```

3. 再次执行上述测试方法，发现redis执行的命令已经是正常的命令了


### 其他
redisTemplate.opsForValue();  
redisTemplate.opsForHash();  
redisTemplate.opsForList();  
redisTemplate.opsForSet();  
redisTemplate.opsForZSet();  
分别对应redis中不同数据类型的操作
