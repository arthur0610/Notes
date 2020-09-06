# Spring AOP

## 1. Spring的AOP简介

### 1.1 什么是AOP
AOP为`Aspect Oriented Programming`的缩写，意思为面向切面编程。

是通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式变成的一种衍生泛型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务各个逻辑部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

### 1.2 AOP的作用及其优势
- 作用：在程序运行期间，在不修改源码的情况下对方法进行功能增强
- 优势：减少重复代码，提高开发效率，并且便于维护

### 1.3 AOP的底层实现
常用的动态代理技术：
1. JDK代理：基于接口的动态代理技术
2. cglib代理：基于父类的动态代理技术

`spring-core`完成了`cglib`的集成。

### 1.4 JDK代理
`spring_aop/src/main/java/edu.zxie0018.proxy.jdk`

### 1.5 cglib代理
`spring_aop/src/main/java/edu.zxie0018.proxy.cglib`

### 1.6 AOP相关概念
Spring的AOP实现底层就是对上面的动态代理的代码进行封装，封装后我们只需要对需要关注的部分进行代码编写，并通过配置的方式完成指定目标的方法增强。

常用术语：
- Target：代理的目标对象
- Proxy：一个类被AOP织入增强后，就产生一个结果代理类。
- Joinpoint：连接点是指那些被拦截到的点。在Spring中，这些点指的是类中的方法，因为Spring只支持方法类型的连接点
- Pointcut：切入点是指我们要对哪些Joinpoint进行拦截的定义
- Advice：通知是指拦截到Joinpoint之后所要做的事情就是通知或增强
- Aspect：是切入点和通知的结合
- Weaving：织入是指把增强应用到目标对象来创建新的代理对象的过程。在Spring采用动态代理织入，而AspectJ采用编译期织入和类装载器织入


### 1.8 AOP开发明确的事项

#### 1.8.1 需要编写的内容
- 编写核心业务代码（目标类的目标方法）
- 编写切面类，切面类中有通知（增强功能方法）
- 在配置文件中，配置织入关系，即将哪些通知与哪些连接点进行结合

#### 1.8.2 AOP技术实现内容
Spring框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的代理对象，根据通知类型，在代理对象的对应位置。讲通知对应的功能织入，完成完整的代码逻辑运行。

#### 1.8.3 AOP底层使用哪种代理方式
Spring框架会根据目标类是否实现了接口来决定采用哪种动态代理方式。

### 1.9 知识要点
- aop：面向切面编程
- aop底层实现：基于JDK的动态代理 和 基于cglib的动态代理
- aop的重点概念：
    1. pointcut：切入点 被增强的方法
    2. advice：通知/增强 封装增强业务逻辑的方法
    3. aspect：切点+通知
    4. weaving：将切点和通知结合的过程
- 开发明确事项：
    1. 谁是切点（切点表达式配置）
    2. 谁是通知（切面类中的增强方法）
    3. 讲切点和通知进行织入配置

## 2. 基于XML的AOP开发
### 2.1 快速入门
1. 导入AOP相关坐标
    `pom.xml`
    ```xml
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.8.4</version>
    </dependency>
    ```
2. 创建目标接口和目标类 （内部有切点）
    `Target.java`
    ```java
    package edu.zxie0018.aop;

    public class Target implements TargetInterface {
        public void save() {
            System.out.println("Save()...");
        }
    }
    ```
3. 创建切面类 （内部有增强方法）
    `MyAspect.java`
    ```java
    package edu.zxie0018.aop;

    public class MyAspect {
        public void before() {
            System.out.println("Before()...");
        }
    }
    ```
4. 将目标类和切面类的对象创建权交给Spring
    `applicationContext.xml`
    ```xml
    <bean id="target" class="edu.zxie0018.aop.Target"></bean>

    <bean id="myAspect" class="edu.zxie0018.aop.MyAspect"></bean>
    ```
5. 在`applicationContext.xml`中配置织入关系
    `applicationContext.xml`
    ```xml
    <aop:config>
        <aop:aspect ref="myAspect">
            <aop:before method="before" pointcut="execution(public void edu.zxie0018.aop.Target.save())"></aop:before>
        </aop:aspect>
    </aop:config>
    ```
