# KM10012 - Spring Scopes

[Reference: 7.5 Beans Scope](http://docs.spring.io/spring/docs/4.3.7.RELEASE/spring-framework-reference/htmlsingle/#beans-factory-scopes)

Spring中的Scope是一个很关键的概念，简单说就是对象在Spring容器（IoC容器）中的生命周期，也可以理解为对象的创建方式。官方文档支持的Scope主要有下边几种【除开singleton和prototype以外，其他五种都是基于Web应用的，只能在ApplicationContext中调用。】：

| Scope | Description |
| :--- | :--- |
| singleton | 单例模式，一旦设置成singleton后，Spring IoC容器中只会存在一个共享的Bean实例，并且对所有的访问该Bean的请求都会返回同一个Bean实例。——这个Bean会在容器启动后被实例化，只要容器不重启或退出，这个实例会一直存活。 |
| prototype | 多例模式，Spring容器收到对它惯例的Bean请求过后，都会创建一个对应的Bean实例，当返回给请求方后，容器不在拥有该对象引用，也不负责对象销毁，而是让请求者来处理。 |
| request | 只用于Web程序，通常和XmlWebApplicationContext共同使用，Spring容器，即XmlWebApplicationContext会给每个Http请求创建一个RequestProcessor对象，请求结束后，该对象被销毁。有几个请求创建几个实例，和prototype差不多。 |
| session | 对于Web应用来说，放在Session中最普遍的就是用户的登陆信息，Spring容器会为每个Session（会话）创建一个UserPreference实例，比requesta存活时间更长。 |
| global session | 只用应用在基于portlet的Web应用程序中才有意义，它映射到portlet中global范围的session，如果普通的web应用中scope用到它，容器会把它当做一般的session来看待。 |
| application | 可应用于整个ServletContext中的范围。 |
| websocket | 可应用于WebSocket中范围内。 |

_**NOTES**_**：**在Spring 3.0中，还有一种域是线程级（Thread Scope），但不是默认注册的，可查看SimpleThreadScoep的文档。若要创建自定义的Scope域，则可参考：[Custom Scope](https://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-scopes-custom-using)。

## 1. Singleton

Spring中的singleton和GoF中的singleton模式有一定的区别，GoF的单件模式是通过硬编码方式限定了这个对象实例的范围，它是类加载器级别的（每一个类加载器中只能创建这个类的一个对象）；Spring中的单件模式更好地描述了一个容器一个Bean，也就是说若您在单个Spring容器中定义了一个特殊类的单例，这个Spring容器仅仅会创建指定类的一个实例。——singleton是Spring容器中的默认范围。

XML的配置文件如下：

```xml
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```

## 2. Prototype

非单件模式，每次收到请求时都创建一个新的Bean，这个Bean可通过另外的一个Bean的`getBean()`方法在容器中获取，根据Spring最初的设计，所有带状态【Stateful】的对象使用prototype、而所有无状态【Stateless】的对象使用singleton。

&gt;

下边是prototype的XML配置片段：

```xml
<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>
```

和其他范围的Bean对比，Spring本身并不管理Prototype的完整生命周期：容器实例化、配置一起其他方式原型对象，然后将其交给客户端，然后再不会有和当前prototype的Bean相关的记录。因此，尽管在所有对象上调用初始化生命周期回调方法，无论其范围如何，若使用了prototype时，配置的销毁生命周期回调将不会被调用。客户端代码必须清理prototype类型的对象并释放Bean所有的昂贵资源。若要使Spring容器释放prototype的Bean占用的资源，则尝试使用自定义的Post-Processor，这个可清除掉Bean的引用。





