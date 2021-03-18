---
layout: article
title: SpringBoot Druid 配置多数据源
tags: SpringBoot Druid
---

## 1 环境

1. SpringBoot 2.2.1.RELEASE
2. Mybatis-Plus 3.1.0
3. Druid 1.1.9

## 2 配置 Druid 数据源

> 使用 java config 配置，禁用 durid 集成 Springboot 的自动装配,因为自定义的 Druid config

**数据源名称常量**
```
public interface DataSourceNames {
    String ONE = "ONE";
    String TWO = "TWO";
}
```

### yml 配置

```yaml
spring:
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driverClassName: 数据库驱动类
    druid:
      one:  # 数据源 1
        url: 数据库URL
        username: 账户
        password: 密码
      two:  # 数据源 2
        url: 数据库URL
        username: 账户
        password: 密码
```

### 创建 Druid 配置文件

```
@Configuration
public class DynamicDataSourceConfig {

    /**
     * 读取配置文件，创建 DataSource 1
     * @return DataSource Bean
     */
    @Bean
    @ConfigurationProperties("spring.datasource.druid.one")
    public DataSource oneDataSource(){
        DataSource dataSource = DruidDataSourceBuilder.create().build();
        return dataSource;
    }

    /**
     * 读取配置文件，创建 DataSource 2
     * @return DataSource Bean
     */
    @Bean
    @ConfigurationProperties("spring.datasource.druid.two")
    public DataSource twoDataSource(){
        DataSource dataSource = DruidDataSourceBuilder.create().build();
        return dataSource;
    }

    /**
     * 创建动态数据源对象
     * @param oneDataSource 注入数据源 1
     * @param twoDataSource 注入数据源 2
     * @return DynamicDataSource 对象
     */
    @Bean
    @Primary
    public DynamicDataSource dataSource(DataSource oneDataSource, DataSource twoDataSource) {
        Map<Object, Object> targetDataSources = new HashMap<>(2);
        targetDataSources.put(DataSourceNames.ONE, oneDataSource);
        targetDataSources.put(DataSourceNames.TWO, twoDataSource);
        // 还有数据源,在targetDataSources中继续添加
        return new DynamicDataSource(oneDataSource, targetDataSources);
    }
}
```

**启动类中加载当前配置文件**
```java
@Import({DynamicDataSourceConfig.class})
```
**在启动类注解中将原有的依赖去除**
```java
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
```

## 3 使用 AbstractRoutingDataSource 动态切换数据源

```java
public class DynamicDataSource extends AbstractRoutingDataSource {

    /**
     * ThreadLocal 用于提供线程局部变量，在多线程环境可以保证各个线程里的变量独立于其它线程里的变量。
     * 也就是说 ThreadLocal 可以为每个线程创建一个【单独的变量副本】，相当于线程的 private static 类型变量。
     */
    private static final ThreadLocal<String> CONTEXT_HOLDER = new ThreadLocal<>();

    /**
     * 决定使用哪个数据源之前需要把多个数据源的信息以及默认数据源信息配置好
     *
     * @param defaultTargetDataSource 默认数据源
     * @param targetDataSources       目标数据源
     */
    public DynamicDataSource(DataSource defaultTargetDataSource, Map<Object, Object> targetDataSources) {
        super.setDefaultTargetDataSource(defaultTargetDataSource);
        super.setTargetDataSources(targetDataSources);
        super.afterPropertiesSet();
    }

    @Override
    protected Object determineCurrentLookupKey() {
        return getDataSource();
    }

    public static void setDataSource(String dataSource) {
        CONTEXT_HOLDER.set(dataSource);
    }

    public static String getDataSource() {
        return CONTEXT_HOLDER.get();
    }

    public static void clearDataSource() {
        CONTEXT_HOLDER.remove();
    }
}
```

## 4 配置数据源切换 AOP

**数据源注解**

```java
@Documented
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface DataSource {
    String value() default DataSourceNames.ONE;
}
```

**AOP 配置，使用注解切换数据源**
```
@Aspect
@Component
/** 保证该AOP在@Transactional之前执行 */
@Order(-1)
@Log4j2
public class DataSourceAspect {

    /**
     * 切点: 所有配置 DataSource 注解的方法
     */
    @Pointcut("@annotation([注解类全限定名])")
    public void dataSourcePointCut() {
    }

    @Around("dataSourcePointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        MethodSignature signature = (MethodSignature) point.getSignature();
        Method method = signature.getMethod();
        DataSource ds = method.getAnnotation(DataSource.class);
        // 通过判断 DataSource 中的值来判断当前方法应用哪个数据源
        DynamicDataSource.setDataSource(ds.value());
        log.debug("set datasource is " + ds.value());
        try {
            return point.proceed();
        } finally {
            DynamicDataSource.clearDataSource();
            log.debug("clean datasource");
        }
    }
}
```

**在方法上加上 @dataSource(DataSourceNames.tow) 就可以切换到 tow 数据源**