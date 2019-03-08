# Spring Boot 日志框架


Spring Boot底层是Spring 框架, Spring默认使用的是JCL日志框架, Spring Boot选用的是SLF4J和logback日志框架

### SLF4j使用
##### 1. 如何在系统中使用SLF4j
- 在实际开发的时候, 日志记录方法的调用, 不应该直接调用日志的实现类, 而是应该调用日志抽象层里面的方法
- 给系统导入slf4j的jar和logback的实现jar

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```
![出自官方的关系图](/image/concrete-bindings.png)
- 每个日志的实现框架都有自己的配置文件, 使用slf4j以后, **配置文件还是要坐车日志实现框架自己本身的配置文件**

#### 2. 如何让系统中的日志都统一到slf4j
- 先把系统中其它日志框架排除出去
- 用中间包来替换原有的日志框架

![替换关系](/image/legacy.png)

如果我们要引入其他框架, 一定要把这个框架的默认日志依赖移除掉

​如: Spring框架用的是commons-logging；

```xml
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<exclusions>
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
```

**SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可**


#### 3. 日志文件的配置, 也可以分不同环境来执行
自定义配置文件
- logback.xml 直接被日志框架识别
- logback-spring.xml 日志框架不直接加载日志配置项, 由Spring Boot 解析日志配置, 可以使得配置环境对与不同的环境做出不同的配置

```java
<springProfile name="staging">
	<!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev | staging">
	<!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
	<!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```


[Spring Boot官方文档中对于日志框架的介绍](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-logging)
