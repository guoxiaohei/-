```
＃THYMELEAF（ThymeleafAutoConfiguration）
 spring.thymeleaf.cache = true ＃启用模板缓存。
spring.thymeleaf.check-template = true ＃渲染前检查模板是否存在。
spring.thymeleaf.check-template-location = true ＃检查模板位置是否存在。
spring.thymeleaf.content-type = text / html ＃Content-Type值。
spring.thymeleaf.enabled = true ＃启用MVC Thymeleaf视图分辨率。
spring.thymeleaf.encoding = UTF-8 ＃模板编码。
spring.thymeleaf.excluded-view-names =＃逗号分隔的视图名称列表，应从解析中排除。
spring.thymeleaf.mode = HTML5 ＃应用于模板的模板模式。另请参见StandardTemplateModeHandlers。
spring.thymeleaf.prefix = classpath：/ templates / ＃前缀，用于在构建URL时查看名称。
spring.thymeleaf.suffix = .html ＃构建URL时后缀添加到视图名称。
spring.thymeleaf.template-resolver-order = ＃模板解析器在链中的顺序。
spring.thymeleaf.view-names = ＃可以解析的视图名称的逗号分隔列表。
```

```
mybatis.type-aliases-package = com.example.domain.model #实体类的包名
mybatis.mapperLocations = classpath:com/imooc/miaosha/dao/*.xml #xml配置文件的路径
mybatis.configuration.map-underscore-to-camel-case=true #下划线转换为驼峰
mybatis.configuration.default-fetch-size=100  #	为驱动的结果集获取数量（fetchSize）设置一个建议值。此参数只可以在查询设置中被覆盖。
mybatis.configuration.default-statement-timeout=3000 #等待响应时间
```

#datasource
spring.datasource.url=jdbc:mysql://localhost:3306/miaosha?useUnicode=true&characterEncoding=utf-8&allowMultiQueries=true&useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
#druid
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.filters=stat
spring.datasource.maxActive=2
spring.datasource.initialSize=1
spring.datasource.maxWait=60000
spring.datasource.minIdle=1
spring.datasource.timeBetweenEvictionRunsMillis=60000
spring.datasource.minEvictableIdleTimeMillis=300000
spring.datasource.validationQuery=select 'x'
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
spring.datasource.poolPreparedStatements=true
spring.datasource.maxOpenPreparedStatements=20

#redis
redis.host=192.168.200.128
redis.port=6379
redis.timeout=3
redis.password=123456
redis.poolMaxTotal=10
redis.poolMaxIdle=10
redis.poolMaxWait=3

#rabbit-mq

```
spring.rabbitmq.host=10.110.3.62
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.virtual-host=/
#消费者数量
spring.rabbitmq.listener.simple.concurrency= 10
spring.rabbitmq.listener.simple.max-concurrency= 10
#消费者每次从队列获取的消息数量
spring.rabbitmq.listener.simple.prefetch= 1
#消费者自动启动
spring.rabbitmq.listener.simple.auto-startup=true
#消费失败，自动重新入队
spring.rabbitmq.listener.simple.default-requeue-rejected= true
#启用发送重试
spring.rabbitmq.template.retry.enabled=true 
spring.rabbitmq.template.retry.initial-interval=1000 
spring.rabbitmq.template.retry.max-attempts=3
spring.rabbitmq.template.retry.max-interval=10000
spring.rabbitmq.template.retry.multiplier=1.0

```

