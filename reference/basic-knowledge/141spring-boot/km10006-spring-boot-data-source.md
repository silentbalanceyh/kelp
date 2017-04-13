# KM10006 - Spring Boot Data Source

[Reference: 29. Working with SQL database](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html)

Spring中提供了SQL数据库的扩展，根据JDBC的方言【Direct】模板，使用`JdbcTemplate` 来完成关系数据库映射，如Hibernate；Spring Data中提供了额外的数据访问层功能，通过创建一个`Repository` 实现将Query转换成所需要的方法名。

## 1. 配置DataSource

Java中的`javax.sql.DataSource` 接口提供了标准的数据连接池对应方法，传统数据连接池则是使用`URL` 来实现，数据连接池可自定义，参考["How-to" Guide](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html#howto-configure-a-datasource)

### 1.1. 数据库嵌入支持

通常很方便的开发应用使用了嵌入式内存数据库【In-Memory Embedded Database】，实际上这种数据库没提供持久层存储，您需要在应用启动时填充您使用的数据库，并且在应用结束时将这些数据清除。

Spring Boot中支持的自动配置嵌入式数据库包括：H2、HSQL、Derby，您不需要提供任何链接URL，仅仅将您使用的依赖项构建到您使用的数据库中即可。

_**NOTES**：若您想要在测试中使用该功能，您需要在您使用的数据库中实现链接重用，针对不同的测试套件使用统一的上下文环境。若您想要在每一个上下文环境中使用分开的嵌入式数据库环境，则您需要设置：_`spring.datasource.generate-unique-name = true`

**例如**：您的POM文件如下：

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

_**NOTES**：您需要为每个嵌入式数据库配置_`spring-jdbc`_ 的依赖项，这个例子中使用了_`spring-boot-starter-data-jpa`_ 来实现。_

_**NOTES**：不论什么原因，您都需要配置嵌入式数据库中的链接URL，而且必须注意数据库的自动关闭功能被禁用。若您使用了H2，需要设置_`DB_CLOSE_ON_EXIT=FALSE`_ ；若您使用了HSQLDB，则您需要保证_`shutdown=true`_ 没使用。禁用数据库的自动关闭功能可以让Spring Boot在数据库关闭时控制数据库，因此确保数据库关闭时没有这种情况（自动关闭）发生。_

### 1.2. 连接生产环境数据库

生产环境数据库连接可直接通过`DataSource` 自动配置，这是选择连接的实现算法：

* 考虑到性能和并发，我们倾向于使用Tomcat中的`DataSource` ，所以它是一直选择的方案。
* 其次，若可以使用HikariCP，则优先选择。
* 其次选择Commons DBCP，但是我们不推荐在生产环境使用它，因为Commons DBCP已经是Deprecated的。
* 最后，若Commons DBCP2可用，则使用它。

若您使用`spring-boot-starter-jdbc` 或`spring-boot-starter-data-jpa` 时，您会自动获取`tomcat-jdbc` 的依赖项。

_**NOTES**：您可以略过完全的选择算法，而直接使用属性_`spring.datasource.type`_ 来配置，特别是当您的应用在Tomcat容器中使用了_`tomcat-jdbc`_ 作为默认连接池使用时这个配置特别重要。_

_**NOTES**：您也可以手动配置额外的连接池，若您定义了自己的_`DataSource`_ ，自动配置将不会发生。_

DataSource配置主要依赖外部配置`spring.datasource.*` 来控制，如：您在配置文件`application.properties` 中配置了下边代码片段：

```
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

_**NOTES**：您至少应该指定_`spring.datasource.url`_ 属性，否则Spring Boot将会自动配置嵌入式数据库。通常您也需要指定_`driver-class-name`_ ，因为Spring Boot将会根据_`url`_ 中的属性来推断该值。若要创建一个_`DataSource`_ 则您需要验证_`Driver`_ 是否可用，而且它在做其他事前会验证完成。若您设置_`spring.datasource.driver-class-name=com.mysql.jdbc.Driver`_ ，之后这个类将会被载入。_

您可参考`DataSourceProperties` 去查找更多支持选项，无论实际选项如何，都可以根据具体实现使用各自的前缀：`spring.datasource.tocmat.*` ，`spring.datasource.hikari.*` 和`spring.datasource.dbcp2.*` ，这些前缀需要参考特殊连接池实现文档指定细节。

如下边是Tomcat连接池的自定义配置：

```
# Number of ms to wait before throwing an exception if no connection is available.
spring.datasource.tomcat.max-wait=10000

# Maximum number of active connections that can be allocated from this pool at the same time.
spring.datasource.tomcat.max-active=50

# Validate the connection before borrowing it from the pool.
spring.datasource.tomcat.test-on-borrow=true
```

### 1.3. 连接JNDI DataSource

若您将Spring Boot应用部署到应用程序服务器中，您则想要使用应用程序服务器内置的方式来管理连接池如JNDI。

`spring.datasource.jndi-name` 属性可以用来替换`spring.datasource.url` ，`spring.datasource.username` 和`spring.datasource.password` 而指定JNDI位置访问`DataSource` 。如下边的`application.properties` 片段演示了在JBoss中访问JNDI。

```
spring.datasource.jndi-name=java:jboss/datasources/customers
```

## 2. 使用JdbcTemplate



