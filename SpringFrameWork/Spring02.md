# Spring02 IoC和DI注解开发

## 1. Spring配置连接池
### 1.1 数据源（连接池）的作用
- 数据源（连接池）是为了提高程序性能而出现的
- 事先实例化数据源，初始化部分连接资源
- 使用连接资源时从数据源中获取
- 使用完毕后将连接资源归还给数据源

常用的数据源： `DBCP`, `C3P0`, `BoneCP` 和 `Druid`
### 1.2 数据源开发流程
`c3p0`
```
ComboPooledDataSource dataSource = new ComboPooledDataSource();
dataSource.setDriverClass("com.mysql.jdbc.Driver");
dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
dataSource.setUser("root");
dataSource.setPassword("root");

Connection connection = dataSource.getConnection();
System.out.println(connection);

connection.close();
```
`druid`
```
DruidDataSource dataSource = new DruidDataSource();
dataSource.setDriverClassName("com.mysql.jdbc.Drvier");
dataSource.setUrl("jdbc:mysql://localhost:3306/test");
dataSource.setUsername("root");
dataSource.setPassword("root");

DruidPooledConnection connection = dataSource.getConnection();
System.out.println(connection);

connection.close();
```


#### 抽取jdbc.properties文件
`jdbc.properties`
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root
```

`c3p0`
```java
ResourceBundle rb = ResourceBundle.getBundle("jdbc");
String driver = rb.getString("jdbc.driver");
String url = rb.getString("jdbc.url");
String username = rb.getString("jdbc.username");
String password = rb.getString("jdbc.password");

ComboPooledDataSource dataSource = new ComboPooledDataSource();
dataSource.setDriverClass(driver);
dataSource.setJdbcUrl(url);
dataSource.setUser(username);
dataSource.setPassword(password);

Connection connection = dataSource.getConnection();
System.out.println(connection);

connection.close();
```

resources/jdbc.properties
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=root
```

### 1.3 Spring配置数据源
`pom.xml`
```
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
```
`applicationContext.xml`
```
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"></property>
    <property name="user" value="root"></property>
    <property name="password" value="root"></property>
</bean>
```

test `c3p0`
```
ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
DataSource dataSources = (DataSource) app.getBean("dataSources");
Connection connection = dataSources.getConnection();
System.out.println(connection);
connection.close();
```
same with `Druid`

### 1.4 抽取jdbc配置文件
applicationContext.xml中加载jdbc.properties配置文件获取连接信息

首先需要引入context命名空间和约束路径：
- 命名空间：`xmlns:context="http://www.springframework.org/schema/context"`
- 约束路径：`xsi:schemaLocation="http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"`

```
<!-- import local jdbc configuration file -->
<context:property-placeholder location="classpath:jdbc.properties"/>

<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"></property>
    <property name="jdbcUrl" value="${jdbc.url}"></property>
    <property name="user" value="${jdbc.username}"></property>
    <property name="password" value="${jdbc.password}"></property>
</bean>
```

#### Spring容器加载properties文件
```
<!-- import local jdbc configuration file -->
<context:property-placeholder location="classpath:jdbc.properties"/>
<property name="" value="${}"/>
```

## 2. Spring注解开发

### 2.1 Spring原始注解
Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率。

注解开发是一种趋势，注解替代xml配置文件可以简化配置，提高开发效率。

Spring的原始注解主要是替代`<Bean>`标签的配置


注解 | 含义
---|---
`@Component` | 使用在类上用于实例化Bean
`@Controller` | 使用在Web层类上的实例化Bean
`@Service` | 使用在Service层类上的实例化Bean
`@Repository` | 使用在Dao层类上的实例化Bean
`@Autowired` | 使用在字段上用于根据类型依赖注入
`@Qualifier` | 结合`Autowired`一起使用 用于根据名称进行依赖注入
`@Resource` | 相当于`@Autowired` + `@Qualifier` 按照名称进行注入
`@Value` | 注入普通属性
`@Scope` | 标注Bean的作用范围
`@PostConstruct` | 使用在方法上标注该方法是Bean的初始化方法
`@PreDestory` | 使用在方法上标注该方法是Bean的销毁方法


```java
//<bean id="userDao" class="edu.zxie0018.dao.impl.UserDaoImpl"></bean>
//@Component("userDao")
@Repository("userDao")
public class UserDaoImpl implements UserDao {
    ...
}
```

```java
//<bean id="userService" class="edu.zxie0018.service.impl.UserServiceImpl">
//    <property name="userDao" ref="userDao"></property>
//</bean>
@Component("userService")
public class UserServiceImpl implements UserService {

    //<property name="userDao" ref="userDao"></property>
    @Autowired
    @Qualifier("userDao")
    private UserDao userDao;
    
    ...
}
```

