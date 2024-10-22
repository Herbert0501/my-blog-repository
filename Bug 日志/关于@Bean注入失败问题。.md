---
title: 关于@Bean注入失败问题。
slug: guan-yu-beanzhu-ru-shi-bai-wen-ti.
cover: ""
categories: []
tags: []
halo:
  site: https://blog.kangyaocoding.top
  name: 7861a3dc-fe78-402b-bf7b-701efc17b539
  publish: false
---

在使用 Redission 进行 Bean 注入时出现了一个名称冲突的问题。

把变量名从 `dynamicRedissonClient` 改为 `dynamicThreadRedissonClient` 后修复了问题，原因在于 Spring 容器中的 **Bean 名称冲突** （**变量名的影响**）解决了。

### 背景：

在 Spring 框架中，每个 `@Bean` 方法会生成一个 Bean，默认情况下，Bean 的名称与方法名相同，或者可以通过 `@Bean("beanName")` 指定一个名称。当多个相同类型的 Bean 存在时，Spring 会尝试根据名称来区分这些 Bean，除非明确标记了 `@Primary` 或使用了 `@Qualifier`。

### 原因分析：

1. **Bean 名称冲突**：
    
    - 最初的代码中，`redisRegistry` 方法需要注入一个 `RedissonClient` 实例。
    - 在 Spring 容器中，有两个 `RedissonClient` Bean：
        - 一个是由 Spring 的 `RedissonAutoConfigurationV2` 自动配置类定义的 `redisson` Bean。
        - 另一个是你自定义的 `dynamicThreadRedissonClient` Bean。
    - 当 `redisRegistry` 方法的参数名为 `dynamicRedissonClient` 时，Spring 尝试根据类型 (`RedissonClient`) 和名称 (`dynamicRedissonClient`) 匹配 Bean，但是没有明确的 Bean 名称为 `dynamicRedissonClient`，所以会导致无法唯一确定使用哪个 Bean，Spring 报了 `NoUniqueBeanDefinitionException`。
2. **变量名的影响**✔️：
    
    - Spring 通过**类型优先匹配**进行依赖注入，但如果有多个相同类型的 Bean 存在，它会根据参数名（即方法参数的变量名）来尝试匹配对应名称的 Bean。
    - 当方法参数名改为 `dynamicThreadRedissonClient` 后，Spring 会将这个名称与定义的 `@Bean("dynamicThreadRedissonClient")` 名称匹配，从而唯一确定该 Bean，解决了冲突。

### 总结：

将参数名改为 `dynamicThreadRedissonClient` 后，Spring 能够根据**变量名与 Bean 名称的匹配**成功注入你自定义的 `dynamicThreadRedissonClient` Bean，解决了 Bean 的歧义问题。因此，修改变量名是让 Spring 正确匹配到 Bean 的原因。

如果不想依赖变量名的匹配，还可以通过 `@Qualifier` 明确指定 Bean，或者使用 `@Primary` 来标记优先使用的 Bean。

### 代码：

**冲突的代码**：

```Java
    @Bean("dynamicThreadRedissonClient")
    public RedissonClient redissonClient(DynamicThreadPoolAutoConfigProperties properties) {
        Config config = new Config();
        // 根据需要可以设定编解码器；https://github.com/redisson/redisson/wiki/4.-%E6%95%B0%E6%8D%AE%E5%BA%8F%E5%88%97%E5%8C%96
        config.setCodec(JsonJacksonCodec.INSTANCE);

        config.useSingleServer()
                .setAddress("redis://" + properties.getHost() + ":" + properties.getPort())
                .setPassword(properties.getPassword())
                .setConnectionPoolSize(properties.getPoolSize())
                .setConnectionMinimumIdleSize(properties.getMinIdleSize())
                .setIdleConnectionTimeout(properties.getIdleTimeout())
                .setConnectTimeout(properties.getConnectTimeout())
                .setRetryAttempts(properties.getRetryAttempts())
                .setRetryInterval(properties.getRetryInterval())
                .setPingConnectionInterval(properties.getPingInterval())
                .setKeepAlive(properties.isKeepAlive())
        ;

        RedissonClient redissonClient = Redisson.create(config);

        logger.info("动态线程池，注册器（redis）链接初始化完成。{} {} {}", properties.getHost(), properties.getPoolSize(), !redissonClient.isShutdown());

        return redissonClient;
    }

    @Bean
    public IRegistry redisRegistry(RedissonClient dynamicRedissonClient) {
        return new RedisRegistry(dynamicRedissonClient);
    }
```

**修复后的代码**：

```Java
    @Bean("dynamicThreadRedissonClient")
    public RedissonClient redissonClient(DynamicThreadPoolAutoConfigProperties properties) {
        Config config = new Config();
        // 根据需要可以设定编解码器；https://github.com/redisson/redisson/wiki/4.-%E6%95%B0%E6%8D%AE%E5%BA%8F%E5%88%97%E5%8C%96
        config.setCodec(JsonJacksonCodec.INSTANCE);

        config.useSingleServer()
                .setAddress("redis://" + properties.getHost() + ":" + properties.getPort())
                .setPassword(properties.getPassword())
                .setConnectionPoolSize(properties.getPoolSize())
                .setConnectionMinimumIdleSize(properties.getMinIdleSize())
                .setIdleConnectionTimeout(properties.getIdleTimeout())
                .setConnectTimeout(properties.getConnectTimeout())
                .setRetryAttempts(properties.getRetryAttempts())
                .setRetryInterval(properties.getRetryInterval())
                .setPingConnectionInterval(properties.getPingInterval())
                .setKeepAlive(properties.isKeepAlive())
        ;

        RedissonClient redissonClient = Redisson.create(config);

        logger.info("动态线程池，注册器（redis）链接初始化完成。{} {} {}", properties.getHost(), properties.getPoolSize(), !redissonClient.isShutdown());

        return redissonClient;
    }

    @Bean
    public IRegistry redisRegistry(RedissonClient dynamicThreadRedissonClient) {
        return new RedisRegistry(dynamicThreadRedissonClient);
    }
```

> **感觉真的很相似，所以变量名还是需要注意一下，自己需要注入哪个一个 Bean ，要写好**。

### 报错日志：

```log
24-09-15.12:13:44.909 [main            ] WARN  AnnotationConfigServletWebServerApplicationContext - Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'redisRegistry' defined in class path resource [top/kangyaocoding/middleware/dynamic/thread/pool/sdk/config/DynamicThreadPoolAutoConfig.class]: Unsatisfied dependency expressed through method 'redisRegistry' parameter 0; nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'org.redisson.api.RedissonClient' available: expected single matching bean but found 2: redisson,dynamicThreadRedissonClient
24-09-15.12:13:49.060 [main            ] INFO  StandardService        - Stopping service [Tomcat]
24-09-15.12:13:49.073 [main            ] INFO  ConditionEvaluationReportLoggingListener - 

Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
24-09-15.12:13:49.087 [main            ] ERROR LoggingFailureAnalysisReporter - 

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of method redisRegistry in top.kangyaocoding.middleware.dynamic.thread.pool.sdk.config.DynamicThreadPoolAutoConfig required a single bean, but 2 were found:
	- redisson: defined by method 'redisson' in class path resource [org/redisson/spring/starter/RedissonAutoConfigurationV2.class]
	- dynamicThreadRedissonClient: defined by method 'redissonClient' in class path resource [top/kangyaocoding/middleware/dynamic/thread/pool/sdk/config/DynamicThreadPoolAutoConfig.class]


Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
```