# KM10012 - Spring Scopes

[Reference: 7.5 Beans Scope](http://docs.spring.io/spring/docs/4.3.7.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes)

Spring中的Scope是一个很关键的概念，简单说就是对象在Spring容器（IoC容器）中的生命周期，也可以理解为对象的创建方式。官方文档支持的Scope主要有下边几种：

| Scope | Description |
| :--- | :--- |
| singleton | 单例模式，一旦设置成singleton后，Spring IoC容器中只会存在一个共享的Bean实例，并且对所有的访问该Bean的请求都会返回同一个Bean实例。——这个Bean会在容器启动后被实例化，只要容器不重启或退出，这个实例会一直存活。 |
| prototype | 多例模式，Spring容器收到对它惯例的Bean请求过后，都会创建一个对应的Bean实例，当返回给请求方后，容器不在拥有该对象引用，也不负责对象销毁，而是让请求者来处理。 |
| request | 只用于Web程序，通常和XmlWebApplicationContext共同使用，Spring容器，即XmlWebApplicationContext会给每个Http请求创建一个RequestProcessor对象，请求结束后，该对象被销毁。有几个请求创建几个实例，和prototype差不多。 |
| session | 对于Web应用来说，放在Session中最普遍的就是用户的登陆信息，Spring容器会为每个Session（会话）创建一个UserPreference实例，比requesta存活时间更长。 |
| global session | 只用应用在基于prolet的Web应用程序中才有意义，它映射到prolet中global范围的session，如果普通的web应用中scope用到它，容器会把 |



