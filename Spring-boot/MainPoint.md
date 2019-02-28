# Spring boot

- @SpringBootApplication
> - 开启Spring的组件扫描和Spring Boot的自动配置
> - 组合了三个Spring的注解(@Configuration, @ComponentScan, @EnableAutoConfiguration)

- The @RestController and @RequestMapping Annotations
> - The @RequestMapping annotation provides “routing” information. It tells Spring that any HTTP request with the / path should be mapped to the home method.
> - The @RestController annotation tells Spring to render the resulting string directly back to the caller.
> - Both of them are not specific to Spring Boot, they also are Spring MVC annotations
- The @EnableAutoConfiguration Annotation
> - This annotation tells Spring Boot to “guess” how you want to configure Spring, based on the jar dependencies that you have added. For example,  spring-boot-starter-web added Tomcat and Spring MVC, the auto-configuration assumes that you are developing a web application and sets up Spring accordingly.
- The “main” Method
> - The final part of our application is the main method. This is just a standard method that follows the Java convention for an application entry point. Our main method delegates to Spring Boot’s SpringApplication class by calling run. SpringApplication bootstraps our application, starting Spring, which, in turn, starts the auto-configured Tomcat web server. We need to pass Example.class as an argument to the run method to tell SpringApplication which is the primary Spring component. The args array is also passed through to expose any command-line arguments.
> ```
>@SpringBootApplication
>public class RememberApplication {
>	public static void main(String[] args) {
>		SpringApplication.run(RememberApplication.class, args);
>	}
>}
>```

- Creating an Executable Jar for Spring Boot
> - First step: we need to add the spring-boot-maven-plugin to our pom.xml,
> such as :
> ```
><build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>```
> - Next, run **mvn package** from the command line
> - Then, you got the application, if you want to run it, use **jar -jar** as follows
> ```java -jar target/myproject-0.0.1-SNAPSHOT.jar```
> - if you want to exit, press the **ctrl-c**

- spring-boot-starter-parent. the parent project provides the following features
> - Java 8 as the default cmoplier level
> - UTF-8 source encoding
> - A Dependency Management section, inherited from the spring-boot-dependencies pom, that manages the versions of common dependencies. This dependency management lets you omit <version> tags for those dependencies when used in your own pom.
> - Sensible resource filtering for **application.properties** and **application.yml** including **profile-specific files (for example, application-dev.properties and application-dev.yml)**
> - Sensible plugin configuration (exec plugin, Git commit ID, and shade).**?**
- Importing Additional Configuration Classes
> - The **@Import** annotation can be used to import additional configuration classes
> - use **@ComponentScan** to automatically pick up **all** Spring components, including @Configuration classe
- Importing XML Configuration
> - We can start with **@configuration**, or use an @ImportResource to load XML
- Auto-configuration
> - You should only ever add one **@SpringBootApplication** or **@EnableAutoConfiguration** annotation. We generally recommend that you add one or the other to your primary **@Configuration** class only.
- Disabling specific Auto-configuration classes
> - user @Configuration and @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class}), not SpringBootApplication
