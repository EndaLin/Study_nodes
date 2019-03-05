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

### Spring Boot 目录结构
![目录结构](/image/SpringBoot.png)
- 这里主要说resources文件夹下面的结构
> - static: 保存所以静态资源, 如js css images等等
> - templates: 保存所以模板页面, Spring Boot默认jar包使用嵌入式tomcat, 默认不支持JSP页面, 可以使用模板引擎(freemarker, 或者Spring Boot推荐的thymeleaf)
> - application.properties : Spring Boot 的配置文件, 可以修改一些默认配置

### Spring Boot配置文件
#### 1.配置文件
Spring Boot 使用一个全局的配置文件, 配置文件名是固定的
- application.properties
- application.yml **(推荐使用)**

配置文件的作用在于: 修改Spring Boot自动配置的默认值

#### 2.YAML基本语法
- 键值对的形式, 以空格来控制层级关系
- k:(空格)v
- 空格不能省, 大小写敏感
- 推荐使用YMAL来做配置文件, 例子
```yaml
server:
  port: 8081
```
- YAML value的写法值得需要注意的地方
> - 字符串默认不用加上单引号或者双引号
> - - 如果加上双引号, 则不会转义字符串里面的特殊字符, 如 "wt \n wt" 输出的是 "wt \n wt"
> - - 如果加上单引号, 则会转义字符串里面的特殊字符, 如 "wt \n wt" 输出的是 "wt 换行 wt"
> - 数组, 使用-值表示数组中的一个元素
> ```
> pets:
>  - cats
>  - dog
>  - pig
>```

#### 3. YAML配置文件值注入
- 此处举例子进行说明
- 首先编写YAML文件

```yml
person:
  name: wt
  age: 22
  maps: {k1: v1, k2: v2}
  dog:
    name: my_dog
    age: 2
```

- 导入依赖

```xml
<!--导入配置文件处理器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

- JAVABean

```java

/**
 * 将配置文件中配置的每一个属性的值,映射到这个组件中
 * @ConfigurationProperties 告诉Spring Boot 将本类中的所有属性和配置文件中的相关配置进行绑定
 * prefix = "person" 配置文件中哪一个属性进行绑定
 *
 * 只有这个组件是容器中的组件,才能使用容器提供的@ConfigurationProperties功能
 */

@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private String name;
    private Integer age;
    private Map<String, Object> maps;
    private Dog dog;

    ---
}
```
- JAVABean

```java
public class Dog {
    private String name;
    private Integer age;

    ---
}
```

- 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootSampleApplicationTests {

    @Autowired
    Person person;

    @Test
    public void contextLoads() {
        System.out.println(person);
    }

}

输出 Person{name='wt', age=22, maps={k1=v1, k2=v2}, dog=Dog{name='my_dog', age=2}}
```
#### 4. @Value 和 @ConfigurationProperties的区别
 | @ConfigurationProperties | @Value
:----------- | :-----------: | -----------:
功能         | 批量注入配置属性文件的属性        | 一个个指定
松散绑定(松散语法)         | 支持        | 不支持
SpringEL        | 不支持        | 支持
JR303数据校验         | 支持        | 不支持
复杂类型         | 支持        | 不支持

如果说, 我们只是需要在某个业务逻辑中获取一下配置文件的某个值, 则使用@Value
```java
@RestController
public class HelloController {

    @Value("${person.name}")
    private String name;

    @RequestMapping("/sayHello")
    public String sayHello() {
        return "hello " + name;
    }
}
```
