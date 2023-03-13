---
title: ssm后台框架搭建
date: 2022-12-03 20:29:44
categories: j2ee
tags: 
    - ssm
description: 随着后台技术的更新换代，以前的后台框架ssh已经过时，目前大多使用ssm来搭建系统，这篇文章详细介绍了ssm框架的搭建过程。
---

SSM（Spring+SpringMVC+MyBatis）框架集由Spring、MyBatis两个开源框架整合而成（SpringMVC是Spring中的部分内容），常作为数据源较简单的web项目的框架。

## 前期准备

### 安装java运行环境
因为后续的 ide 和 maven 的运行都需要用到 java 运行环境
1. 下载jdk：[官网下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. 配置环境变量
在terminal编辑.bash_profile，输入下面的环境配置即可。
```
# 编辑配置文件
vim ~/.bash_profile

# java
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_45.jdk/Contents/Home
export CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.
export PATH=$JAVA_HOME/bin:$PATH:. 
```
3. 测试
```
# 启用配置文件
source ~/.bash_profile

# 测试查看java版本
java -version
```

### 安装idea
1. 下载安装：[下载地址](https:// www.jetbrains.com/)。注意推荐下载前一年的版本，方便后面破解。
2. 破解：[自行百度](www.baidu.com)

### 安装maven
1. 下载maven：[下载地址](https://maven.apache.org/download.cgi)
2. 解压放到指定目录下（比如：/Users/xxx/Library/Maven/）
3. 配置环境变量
```
# 编辑配置文件
vim ~/.bash_profile

# maven
export MAVEN_HOME=/Users/xxx/Library/Maven/apache-maven-3.8.6
export PATH=$PATH:$MAVEN_HOME/bin
```
4. 测试
```
# 启用配置文件
source ~/.bash_profile

# 测试查看java版本
mvn -v
```
5. 如果 mvn -v 命令得到的 maven 不是自己安装的版本，有可能是之前通过 brew 安装了 maven ，使用下面的命令删除即可。
```
brew uninstall maven
```
6. 配置本地仓库
- 新建本地仓库目录（比如：/Users/xxx/Library/Maven/repo）
- 将本地仓库目录和 maven 配置文件关联
```
# 修改 maven 配置文件，目录如下
$MAVEN_HOME/conf/settings.xml

# 配置本地仓库地址
<localRepository>/Users/xxx/Library/Maven/repo</localRepository>
```
7. 配置镜像
因为墙的原因，一般都需要在 maven 的配置文件里设置镜像。
```
<mirrors>
  <mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
  </mirror>
  <mirror>
    <id>ui</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://uk.maven.org/maven2/</url>
  </mirror>
  <mirror>
    <id>jboss-public-repository-group</id>
    <mirrorOf>central</mirrorOf>
    <name>JBoss Public Repository Group</name>
    <url>http://repository.jboss.org/nexus/content/groups/public</url>
  </mirror>
  <mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
  </mirror>
  <mirror>
    <id>OSChina</id>
    <name>OSChina Central</name>
    <url>http://maven.oschina.net/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
  </mirror>
  <mirror>
    <id>nexus-osc-thirdparty</id>
    <mirrorOf>thirdparty</mirrorOf>
    <name>Nexus osc thirdparty</name>
    <url>http://maven.oschina.net/content/repositories/thirdparty/</url>
  </mirror>
</mirrors>
```
8. 修改 idea 中 maven 的配置
- 进入修改项：Preferences -> Build,Execution,Deployment -> Build Tools -> Maven
- 修改 Maven home path 为 $MAVEN_HOME 目录
- 修改 User setting file 为 $MAVEN_HOME/conf/setting.xml 目录

## 开发项目

### 创建项目
1. idea -> file -> new -> project...
2. 选择spring iniliaizr -> next
3. 选择spring web库 -> next

### 运行
1. 创建一个 TestController 文件写入下面代码。
```
@RestController
public class TestController {
  @RequestMapping("/")
  public String index(){  return "Index"; }
}
```
2. 点击工具栏的 “run Application” 按钮运行。
3. 在浏览器输入localhost://8080/即可访问。

### 项目中数据库的使用（mysql+mybatis为例）
前提是你电脑已经安装了数据库服务器软件，我这里是mysql。没有安装的自行按钮，可通过这里给出的[mysql官方下载地址](https://dev.mysql.com/downloads/mysql/)自行下载安装
1. 在数据库中创建一条数据，用于项目访问。
- 开启数据库，mac os 是通过 system preferences -> mysql -> start mysql server 开启。
- 进入数据库，在 terminal 中输入如下命令访问
```
> mysql -u root -p
> 输入你的密码按回车
```
- 创建数据库数据
```
-- 创建数据库
create datebase `template`; 
-- 切换数据库
use `template`;
-- 创建表
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `name` varchar(20) DEFAULT NULL COMMENT '姓名',
  `password` varchar(100) DEFAULT NULL COMMENT '密码',
  `age` int DEFAULT NULL COMMENT '年龄',
  PRIMARY KEY (`id`)
) ENGINE=MyISAM AUTO_INCREMENT=1029 DEFAULT CHARSET=utf8;
-- 插入一条数据到表
insert into `user`(`id`, `name`, `password`, `age`) values (1, '魏辉辉', '123456', 18);
```

2. 在项目的pom.xml添加数据库相关库
```
<!--	mybatis 包含了 spring-boot-starter-jdbc	-->
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>2.2.2</version>
</dependency>
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
</dependency>
<!-- 数据库连接池 -->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.2.14</version>
</dependency>
```

3. 在项目的resources/config/application.properties中配置数据库信息
```
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url=jdbc:mysql://localhost:3306/[数据库名称]?serverTimezone=GMT%2B8
spring.datasource.username=[用户名]
spring.datasource.password=[用户密码]
```

4. 编写访问数据代码
- 在src代码目录创建 user 表对应的实体类 User.java，代码如下。（一般为了更好管理会创建对应模块的包名，比如完整的路径：com.weihh.template.user.bean.User.java）
```
/**
 *这里使用@data使用自动数据绑定第三方库lombok，请自行在pom.xml中添加lombok依赖：
 *<dependency>
 *  <groupId>org.projectlombok</groupId>
 *  <artifactId>lombok</artifactId>
 *</dependency>
 */
@Data
public class User implements Serializable {
  private Long id;
  private String name;
  private String password;
  private int age;
}
```
- 创建 user 表的数据库访问类 UserMapper.java，代码入下。(完整的路径：com.weihh.template.user.mapper.UserMapper.java)
```
@Mapper
public interface UserMapper {
  User getUserById(Long id);
}
```
- 创建mapper文件：UserMapper.xml，内容如下。(完整的路径：com.weihh.template.user.mapper.UserMapper.xml)
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.weihh.template.lru.mapper.UserMapper">
  <select id="getUserById" parameterType="java.lang.Long" resultType="com.weihh.template.lru.bean.User">
    select * from user where id=#{id}
  </select>
</mapper>
```
- 在pom.xml文件中设置mapper扫描
```
<build>
  ...
  <resources>
    <resource>
      <directory>src/main/java</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>true</filtering>
    </resource>
    <resource>
      <directory>src/main/resources</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
      </includes>
      <filtering>true</filtering>
    </resource>
  </resources>
</build>
```
- 创建 UserController.java 用来处理访问数据请求。(完整的路径：com.weihh.template.user.controller.UserController.java)
```
@RestController
@RequestMapping("/user")
public class UserController {
  // 理论上需要包一层UserService中间层去访问UserMapper，这里为了减少代码展示代码直接访问UserMapper
  private UserMapper userMapper;
  public UserController(UserMapper userMapper){ this.userMapper = userMapper; }
  // 浏览器输入localhost:8080//user/detail?id=1即可返回数据库数据
  @GetMapping("/detail")
  public User getUserById(Long id){ return userMapper.getUserById(id); }
}
```

### 项目中redis使用
1. 安装redis
- mac 电脑不建议使用brew安装redis，因为它对xcode版本有要求。推荐使用redis源码通过make编译自行安装。 [redis源码官网下载地址](https://redis.io/download/)。
- 下载后使用下面命令解压
```
# 解压
tar -zxvf redis-7.0.5

# 建议是将redis目录移至/usr/local目录下，为brew默认安装的位置
sudo mv redis-7.0.5 /usr/local
```
- 通过如下命令编译和安装
```
# 进入redis目录
cd redis-7.0.5
# make 编译
make
# 安装
make install
```
- 试用redis
```
# redis启动。默认前台启动，如果你想后台启动需要修改安装目录下的redis.conf文件，将daemonize no改为daemonize yes
redis-server /usr/local/redis-7.0.5/redis.conf
# 连接redis。
redis-cli -h 127.0.0.1 -p 6379
# 创建键值对（String类型）。注意设置值为String时，必须采用'"value"'的形式，如果直接时"value"会造成读取失败。
set userName '"weihh"'
# 创建键值对（对象类型）。
set user '{"userName": "weihh", "age": 32}'
# 通过key读取value。
get userName // 此时会输出"\"weihh\""
# 关闭redis
redis-cli shutdown
```
2. 项目用使用redis
- 添加pom.xml依赖。
```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```
- 配置redis连接（编辑application.properties文件）。
```
server.port=8088
spring.redis.host=127.0.0.1
spring.redis.port=6379
```
- 创建redis访问类（这里我们定义为 RedisUtil.java）。
```
@Component
@Data
public class RedisUtil {
  private RedisTemplate<String, Object> redisTemplate;
  @Autowired
  public RedisUtil(RedisTemplate<String, Object> redisTemplate) { this.redisTemplate = redisTemplate; }
  public String get(String key) {
    Object obj = key == null ? null : redisTemplate.opsForValue().get(key);
    null != obj ? obj.toString() : "";
  }
  public boolean set(String key, Object value) {
    try {
      if (value instanceof String) {
        redisTemplate.opsForValue().set(key, value);
      } else {
        redisTemplate.opsForValue().set(key, JSON.toJSONString(value));
      }
      return true;
    } catch (Exception e) { 
      e.printStackTrace(); return false; 
    }
  }
}
```
- 创建 redis 数据 json 化配置类（这里我们定义为 RedisConfig.java）。
```
@Configuration
public class RedisConfig {
  @Bean
  public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
    RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
    redisTemplate.setConnectionFactory(factory);
    // 配置序列化规则
    Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
    // 设置key-value序列化规则
    redisTemplate.setKeySerializer(new StringRedisSerializer());
    redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
    // 设置hash-value序列化规则
    redisTemplate.setHashKeySerializer(new StringRedisSerializer());
    redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
    return redisTemplate;
  }
}
```
- 创建访问 redis 的 controller。启动服务后我们在浏览器输入 /redisSetName 即会向redis写入数据。输入 /redisGetName 则会读取插入的数据。
```
@RestController
public class RedisController {
  @Autowired
  private RedisUtil redisUtil;
  @GetMapping("/redisSetName")
  public String redisSetName(){ return redisUtil.set("name", "weihh") + ""; }
  @GetMapping("/redisGetName")
  public String redisGetName(){ return redisUtil.get("name"); }
}
```

## 总结
本文介绍了ssm项目创建的过程。在项目中我们实现了项目架构的简单封装，架构中使用了mysql数据库和mybatis数据库orm框架，同时也运用了redis做数据缓存。
一个ssm项目基本完成，接下来我将分别对后端项目所用的技术做细分深入。比如spring boot技术、数据库技术、redis技术等，每个技术点都将单独出博文来介绍。

## 引用
1. [lombok官网](https://projectlombok.org/features/) 。
2. [redis官网](https://www.redis.net.cn/)，包含了软件下载、使用教程、命令介绍等。