# 探究Spring Boot

### POM 文件
#### 1.父项目
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

他的父项目是
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.1.1.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
这个父项目是真正管理Spring Boot应用里面所有依赖版本,
因为里面有一个dependency version, 为各种jar包定义了默认的版本号
```
Spring Boot的版本仲裁中心;
以后我们导入依赖默认是不需要写版本的, (在没有dependencies里面管理的依赖自然要声明版本号)

### 2.起步依赖
- Spring-boot-starter 场景启动器
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
> - 举个例子,***spring-boot-starter***-web,帮我们导入web应用开发所需要的所有依赖
> - Spring boot 把所有的功能场景都抽取出来,做成一个个starters(启动器), 只需要在项目里面导入这些starters, 所有相关的依赖都会导入进来
> - 一般需要什么功能就导入什么场景的启动器

### 主程序类,主入口类
```java
/**
 * SpringBootApplication 来标注一个主程序类, 说明这是一个Spring Boot 应用
 */
@SpringBootApplication
public class SpringbootSampleApplication {

    public static void main(String[] args) {

        SpringApplication.run(SpringbootSampleApplication.class, args);
    }
}
```
- @SpringBootApplication: Spring Boot应用标注在某个类上来说明这个类是Spring Boot的主配置类, Spring Boot就应该运行这个类的main方法来启动Spring Boot应用

```java
/**
*SpringBootApplication 部分源码
*/
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
```
- @SpringBootConfiguration : Spring Boot的配置类
- @EnableAutoConfiguration: 开启自动配置功能
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
```
- @AutoConfiguraPackage:**将主配置类(@SpringBootApplication标注的类)的所在包以及下面所有子包里面的所有组件都扫描到Spring容器中, 这一点要特别注意!!!**
> - 举个例子
> - ![SpringBootApplication](/image/SpringBootApplication.png)