6. 测试代码
    `pom.xml`导入`spring-test`
    ```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.0.5.RELEASE</version>
    </dependency>
    ```
    `edu.zxie0018.test.AopTest.java`进行测试
    ```java
    package edu.zxie0018.test;

    import edu.zxie0018.aop.TargetInterface;
    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
    
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration("classpath:applicationContext.xml")
    public class AopTest {
    
        @Autowired
        private TargetInterface targetInterface;
    
        @Test
        public void test1() {
            targetInterface.save();
        }
    }
    ```

### 2.2 XML配置AOP详解
#### 2.2.1 切点表达式的写法
表达式语法：
```
execution([修饰符] 返回值类型 包名.类名.方法名(参数))
```
- 访问修饰符可以省略
- 返回值类型 包名 类名 方法名 可以使用 * 代表任意
- 包名与类名之间的一个点`.`代表当前包下的类，两个`..`代表当前包下及其子包下的类
- 参数列表可以使用两个点`..`表示任意个数，任意类型的参数列表

例子：
```xml
execution(public void edu.xxx.aop.Target.method())
```
`edu`包下的`xxx`子包下的`aop`子包下的`Target`类中的`method()`方法，并且这个方法返回值为`void`，并且这个方法没有`args`参数。
```xml
execution(void edu.xxx.aop.Target.*(..))
```
`edu`包下的`xxx`子包下的`aop`子包下的`Target`类中**任意**方法，并且有**任意**参数，但是要满足方法的返回值为`void`
```xml
execution(* edu.xxx.aop.*.*(..))
```
`edu`包下的`xxx`子包下的`aop`子包下的**任意**类的**任意**方法，并且有**任意**参数，不限定方法的范围值。
```xml
execution(* edu.xxx.aop..*.*(..))
```
`edu`包下的`xxx`子包下的**任意**子包下的**任意**类的**任意**方法，并且有**任意**参数，不限定方法的范围值。
```xml
execution(* *..*.*(..))
```
没任何意义 全局增强

#### 2.2.2 通知的类型
通知的配置语法：
```
<aop:通知类型 method="切面类中方法名" pointcut="切点表达式"></aop:通知类型>
```


名称 | 标签 | 说明
---|---|---
前置通知 | `aop:before` | 用于配置前置通知 指定增强的方法在切入点方法之前执行
后置通知 | `aop:after-returning` | 用于配置后置通知 指定增强的方法在切入点方法之后执行
环绕通知 | `aop:around` | 用于配置环绕通知 指定增强的方法在切入点方法之前和之后都执行
异常抛出通知 | `aop:throwing` | 用于配置异常抛出通知 指定增强的方法在出现异常时执行
最终通知 | `aop:after` | 用于配置最终通知 无论增强方法执行是否有异常都执行

`aop:around`
```java
public Object around(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("AroundBefore()...");
    Object o = pjp.proceed();
    System.out.println("AroundAfter()...");
    return o;
}
```
`<aop:around method="around" pointcut="execution(* edu.zxie0018.aop.*.*(..))"/>`


`<aop:after-throwing method="afterThrowing" pointcut="execution(* edu.zxie0018.aop.*.*(..))"/>`

`<aop:after method="after" pointcut="execution(* edu.zxie0018.aop.*.*(..))"/>`

#### 2.2.3 切点表达式的抽取
当多个增强的切点表达式相同时，可以将切点表达式进行抽取，在增强中使用`pointcut-ref`属性代替`pointcut`属性来引用抽取后的切点表达式。

```xml
<aop:pointcut id="myPointcut" expression="execution(* edu.zxie0018.aop.*.*(..))"/>            
<aop:around method="around" pointcut-ref="myPointcut"/>
<aop:after-throwing method="afterThrowing" pointcut-ref="myPointcut"/>
<aop:after method="after" pointcut-ref="myPointcut"/>
```

### 2.3 AOP知识要点
- AOP织入的配置
    ```xml
    <aop:config>
        <aop:aspect ref="切面类">
            <aop:before method:"通知方法名称" pointcut="切点表达式" />
        </aop:aspect>
    </aop:config>
    ```
