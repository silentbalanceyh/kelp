# KM10010 - Spring Boot中的测试

[Reference: Spring Boot Testing](http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-testing.html)

Spring Boot提供了一批Annotation注解帮助您测试应用，有两个模块支持测试功能：

* `spring-boot-test`包含了核心项；
* `spring-boot-test-autoconfigure`支持测试用例的自动配置；

大部分开发者仅仅需要使用`spring-boot-starter-test`项目，这个项目导入了上边两个项目：JUnit，AssertJ，Hamcrest等。

## 1. 测试依赖

若您在测试范围（scope = test）中使用了`spring-boot-starter-test`，下边的库是已经提供的：

* JUnit——标准Java应用单元测试框架
* Spring Test & Spring Boot Test——和Spring Boot应用集成的测试功能包
* 