`@Value`
```java
@Value("${jdbc.driver}")
private String driver;
```


#### 组件扫描在`applicationContext.xml`中
`NoSuchBeanDefinitionException`

使用注解进行开发时，需要在`applicationContext.xml`中配置组件扫描，作用是指定哪个包及其子包下的bean需要进行扫描以便识别使用注解配置的类，字段和方法。
```
<!--    configure component scanner-->
<context:component-scan base-package="edu.zxie0018"/>
```

#### 注解方法和XML配置方法的差别
`set()`方法必须存在 使用XML配置方法

注解开发 可以不提供`setUserDao()`方法
```
//按照数据类型从Spring容器中进行匹配
@Autowired
//按照Bean_id从Spring容器中进行匹配 但是@Qualifier 和@Autowired结合使用
@Qualifier("userDao") 
private UserDao userDao;
```
也可以使用`@Resource`替换 `@Qualifier + @Autowired`
```
@Resource(name="userDao") // = @Qualifier + @Autowired
private UserDao userDao;
```



### 2.2 Spring新注解
使用Spring的原始注解并不能全部替代xml配置文件，还需要使用注解替代的配置如下：
- 非自定义的Bean的配置：`<bean class="com.alibaba.***>`
- 加载properties文件的配置：`<context:property-placeholder>`
- 组件扫描的配置：`<context:component-scan>`
- 引入其他文件：`<import>`


注解 | 含义
---|---
`@Configuration` | 用于指定当前类是一个Spring的配置类，当创建容器时会从该类上加载注解
`@ComponentScan` | 用于指定Spring在初始化容器时要扫描的包 作用和在Spring的xml配置文件中`<context:component-scan base-package="edu.zxie0018">`
`@Bean` | 使用在方法上 标注将该方法的返回值存储到Spring容器当中
`@PropertySource` | 用于加载.properties文件中的配置
`@Import` | 用于导入其他的配置类

`@Configuration`, `@ComponentScan`, `@Import`
```java
//标志该类是Spring的核心配置类
@Configuration
//<!--    configure component scanner-->
//<context:component-scan base-package="edu.zxie0018"/>
@ComponentScan("edu.zxie0018")
//引入其他文件 合集的形式
@Import({DataSourseConfiguration.class, })
public class SpringConfiguration {
    ...
}

```

`@PropertySource`, `@Bean`
```java
@PropertySource("classpath:jdbc.properties")
public class DataSourseConfiguration {
    @Value("${jdbc:driver}")
    private String Driver;
    @Value("${jdbc:url}")
    private String url;
    @Value("${jdbc:username}")
    private String username;
    @Value("${jdbc:password}")
    private String password;

    @Bean("dataSource")
    public DataSource getDataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
        dataSource.setUser("root");
        dataSource.setPassword("root");

        return dataSource;
    }
}
```

## 3. Spring整合Junit

### 3.1 原始Junit测试Spring的问题
在测试类中，每一个测试方法都有以下两行代码：
```
ApplicationContext app = new ClassPathXmlApplicationContext("bean.xml");
IAccountService as = app.getBean("accountService", IAccountService.class);
```

这两行代码的作用是获取容器，如果不写的话，直接会提示空指针异常。所有又不能轻易的删掉。

#### 解决思路
- 让SpringJunit负责创建Spring容器，但是需要将配置文件的名称告诉它
- 讲需要进行测试Bean直接在测试类中进行注入

### 3.2 Spring集成Junit步骤
1. 导入Spring继承Junit坐标 `pom.xml`
    ```
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.0.5.RELEASE</version>
    </dependency>
    ```
2. 使用`@Runwith`注解替代原有的运行期
    ```
    @RunWith(SpringJUnit4ClassRunner.class)
    ```
3. 使用`@ContextConfiguration`指定配置文件或者配置类
    ```
    @ContextConfiguration(value = "classpath:applicationContext.xml")
    ```
    OR
    ```
    @ContextConfiguration(classes = {SpringConfiguration.class})
    ```
4. 使用`@Autowired`注入需要测试的对象
    ```
    @Autowired
    private UserService userService;
    ```
5. 创建测试方法进行测试
    ```
    @Test
    public void test1() {
        userService.save();
    }
    ```

`SpringJunitTest.java`
```
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration(value = "classpath:applicationContext.xml")
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest {
    @Autowired
    private UserService userService;

    @Test
    public void test1() {
        userService.save();
    }
}
```


