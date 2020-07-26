# 什么是spring
Spring 是2003年兴起的一个轻量级的Java开源框架。Spring作为开源的中间件，提供了J2EE应用的各层的解决方案，Spring贯穿了表现层，业务层和持久层，而不是仅仅关注于某一层的方案。Spring并不想取代那些已有的框架，而是与它们无缝地整合。

Spring是一个轻量级**控制翻转**(loC)和**面向切面**(AOP)的**容器**框架。

# 为什么要使用Spring
1. 方便解耦，简化开发
    - 通过Spring提供的loC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合。
2. AOP编程的支持
    - 通过Spring提供的AOP功能，方便进行面向切面的编程，许多不容易用传统OOP实现的功能可以通过AOP轻松应付。
3. 声明式事务的支持
    - 通过声明式方式灵活地进行事务的管理，提高开发效率和质量。
4. 方便程序的测试
    - 方便程序的测试
5. 方便集成各种优秀框架
    - Spring并不排斥各种优秀的开源框架，相反，Spring可以降低各种框架的使用难度，Spring提供了各种优秀框架(Struts, Hibernate, MyBaits, Hessian, Quartz) 等的直接支持。
6. 降低Java EE API 的使用难度
    - Spring对很多难用的Java EE API(JDBC, JavaMail, 远程调用等) 提供了一个薄薄的封装层， 通过Spring的简易封装，这些Java EE API 的使用难度大为降低。

## 1. Bean
### 1.1 Bean标签的基本配置
用于配置对象交由Spring来创建。
默认情况下它调用的是类中的 无参构造函数。

基本属性：
- id: Bean实例在Spring容器中的唯一标识
- class: Bean的全限定名称

### 1.2 Bean标签取值范围
Scope: 指对象的作用范围


取值范围 | 含义
---|---
singleton | 默认值，单例的
prototype | 多例的
request | WEB 项目中, Spring创建的bean的对象并存入request
session | WEB 项目中, Spring创建的bean的对象并存入session
global session | Web项目中， 应用在protlet环境，如果没有protlet环境相当于存入session


当Scope的取值为singleton时

Bean的实例化个数为 一个

Bean的实例化时机是 当Spring核心文件被加载时，实例化配置的Bean实例

Bean的生命周期：
- 对象创建：当应用加载，创建容器时，对象就被创建了
- 对象运行：只要容器在，对象一直活着
- 对象销毁：当应用卸载，销毁容器时，对象就被销毁了

当Scope的取值为prototype时

Bean的实例化个数为 多个

Bean的实例化时机是 当调用getBean() 方法时实例化Bean

Bean的生命周期：
- 对象创建：当使用对象时，创建新的对象实例
- 对象运行：只要对象在使用时，就一直活着
- 对象销毁：当对象长时间不使用，被java的垃圾回收站回收了


### 1.3 Tag: init-method and destory-method

### 1.4 Bean实例化的三种方式
- 无参构造方法实例化
```
<bean id="userDao" class="edu.zxie0018.dao.impl.UserDaoImpl" scope="singleton" init-method="init" destroy-method="destory"></bean>
```
- 工厂静态方法实例化
```
<bean id="userDao" class="edu.zxie0018.factory.StaticFactory" factory-method="getUserDao"></bean>
```
- 工厂实例方法实例化
```
<bean id="factory" class="edu.zxie0018.factory.DynamicFactory"></bean>
<bean id="userDao" factory-bean="factory" factory-method="getUserDao"></bean>
```

### 1.5 Bean的依赖注入方式
怎么将UserDao注入到UserService内部
- 构造方法
UserServiceImpl.java
```
    private UserDao userDao;
    public UserServiceImpl() {
    }
    
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }
```

ApplicationContext.xml
```
<bean id="userDao" class="edu.zxie0018.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="edu.zxie0018.service.impl.UserServiceImpl">
    <constructor-arg name="userDao" ref="userDao"></constructor-arg>
</bean>
```

- set方法
UserServiceImpl.java
```
    private UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```

ApplicationContext.xml
```
<bean id="userDao" class="edu.zxie0018.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="edu.zxie0018.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
```

