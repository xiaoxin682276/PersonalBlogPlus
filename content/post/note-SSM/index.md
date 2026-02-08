---
title: Spring框架面试题高频总结（问答实录）
description: 精炼易记的 Spring 高频面试 Q&A 总结
slug: spring-interview-qa
date: 2026-02-08 00:00:00+0000
image: spring-qa-cover.jpg
categories:
    - 开发框架
    - Spring
---

#### 1. Spring框架中的单例bean是线程安全的吗？
- **答：**
  不是线程安全的。当多个请求并发访问时，可能会修改单例bean的共享状态，导致线程安全问题。Spring不自动处理单例bean的线程安全，开发者需要自己保证。如果bean的状态是不可变的（如Service类或DAO类），则可以认为是线程安全的。如果bean有可变状态（如ViewModel对象），则需要手动处理线程安全，或者将bean的作用域从“singleton”改为“prototype”。

---

#### 2. 什么是AOP？
- **答：**
  AOP，即面向切面编程，在Spring中用于将那些与业务无关但对多个对象产生影响的公共行为和逻辑抽取出来，实现公共模块复用，降低耦合。常见的应用场景包括公共日志保存和事务处理。

---

#### 3. 你们项目中有没有使用到AOP？
- **答：**
  我们之前在后台管理系统中使用AOP来记录系统操作日志。主要思路是使用AOP的环绕通知和切点表达式，找到需要记录日志的方法，然后通过环绕通知的参数获取请求方法的参数，例如类信息、方法信息、注解、请求方式等，并将这些参数保存到数据库。

---

#### 4. Spring中的事务是如何实现的？
- **答：**
  Spring实现事务的本质是利用AOP完成的。它对方法前后进行拦截，在执行方法前开启事务，在执行完目标方法后根据执行情况提交或回滚事务。

---

#### 5. Spring中事务失效的场景有哪些？
- **答：**
  在项目中，我遇到过几种导致事务失效的场景：
    1. 如果方法内部捕获并处理了异常，没有将异常抛出，会导致事务失效。因此，处理异常后应该确保异常能够被抛出。
    2. 如果方法抛出检查型异常（checked exception），并且没有在@Transactional注解上配置rollbackFor属性为Exception，那么异常发生时事务可能不会回滚。
    3. 如果事务注解的方法不是公开（public）修饰的，也可能导致事务失效。

---

#### 6. Spring的bean的生命周期？
- **答：**
  Spring中bean的生命周期包括以下步骤：
    1. 通过BeanDefinition获取bean的定义信息。
    2. 调用构造函数实例化bean。
    3. 进行bean的依赖注入，例如通过setter方法或@Autowired注解。
    4. 处理实现了Aware接口的bean。
    5. 执行BeanPostProcessor的前置处理器。
    6. 调用初始化方法，如实现了InitializingBean接口或自定义的init-method。
    7. 执行BeanPostProcessor的后置处理器，可能在这里产生代理对象。
    8. 最后是销毁bean。

---

#### 7. Spring中的循环引用？
- **答：**
  循环依赖发生在两个或两个以上的bean互相持有对方，形成闭环。Spring框架允许循环依赖存在，并通过三级缓存解决大部分循环依赖问题：
    1. 一级缓存：单例池，缓存已完成初始化的bean对象。
    2. 二级缓存：缓存尚未完成生命周期的早期bean对象。
    3. 三级缓存：缓存ObjectFactory，用于创建bean对象。

---

#### 8. 那具体解决流程清楚吗？
- **答：**
  解决循环依赖的流程如下：
    1. 实例化A对象，并创建ObjectFactory存入三级缓存。
    2. A在初始化时需要B对象，开始B的创建逻辑。
    3. B实例化完成，也创建ObjectFactory存入三级缓存。
    4. B需要注入A，通过三级缓存获取ObjectFactory生成A对象，存入二级缓存。
    5. B通过二级缓存获得A对象后，B创建成功，存入一级缓存。
    6. A对象初始化时，由于B已创建完成，可以直接注入B，A创建成功存入一级缓存。
    7. 清除二级缓存中的临时对象A。

