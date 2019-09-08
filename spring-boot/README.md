
# Spring Boot 

## 1. What is Spring Boot?
Spring Boot is basically an extension of the Spring framework which eliminated the boilerplate configurations required for setting up a Spring application. Spring Boot is designed to reduce boiler plate configuration providing you the shortest way to have a Spring web application up and running as quickly as possible, with minimal upfront configuration.

## 2. What are the features of Spring Boot
As per Spring Boot [website](https://spring.io/projects/spring-boot), it has following features:
1. Create stand-alone Spring applications
2. Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
3. Provide opinionated 'starter' dependencies to simplify your build configuration
4. Automatically configure Spring and 3rd party libraries whenever possible
5. Provide production-ready features such as metrics, health checks and externalized configuration
6. Absolutely no code generation and no requirement for XML configuration

## 3. What are the limitations of Spring Boot
1. You need to keep control on your app dependencies otherwise, it may unnecessarily increase the deployment binary size with unused dependencies.
2. It is time consuming process to migrate existing Spring Framework projects into Spring Boot.

## 4.  Spring Boot vs Spring MVC

| Spring MVC    | Spring Boot  | 
| ------------- | ---------- | 
|  **Spring MVC** is a complete HTTP oriented MVC framework managed by the Spring Framework and based on Servlets. | **Spring boot** is a utility for setting up applications quickly, offering an out of the box configuration in order to build Spring powered applications.|
| Takes significantly more time to configure and embedded server with jar or war| **Spring Boot** = **Spring MVC** + Auto Configuration(Don't need to write spring.xml file for configurations) + Server(You can have embedded Tomcat, Netty, Jetty server).|
| It requires to specify all dependencies and configuration | It avoids writing lots of boilerplate code, annotations and XML configuration.|

`Spring MVC` is a sub-project of the Spring Framework, targeting design and development of applications that use the MVC (Model-View-Controller) pattern. 

## 5.  Embedded Web Servers
-   For Servlet stack applications, the  `spring-boot-starter-web`  includes **Tomcat** by including  `spring-boot-starter-tomcat`,  **Jetty** -  `spring-boot-starter-jetty`  or **Undertow** - `spring-boot-starter-undertow`  instead.
-   For Reactive stack applications, the  `spring-boot-starter-webflux`  includes Reactor Netty by including  `spring-boot-starter-reactor-netty`, Tomcat, Jetty or Undertow.  Read [more](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-embedded-web-servers.html)

## 6. What is the default embedded server with Spring Boot?
Tomcat is default embedded server with Spring Boot which comes with `spring-boot-starter-web` as part of its dependency. Use Jetty instead of default Tomcat:
```xml
<properties>
	<servlet-api.version>3.1.0</servlet-api.version>
</properties>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<exclusions>
		<!-- Exclude the Tomcat dependency -->
		<exclusion>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
		</exclusion>
	</exclusions>
</dependency>
<!-- Use Jetty instead -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```
## 7.  How to disable Embedded Web Servers
Add following line in application.properties
`spring.main.web-application-type=none`

## 8. What are Spring Boot Starters?
Starters are a set of convenient dependency descriptors that you can include in an application. For example, if you want to get started using Spring and JPA for database access, you won't need to hunt through sample code and copy-paste loads of dependency descriptors. You can just include the `spring-boot-starter-data-jpa` dependency in your project. Read [more](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html#using-boot-starter)

## 9. Explain commonly used Spring Boot Annotations
1. **@EnableAutoConfiguration** - EnableAutoConfiguration attempts to automatically configure the Spring Application Context and beans that you are likely to need.. For example: If you have `SQLDB` on your classpath, and you have not manually configured any database connection beans, then Spring Boot auto-configures an in-memory database.
2. **@ComponentScan** It is used to specify the base packages to scan. It tells Spring that which packages should be managed by Spring. If you have a class annotated with `@Controller` which is in a package which is not scanned by Spring, you will not be able to use it as Spring controller. Even classes annotated with @Configuration are candidates for component scanning as @Configuration annotation is meta-annotated with @Component.
Example : 
	```java
	@ComponentScan({"com.my.package.first","com.my.package.second"})
	public class AppConfig 
	```
	or you can use  **type-safe**  _alternative_ to specifying a base-package location as a String
	```java
	@ComponentScan(basePackageClasses = {ExampleController.class, ExampleModel.class})
	public class AppConfig 
	```
3. **@Bean** - It tells Spring that a method produces a bean and that needs to be managed by the Spring container. By default, the bean name will be the same as the method name. It is a method-level annotation.<br/>
_Singleton_ scope is the default scope for a bean. It can be overridden by supplying the `scope` attribute to `@Bean` annotation
	```java
	@Configuration
	public class AppConfig {
	    @Bean
	    public NetworkService networkService() {
	        return new NetworkServiceImpl();
	    }
	    @Bean(scope=DefaultScopes.PROTOTYPE)
	    public Encryptor encryptor() {
	        // ...
	    }
	}
	```
	You can customize bean name. Check the example [here](https://docs.spring.io/spring-javaconfig/docs/1.0.0.M4/reference/html/ch02s02.html)

4.  **@Component** - It is used to indicate a class as a bean so at the time of component scanning, spring can auto-detect and auto-configure beans using classpath scanning. There’s an implicit one-to-one mapping between the annotated class and the bean (i.e. one bean per class). By default, the bean instance of this class has the same name as the class name with a lowercase initial. We can specify a different name using the optional _value_ argument of this annotation. It is a class level annotation.
5.  **@Repository** - It is a specialization of `@Component` . It indicates DAO component or Repository classes which represent the database access or persistence layer in an application.
6. **@Controller** -   It is also a specialization of `@Component`. It indicates that this class serves as a controller component in the presentation layer. When we add the `@Controller` annotation to a class, we can use another annotation i.e. `@RequestMapping` -  to map URLs to instance methods of a class. We can use `@RequestMapping` in only those methods whose classes are annotated with `@Controller` and it will NOT work with `@Component`, `@Service`, `@Repository` etc.
7. **@Service** -   It is also a specialization of `@Component`. The classes with business logic are annotated with this annotations and call methods in the repository layer. 
8. **@RestController** - This annotation is a specialized version of `@Controller` which itself is annotated with `@Controller` and `@ResponseBody`. So it automatically adds `@Controller` and `@ResponseBody` annotation automatically. So we do not have to add `@ResponseBody` to our request mapping methods. Read [more](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html)

9. **@Configuration** -  It indicates that a class declares one or more `@Bean` methods and may be processed by the Spring container to generate bean definitions and service requests for those beans at runtime. It's a replacement to the XML based configuration for configuring spring beans.
Example:
	```java
	@Configuration
	 public class AppConfig {
	     @Bean
	     public MyBean1 myBean1() {
	         // instantiate, configure and return bean
	     }
	     @Bean
	     public MyBean2 myBean2() {
	         // instantiate, configure and return bean
	     }
	 }
	```
10. **@SpringBootApplication** : It includes following annotations:
    1.  @EnableAutoConfiguration
    2. @ComponentScan
    3. @Configuration
    
    Read [more](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-using-springbootapplication-annotation.html)

11. **@Autowired** - It is used for automatic injection of beans on the setter method, constructor or a field.
12. **@Qualifier** - By default, Spring resolves entries for injection by its type. If there are more than one beans of the same type in the spring container, it will throw _NoUniqueBeanDefinitionException_.`@Qualifier` annotation is used to eliminate the issue of which bean needs to be injected.
	```java
	@Service("vehicleService")
	public class VehicleCustomService implements CustomService {
	   // class implimentation
	}
	
	@Service("applianceService")
	public class ApplianceCustomService implements CustomService {
	   // class implimentation
	}
	
	@Component
	public class DemoService {
	    @Autowired
	    @Qualifier("vehicleService")
	    private CustomService customService;
	}
	```
## 10.  What is Spring Initializr?
It is a web tool which is provided by Spring on official [site](https://start.spring.io). You can create basic Spring Boot project by providing required minimum details for example : language(Java, Groovy, Kotlin), Build tool (Gradle, Maven) etc.

## 11. What is Spring Boot CLI?
It is a command line tool that you can use to quickly prototype with Spring. It lets you run scripts. Read [more](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started-installing-spring-boot.html#getting-started-installing-the-cli)

## 12. Ways to configure Common application properties (application.properties)
Spring Boot properties can be specified inside `application.properties` or `application.yml` file, or as command line switches. Read [more](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)

## 13. Difference between application.properties and application.yml
Both are different ways to configure common application properties. YAML files can’t be loaded via the `@PropertySource` annotation. So if you need to load values from common properties, you need to use a properties file. Read [more](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-yaml-shortcomings)

## 14. Difference between application.properties and bootstrap.properties
_bootstrap.properties_ is required if you're using **_Spring Cloud_** and your application's configuration is stored on a remote Spring Cloud Config Server. It is loaded before the _application.properties_. You need to specify  config server URL in bootstrap.properties to fetch properties.
```
spring.application.name=app-name
spring.cloud.config.uri=${config.server:http://config-server/context}
```
_application.properties_  is specific to Spring Boot applications. Default location of  _application.properties_  is `
/src/main/resources/application.properties`

## 15. What is Spring Boot Actuator ?
Actuator is a plugin or extension to Spring Boot Autoconfigure with a number of additional features to help you monitor and manage your application in production. You can choose to manage and monitor your application by using HTTP endpoints or with JMX. Auditing, health, and metrics gathering can also be automatically applied to your application. Benefit of this is that we can get production grade tools without having to actually implement these features ourselves.
To add the actuator to a Maven based project, add the following ‘Starter’ dependency:
```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-actuator</artifactId>
	</dependency>
</dependencies>
```
## 16. What is Profile?
Application configuration in different kinds of environments, such as dev, QA, prod etc. will be different. We create different profiles to handle this issue and it follows the naming convention `application-{profile}.properties`. The name of the `.properties` files for these environment will be as following:
```
application-dev.properties
application-qa.properties
``` 
**Activate Profile:**
You can set a System property (`spring.profiles.active`) or an OS environment variable `SPRING_PROFILES_ACTIVE` to current profile to use. You can also launch your application with a `-D` argument (remember to put it before the main class or jar archive), as follow
`$ java -jar -Dspring.profiles.active=qa project-0.0.1-SNAPSHOT.jar`
or in Spring Boot, you can also set the active profile in  `application.properties`, as follows
`spring.profiles.active=production`


## 17. What is IoC and DI?
**Inversion of Control (IoC)**  - It’s a generic term and implemented in several ways (events, delegates, DI etc.). It means that objects do not create other objects on which they depend. Instead, they get the objects that they need from an outside source for example: a configuration file or framework.
**Dependency Injection (DI)** - DI is the process of providing the dependencies. It is a form of IoC, where implementations are passed into an object through constructors/setters/service lookup, usually by a framework component.

## 18. Inversion of Control(IoC) Container?
Interface `org.springframework.context.ApplicationContext` represents the Spring IoC container. It is responsible for instantiating, configuring, and assembling the aforementioned beans and managing their lifecycle. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. Check [here](https://docs.spring.io/spring/docs/3.2.x/spring-framework-reference/html/beans.html)

	
