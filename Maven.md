# Maven 配置
## Maven介绍
项目管理和综合工具
提供了开发人员构建一个完整的生命周期框架

`简化和标准化`

Maven提供了开发人员的方式来管理：
- Builds
- Documentation
- Reporting
- Dependencies
- SCMs
- Release
- Distribution
- Mailing list

Maven简化和标准化项目建设过程。处理编译，分配，文档，团队协作和其他任务的无缝连接。Maven增加可重用性并负责建立相关的任务。

## Maven安装配置
想要安装Apache Maven在Windows 系统， 需要下载Maven的 zip 文件，并将其解压到你想安装的目录，并配置windows 环境变量

### 下载Apache Maven
[Maven 下载地址](http://maven.apache.org/download.cgi)

下载Maven的 zip 文件 解压到本地

### 添加MAVEN_HOME
前提：确保已安装JDK，并设置`JAVA_HOME`环境变量到windows环境变量

配置环境变量
![图1](https://note.youdao.com/yws/public/resource/62fb2d78f342eeddcef1e6a83991cfa7/xmlnote/2B361B0506FD431DA218087A8319E51F/108)


### 添加到环境变量 PATH
![图2](https://note.youdao.com/yws/public/resource/62fb2d78f342eeddcef1e6a83991cfa7/xmlnote/70E720CF51B94D3499E6D55E10FD6FD6/126)

`%MAVEN_HOME%\bin`

### 验证
使用命令：`mvn -version`

输出：
```
C:\>mvn -version
C:\
Apache Maven 3.6.1 (d66c9c0b3152b2e69ee9bac180bb8fcc8e6af555; 2019-04-05T03:00:29+08:00)
Maven home: D:\Java\OpenSource\apache-maven-3.6.1\bin\..
Java version: 1.8.0_211, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jre1.8.0_211
Default locale: en_US, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"

C:\>
```


## Maven本地仓库
Maven的本地资源库是用来存储所有项目的依赖关系（插件jar和其他文件，这些文件被Maven下载）到本地文件夹。很简单，当你建立一个Maven项目，所有相关文件将被存储在你的Maven本地仓库。

默认情况下，Maven的本地资源库默认为`.m2`目录文件夹：

- Unix/MacOS X: `~/.m2`
- Windows: `C:\Documents and Setting\{username}\.m2`

### 配置Maven 的本地库
通常情况下，可改变默认的`.m2`目录下的默认本地存储库文件夹到其他更有意义的名称，例如 maven-repo 

找到`{M2_HOME}\conf\setting.xml`，更新`localRepository`到其他名称。

```

46  <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
47          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
48          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
49  <!-- localRepository
50   | The path to the local repository maven will use to store artifacts.
51   |
52   | Default: ${user.home}/.m2/repository
53  <localRepository>/path/to/local/repo</localRepository>
54  -->
55  <localRepository>D:\Java\OpenSource\apache-maven-3.6.1\repo</localRepository>

55 是按照 53的格式配置 新的本地库

```

## Maven中央库
当你建立一个Maven项目，Maven会检查你的`pom.xml`文件，以确定哪些依赖下载。
首先，Maven将从本地资源库获得Maven的本地资源库依赖资源，如果没有找到，然后把它会从默认的Maven中央存储库 [http://repo1.maven.org/maven2/]查找下载。

`pom.xml`就是Maven在项目中的核心配置文件。
`github`上分享的开源项目 绝大多数（~~95%~~）都有`pom.xml` 

使用MVNrepository 搜索：http://mvnrepository.com/

## Maven依赖机制
在Maven依赖机制的帮助下自动下载所有必需的依赖库，并保持版本升级。让我们研究一个案例，以了解他是如何工作的。

假设你想使用Log4j作为项目的日志。这里你要做什么？
### 传统方式
- 访问 http://logging.apache.org/log4j/
- 下载Log4j的jar库
- 复制jar到项目类路径
- 手动将其包含到项目依赖
- 所有的管理需要一切由自己做
如果有Log4j版本升级，则需要重复上诉步骤一次。

### Maven方式
- 你需要知道log4j的Maven坐标，例如：
```
1   <groupId>log4j</groupId>
2   <artifactId>log4j</artifactId>
3   <version>1.2.17</version>
```
- 它会自动下载log4j的 1.2.17版本库
- 声明Maven的坐标转换成`pom.xml`文件
```
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```
- 当Maven编译或构建，log4j的jar会自动下载，并把它放到Maven本地存储库
- 所有由Maven管理


### 解释说明
看看有什么不同？那么到底在Maven发生了什么？

当建立一个Maven的项目，`pom.xml`文件将被解析，如果看到log4j的Maven坐标，然后Maven按此顺序搜索Log4j库：
- 在Maven的本地仓库搜索log4j
- 在Maven的中央存储库搜索log4j
- 在Maven远程仓库搜索log4j（如果在pom.xml中定义）

Maven依赖库管理是一个非常好的工具，为您节省了大量的工作


## Maven POM
POM代表项目对象模型。它是Maven工作中的基本单位，是一个XML文件。它始终保存在该项目基本目录中的 `pom.xml` 文件中。

POM包含的项目是使用Maven来构建的，它用来包含各种配置信息。

POM也包含了目标和插件。在执行任务或目标时，Maven会使用当前目录中的POM。它读取POM得到所需要的配置信息，然后执行目标。部分的配置可以在POM使用如下：
- project dependencies
- plugins
- goals
- build profiles
- project version
- developers
- mailing list

创建一个POM之前，应该要先决定项目组(groupId)，它的名字(artifactId)和版本，因为这些属性在项目仓库是唯一标识的。

### POM的例子
```
<!-- https://mvnrepository.com/artifact/org.apache.tuscany.sca/tuscany-base-runtime-pom -->
<dependency>
    <groupId>org.apache.tuscany.sca</groupId>
    <artifactId>tuscany-base-runtime-pom</artifactId>
    <version>2.0.1</version>
    <type>pom</type>
</dependency>
```

要注意的是，每个项目只有一个POM文件
- 所有的POM文件要项目元素必须有三个必填字段：groupId,artifactId,version
- 在库中的项目符号是: `groupId:artifactId,version`
- `pom.xml`的根元素是project，它有三个主要的子节点。


节点 | 描述
---|---
groupId | 这是项目组的编号，这在组织或项目中通常是独一无二的。例如，一家银行集团`com.company.bank`拥有所有银行相关项目
artifactId | 这是项目的ID。这通常是项目的名称。例如，`consumer-banking`。出了groupId之外，artifactId还定义了artifact在存储库中的位置。
version | 这是项目的版本。与groupId一起使用，artifact在存储库中用于将版本批次分离。例如：`com.company.bank:consumer-banking:1.0`，`com.company.bank:consumer-banking:1.1`

分为三个部分：
1. 项目自身信息
2. 项目运行所依赖的jar包信息
3. 项目运行环境信息 比如jdk tomcat信息


## Maven项目标准结构
```
src/main/java   //核心代码部分
src/main/resources  //配置文件部分
src/test/java   //测试代码部分
src/test/resources  //测试配置文件部分
src/main/webapp     //页面资源， js，css，图片等等
```

## Maven常用命令
```
$mvn clean
$mvn compile
$mvn test
$mvn package
$mvn install
$mvn deploy
```

## 以Maven的方式创建java项目
使用骨架`archetype`创建maven的java工程
```
new project -> maven
->
Create from archetype:
org.apache.maven.archetypes:maven-archetype-quickstart
```


```
// 需要手动建立 resources 文件夹 在 main和 test下
src/main/java -> Mark Directory as -> Sources Root
src/test/java -> Mark Directory as -> Test Sources Root

src/main/java -> New Directory "Resources" -> Mark Directory as -> Resources Root
src/test/java -> New Directory "Resources" -> Mark Directory as -> Test Resources Root
```

不使用骨架`archetype`创建maven的java项目
```
// 需要手动建议 resources 文件夹 仅仅在test下
src/main/java -> Mark Directory as -> Sources Root
src/test/java -> Mark Directory as -> Test Sources Root

src/test/java -> New Directory "Resources" -> Mark Directory as -> Test Resources Root
```

## 以Maven的方式创建javaweb项目
使用骨架`archetype`创建maven的javaweb工程
```
new project -> maven
->
Create from archetype:
org.apache.maven.archetypes:maven-archetype-webapp

```

```
//需要手动添加 java 文件夹在 src/main下
src/main/ -> New Directory "java" -> Mark Directory as -> Sources Root

```


## import other person's project