#### 更简单的set方法注入 p命名空间注入
需要在`ApplicationContext.xml`中 先引入P命名空间：
```
xmlns:p="http://www.springframework.org/schema/p"
```
然后 需要修改注入方式在`ApplicationContext.xml`
```
<bean id="userService" class="edu.zxie0018.service.impl.UserServiceImpl" p:userDao-ref="userDao"></bean>
```

### 1.6 Bean的依赖注入的数据类型
上面的操作都是注入的引用的Bean
除了对象的引用可以注入，普通数据类型和集合等都可以在容器中进行注入。

注入数据的三种数据类型：
- 普通数据类型
UserDaoImpl.java
```
    private String username;
    private int age;

    public void setUsername(String username) {
        this.username = username;
    }

    public void setAge(int age) {
        this.age = age;
    }
```

ApplicationContext.xml
```
<bean id="userDao" class="edu.zxie0018.dao.impl.UserDaoImpl">
    <property name="username" value="kk"/>
    <property name="age" value="18"/>
</bean>
```

- 引用数据类型
- 集合数据类型
UserDaoImpl.java
```
    private List<String> strList;
    private Map<String, User> userMap;
    private Properties properties;

    public void setStrList(List<String> strList) {
        this.strList = strList;
    }

    public void setUserMap(Map<String, User> userMap) {
        this.userMap = userMap;
    }

    public void setProperties(Properties properties) {
        this.properties = properties;
    }
```
ApplicationContext.xml
```
<bean id="userDao" class="edu.zxie0018.dao.impl.UserDaoImpl">
    <property name="strList">
        <list>
            <value>kk</value>
            <value>kkk</value>
            <value>kkkk</value>
        </list>
    </property>
    <property name="userMap">
        <map>
            <entry key="user1" value-ref="user1"></entry>
            <entry key="user2" value-ref="user2"></entry>
        </map>
    </property>
    <property name="properties">
        <props>
            <prop key="p1">p1</prop>
            <prop key="p2">pp22</prop>
            <prop key="p3">ppp333</prop>
        </props>
    </property>
</bean>

<bean id="user1" class="edu.zxie0018.domain.User">
    <property name="username" value="kk"></property>
    <property name="addr" value="kk'address"></property>
</bean>
<bean id="user2" class="edu.zxie0018.domain.User">
    <property name="username" value="kkk"></property>
    <property name="addr" value="kkk'address"></property>
</bean>
```

User.java
```
    private String username;
    private String addr;

    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getAddr() {
        return addr;
    }
    public void setAddr(String addr) {
        this.addr = addr;
    }
```

### 1.7 引入其他配置文件（分模块开发）
实际开发中 Spring的配置内容非常多
这就导致Spring的配置很繁杂且体积庞大

所以可以将部分配置拆解到其他的配置文件中，而在Spring 的主配置文件中通过`import`标签进行加载
```
<import resource="applicationContext-xxx.xml"/>
```

### 1.8 Spring的重点配置
```
<bean>标签
    id属性：在容器中Bean实例的唯一标识，不允许重复
    class属性：要实例化的Bean全限定名
    scope属性：Bean的作用范围，常用Singleton和prototype
    <property>标签：属性注入
        name属性：属性名称
        value属性：注入的普通属性值
        ref属性：注入的对象引用值
        <list>标签
        <map>标签
        <properties>标签
    <constructor-arg>标签
<import>标签：导入其他的Spring的分文件
```

## 2. Spring相关API
### 2.1 ApplicationContext的继承体系
applicationContext: 接口类型 代表应用上下文 可以通过其实例获得Spring容器中的Bean对象

### ApplicationContext的实现类
1. ClassPathXmlApplicationContext
    从类的根路径下加载配置文件 推荐使用
    ```
    ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
    ```
2. FileSystemXmlApplicationContext
    从磁盘路径上加载配置文件
    ```
    ApplicationContext app = new FileSystemXmlApplicationContext("PATH");
    ```
3. AnnotationConfigApplicationContext
    使用注解配置容器对象时 需要使用此类来创建spring容器（用来读取注解）
    ```
    ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
    ```
