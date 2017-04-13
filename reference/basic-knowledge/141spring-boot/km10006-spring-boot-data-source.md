# Spring Boot Data Source

[Reference: Working with SQL database](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html)

Spring中提供了SQL数据库的扩展，根据JDBC的方言【Direct】模板，使用`JdbcTemplate` 来完成关系数据库映射，如Hibernate；Spring Data中提供了额外的数据访问层功能，通过创建一个`Repository` 实现将Query转换成所需要的方法名。

## 1. 配置DataSource

Java中的`javax.sql.DataSource` 接口提供了标准的数据连接池对应方法，传统数据连接池则是使用`URL` 来实现，数据连接池可自定义，参考["How-to" Guide](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html#howto-configure-a-datasource)

### 1.1. 数据库嵌入支持

通常很方便的开发应用使用了嵌入式内存数据库【In-Memory Embedded Database】，实际上这种数据库没提供持久层存储，您需要在应用启动时填充您使用的数据库，并且在应用结束时将这些数据清除。

Spring Boot中支持的自动配置嵌入式数据库包括：H2、HSQL、Derby，您不需要提供任何链接URL，仅仅将您使用的依赖项构建到您使用的数据库中即可。

_NOTES：若您想要在测试中使用该功能，您需要在您使用的数据库中实现链接重用，针对不同的测试套件使用统一的上下文环境。若您想要在每一个上下文环境中使用分开的嵌入式数据库环境，则您需要设置：`spring.datasource.generate-unique-name = true`_

例如，您的POM文件如下：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.hsqldb</groupId>
    <artifactId>hsqldb</artifactId>
    <scope>runtime</scope>
</dependency>
```



