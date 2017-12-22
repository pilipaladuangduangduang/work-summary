# Spring工作归纳    

标签（空格分隔）： 工作总结

---

## 在该笔记中总结工作开发过程中和Spring相关的内容：

### Spring中的EL表达式可以用于注解中
  
``` 
// 首先在properties中定义好cron表达式，在注解中直接通过 ${} 表达式引用
@Scheduled(cron = "${homepage.monitor.cron}")
``` 

### SpringMVC支持的REST

 > REST一定是面向资源、面向资源、面向资源的，重要的事情说三次。
 
### SpringMVC中使用Interceptor拦截器

SpringMVC中的Interceptor拦截器是相当重要和相当有用的，它的主要作用是拦截用户的请求并进行相应的处理。比如通过它来进行权限验证，或者是来判断用户是否登陆，或者是像12306 那样子判断当前时间是否是购票时间。

SpringMVC中Interceptor的两种实现方式：

 - 实现HandlerInterceptor接口，强制要求实现所有的方法；

 - 继承HandlerInterceptorAdapter抽象类，可以自由的实现方法。
 
``` java
// 到达Controller之前的方法处理
boolean preHandle(request, response, handler);
// Controller处理之后的后续操作
void postHandle(request, response, handler, modelAndView) throws Exception;
// preHandle方法返回true时，才会执行该方法，主要用于清理资源
void afterCompletion(request, response, handler, Exception ex) throws Exception;
``` 
 
### SpringMVC中的@RequestMapping不能重复

 > 在Controller中，@RequestMapping是不能重复的，即使一个修饰的是类，另一个修饰的是方法。

### Spring调用init方法

 > Spring中类初始化方法除了在配置文件中定义init-method，还可以直接在方法上使用注解<font color="FF2D2D">@PostConstruct</font>；
 
### Spring中开启异步任务

Spring中开启异步任务非常简单，一种是通过在applicationContext.xml中定义ThreadPoolTaskExecutor，另一种是通过@Async注解。

```
<!-- 定义ThreadPoolTaskExecutor -->
<bean id="taskExecutor" class="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor">  
   <property name="corePoolSize" value="10"/>  
   <property name="maxPoolSize" value="30"/>  
   // 这个类中属性的意义还请自行了解
</bean>
```
 
### Spring中使用@Async

今天在使用Spring中的@Async注解来执行异步操作，但总是没生效，经历一番搜索、调试终于得知原因并解决：

 - 对于使用了@Async的Bean，Spring容器在启动的时候扫包时，不能重复扫描该Bean，否则@Async会失效；

 - 在Bean中定义了@Async的方法，自身类在调用该方法时是不会异步执行的，需要通过代理类来调用来执行。

异步任务的配置信息：

``` 
<context:component-scan base-package="com.xingbook.admin.service.task" />
<!-- 引用Executor，使得@Async生效 -->
<task:annotation-driven executor="taskExecutor" scheduler="taskScheduler"/>
<!-- 8-16表示coresize为8，maxsize为16 -->
<task:executor id="taskExecutor" pool-size="8-16" queue-capacity="1024"/>
<task:scheduler id="taskScheduler" pool-size="8"/>
``` 

### Spring中@Async的小发现

``` 
@Component
public class AsyncClass {

    @Async
    void m1() {
    };
    
    @Async
    void m2() {
    }
    
    @Async
    void m3() {
        // 这里的m1()和m2()不会异步执行
        m1();
        m2();
    }

}
``` 
