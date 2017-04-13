# Spring Boot Data Source

[Reference: Working with SQL database](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html)

Spring中提供了SQL数据库的扩展，根据JDBC的方言【Direct】模板，使用`JdbcTemplate` 来完成关系数据库映射，如Hibernate；Spring Data中提供了额外的数据访问层功能，通过创建一个`Repository` 实现将Query转换成所需要的方法名。

## 1. 配置DataSource

Java中的`javax.sql.DataSource` 接口提供了标准的数据连接池对应方法，传统数据连接池则是使用`URL` 来实现，数据连接池可自定义，参考["How-to" Guide](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html#howto-configure-a-datasource)

### 1.1. 数据库嵌入支持