- 通知的类型：前置通知，后置通知，环绕通知，异常抛出通知，最终通知
- 切点表达式的写法：
    ```
    execution([修饰符] 返回值类型 包名.类名.方法名(参数))
    ```

## 3. 基于annotation的AOP开发

### 3.1 基于注解的AOP开发步骤
1. 创建目标接口和目标类（内部有切点）
2. 创建切面类（内部有增强方法）
3. 将目标类和切面类的对象创建权交给Spring
    ```java
    @Component("target")
    public class Target implements TargetInterface {}
    ```
    ```java
    @Component("myAspect")
    public class MyAspect {}
    ```
4. 在切面类中使用注解配置织入关系
    ```java
    @Component("myAspect")
    @Aspect
    public class MyAspect {

        @Before("execution(* edu.zxie0018.aop.*.*(..))")
        public void before() {
            System.out.println("Before()...");
        }
    
        @AfterReturning("execution(* edu.zxie0018.aop.*.*(..))")
        public void afterReturning() {
            System.out.println("AfterReturning()...");
        }
    
        @Around("execution(* edu.zxie0018.aop.*.*(..))")
        public Object around(ProceedingJoinPoint pjp) throws Throwable {
            System.out.println("AroundBefore()...");
            Object o = pjp.proceed();
            System.out.println("AroundAfter()...");
            return o;
        }
    
        @After("execution(* edu.zxie0018.aop.*.*(..))")
        public void after() {
            System.out.println("After()...");
        }
    
        @AfterThrowing("execution(* edu.zxie0018.aop.*.*(..))")
        public void afterThrowing() {
            System.out.println("AfterThrowing()...");
        }
        
    }
    ```
5. 在配置文件中开启组件扫描和AOP的自动代理
    ```xml
    <context:component-scan base-package="edu.zxie0018.aop"/>
    <aop:aspectj-autoproxy/>
    ```
6. 测试
    


### 3.2 注解配置AOP详解

#### 3.2.1 注解通知的类型
通知的配置语法`@通知注解("切点表达式")`

名称 | 注解 | 说明
---|---|---
前置通知 | `@Before` | 用于配置前置通知 指定增强的方法在切入点方法之前执行
后置通知 | `@AfterReturning` | 用于配置后置通知 指定增强的方法在切入点方法之后执行
环绕通知 | `@Around` | 用于配置环绕通知 指定增强的方法在切入点方法之前和之后都执行
异常抛出通知 | `@AfterThrowing` | 用于配置异常抛出通知 指定增强的方法在出现异常时执行
最终通知 | `@After` | 用于配置最终通知 无论增强方法执行是否有异常都执行

#### 3.2.2 切点表达式的抽取
同XML配置AOP一样 可以将切点表达式抽取 抽取的具体方式是在切面内定义方法，在该方法上使用`@Pointcut`注解定义切点表达式，然后在增强注解中进行引用。

```java
//@AfterThrowing("execution(* edu.zxie0018.aop.*.*(..))")
@AfterThrowing("myAspect.myPointcut()")
public void afterThrowing() {
    System.out.println("AfterThrowing()...");
}

@Pointcut("execution(* edu.zxie0018.aop.*.*(..))")
public void myPointcut() {}
```

### 3.3 AOP注解开发知识要点
- 注解AOP开发步骤
    1. 使用`@Aspect`注解切面类
    2. 使用@通知注解标注通知方法
    3. 在配置文件中开启组件扫描和AOP自动代理     
    ```xml
    <context:component-scan base-package="edu.zxie0018.aop"/>
    <aop:aspectj-autoproxy/>
    ```
- 通知注解类型

名称 | 注解 | 说明
---|---|---
前置通知 | `@Before` | 用于配置前置通知 指定增强的方法在切入点方法之前执行
后置通知 | `@AfterReturning` | 用于配置后置通知 指定增强的方法在切入点方法之后执行
环绕通知 | `@Around` | 用于配置环绕通知 指定增强的方法在切入点方法之前和之后都执行
异常抛出通知 | `@AfterThrowing` | 用于配置异常抛出通知 指定增强的方法在出现异常时执行
最终通知 | `@After` | 用于配置最终通知 无论增强方法执行是否有异常都执行