---

#### 9. 构造方法出现了循环依赖怎么解决？
- **答：**
  由于构造函数是bean生命周期中最先执行的，Spring框架无法解决构造方法的循环依赖问题。可以使用@Lazy懒加载注解，延迟bean的创建直到实际需要时。

---

#### 10. SpringMVC的执行流程？
- **答：**
      SpringMVC的执行流程包括以下步骤：
      1. 用户发送请求到前端控制器DispatcherServlet。
      2. DispatcherServlet调用HandlerMapping找到具体处理器。
      3. HandlerMapping返回处理器对象及拦截器（如果有）给DispatcherServlet。
      4. DispatcherServlet调用HandlerAdapter。
      5. HandlerAdapter适配并调用具体处理器（Controller）。
      6. Controller执行并返回ModelAndView对象。
      7. HandlerAdapter将ModelAndView返回给DispatcherServlet。
      8. DispatcherServlet传给ViewResolver进行视图解析。
      9. ViewResolver返回具体视图给DispatcherServlet。
      10. DispatcherServlet渲染视图并响应用户。

---

#### 11. Springboot自动配置原理？
- **答：**
      Spring Boot的自动配置原理基于@SpringBootApplication注解，它封装了@SpringBootConfiguration、@EnableAutoConfiguration和@ComponentScan。@EnableAutoConfiguration是核心，它通过@Import导入配置选择器，读取META-INF/spring.factories文件中的类名，根据条件注解决定是否将配置类中的Bean导入到Spring容器中。

---

#### 12. Spring 的常见注解有哪些？
- **答：**
      Spring的常见注解包括：
      1. 声明Bean的注解：@Component、@Service、@Repository、@Controller。
      2. 依赖注入相关注解：@Autowired、@Qualifier、@Resource。
      3. 设置作用域的注解：@Scope。
      4. 配置相关注解：@Configuration、@ComponentScan、@Bean。
      5. AOP相关注解：@Aspect、@Before、@After、@Around、@Pointcut。

---

#### 13. SpringMVC常见的注解有哪些？
- **答：**
      SpringMVC的常见注解有：
      - @RequestMapping：映射请求路径。
      - @RequestBody：接收HTTP请求的JSON数据。
      - @RequestParam：指定请求参数名称。
      - @PathVariable：从请求路径中获取参数。
      - @ResponseBody：将Controller方法返回的对象转化为JSON。
      - @RequestHeader：获取请求头数据。
      - @PostMapping、@GetMapping等。

---

#### 14. Springboot常见注解有哪些？
- **答：**
      Spring Boot的常见注解包括：
      - @SpringBootApplication：由@SpringBootConfiguration、@EnableAutoConfiguration和@ComponentScan组成。
      - 其他注解如@RestController、@GetMapping、@PostMapping等，用于简化Spring MVC的配置。

---

#### 15. MyBatis执行流程？
- **答：**
      MyBatis的执行流程如下：
      1. 读取MyBatis配置文件mybatis-config.xml。
      2. 构造会话工厂SqlSessionFactory。
      3. 会话工厂创建SqlSession对象。
      4. 操作数据库的接口，Executor执行器。
      5. Executor执行方法中的MappedStatement参数。
      6. 输入参数映射。
      7. 输出结果映射。

---

#### 16. Mybatis是否支持延迟加载？
- **答：**
      MyBatis支持延迟加载，即在需要用到数据时才加载。可以通过配置文件中的lazyLoadingEnabled配置启用或禁用延迟加载。

---

#### 17. 延迟加载的底层原理知道吗？
- **答：**
      延迟加载的底层原理主要使用CGLIB动态代理实现：
      1. 使用CGLIB创建目标对象的代理对象。
      2. 调用目标方法时，如果发现是null值，则执行SQL查询。
      3. 获取数据后，设置属性值并继续查询目标方法。
