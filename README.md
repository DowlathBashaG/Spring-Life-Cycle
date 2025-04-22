# Spring-Life-Cycle & Other useful info's.


Qn 1:

LifeCycle of SpringBean in an ApplicationContext.

![Spring Lifecycle](https://github.com/user-attachments/assets/93a53da3-7a4f-4f8b-bf17-6a2de2787f64)


Ans :

![image](https://github.com/user-attachments/assets/06582cad-a0dd-47ff-a48b-e17bd65ab5a2)

1. Context is created.
2. Bean is created.
3. Bean is ready to use.
4. Bean is destroyed.

I.		Context is created:

		a. Beans definitions are created based on Spring Bean Configuration.	
		b. BeanFactoryPostProcessors are invoked.
						
II.		Bean is created :

		a. Instance of bean is created.
		b. Properties and dependencises are set.
		c. BeanPostProcessor::postProcessBeforeInitialization gets called.
		d. @PostConstruct method gets called.
		e. InitializingBean::afterPropertiesSet method gets called.
		f. @Bean(InitiMethod) method gets called.
		g. BeanPostProcessor::postProcessAfterInitialization gets called.
		
III.    Bean is ready to use.


IV.     Bean is destroyed.

		a. @PreDestroy method gets called.
		b. DisposableBean::destory method gets called.
		c. @bean(destroyMethod) method gets called.
		
		
Qn 2:

Scope of Spring Beans ?

Ans :

Scope 										Description

Singleton							Single Bean per spring container - Default

Prototype 							New Instance each time Bean is requested

Request 							New Instance per each HTTP Request

Session								New Instance per each HTTP Session

Application							One Instance per each ServletContext

Websocket							One Instance per each Websocket



Qn 3:

@Primary & @Qualifier ?

Ans :

@Autowired uses the following steps when resolving dependency.

1. Match Exactly by type. If only one found finish.

2. If multiple beans of same type found, check if any contains @Primary annotation, if yes, inject @Primary bean and finish.

3. If no exactly one match exists, check if @Qualifier exists for field, if yes use @Qualifier to find matching bean.

4. If still no exactly one bean found , narrow the search by using bean name.

5. If still no exactly one bean found, throw exception (NoSuchBeanDefinitionException , NoUniqueBeanDefinitionException ....


Qn. 4:

Why not allowed to annotate a final class with @Configuration? How do @Configuration annotated classes support singleton beans? 

Ans :

Class annotated with @Configuration can not be final because Spring will use CGLIB to create a proxy for @Configuration class. CGLIB creates subclass for each class that is supposed to be proxied. However since final class cannot have subclass CGLIB will fail. This is why methods cannot be final.

If @Configuration class will be final or will have final method,  Spring will throw BeanDefinitionParsingException.


Qn. 5:

How to you configure profiles? What are possible use cases.?

Ans:

Spring Profiles are configured by:

		1. Specifying which beans are part of which profile.

		2. Specifying which profiles are active.

Specify beans being part of profile in following ways:

1. Use @Profile annotation at @Component class level - bean will be part of profile / profiles specified annotation.

2. Use @Profile annotation at @Configuration class level - all beans from this configuration 
   will be part of profile / profiles specified in annotation.

3. Use @Profile annotation at @Bean method of @Configuration class - instance of bean return 
   by this method will be part of profile/profiles specified in annotation.

4. Use @Profile annotation to define custom annotation - @Component / @Configuration / @Bean
   method annotated with custom annotation will be part of profile/profiles specified in annotation.
   
Activate profiles in following way:

1. Programmatically with usuage of ConfigurableEnvironment.

2. By using spring.profiles.active property.

3. On Junit Test Level by using @ActiveProfiles annotation.

4. In Spring Boot programmatically by usuage of SpringApplicationBuilder

5. In Spring boot by application.properties or on yml level.


Qn. 6:

Can you use @Bean together with @Profile?

Ans:

Yes, @Bean annotation can be used together with @Profile inside class annotated with @Configuration annotation on top of method that returns instance of the bean.

If, method annotated with @Bean does not have @Profile, that beans that this bean will exists in
all profiles.

Note :

We can specifiy one,multiple profiles , or profile in which bean should not exists.

Example :

	@Profile("database")
	@Profile("!prod")
	@Profile({"database","file"})


    1.
	
	@Bean
	@Profile({"database","file"})
	public DataProcessor multiSourceDataProcessor(){
	   return new MultiSourceDataProcessor();
	}
	
	2. 
    
	@Bean
	@Profile("database")
	public DataReader dbDataReader{
	   return new DbDataProcessor();
	}
	
	3.
	
	@Bean
	@Profile("file")
	public DataProcessor fileDataReader(){
	   return new FileDataReader();
	}
	
	4. 
	
	@Bean
	@Profile("!prod")
	public DataWriter devDataReader(){
	   return new DevDataWriter();
	}
	
	5.
	
	@Bean
	@Profile("!dev")
	public DataWriter prodDataReader(){
	   return new ProdDataWriter();
	}
	
	

Qn. 7:

Can we use @Component together with @Profile?

Ans.

Yes,@Profile annotation can be used together with @Component on top of class representing spring bean.

If class annotated with @Component does not have @Profile, that beans that this bean wil 

exists in all profiles.

We can specify one,multiple profiles,or profile in which bean should not exists.

		1. @Profile("database")

		2. @Profile("!prod")

		3. @Profile({"database","file"})

Qn. 8:

What is a transaction? What is the difference between a local and a global transaction?

Ans.

Transaction is an operation that consist of series of tasks, in which all of those task should be 
performed, or none of the tasks should be performed. Those tasks are being treated as one unit of 
work. If all tasks in transaction are successful, changes made by those tasks are preserved, if at least one of the tasks is unsuccessful, changes made by tasks that were already completed will be reverted and any tasks awaiting execution will no be executed.

Transaction should follow ACID principle:

Atomicity   : All changes are applied or none changes are applied.

Consistency : System should go from one valid state to other valid state, any constraints
           
		      on data should never be left in invalid state.
			  
Isolation   : One transaction cannot affect other one, concurrent execution of transaction

              should leave system in the same state as if sequenctial exection of transaction
			  
			  would be performed.
			  
Durablility : Guarantees that if transaction has been committed, data will be preserved

              even in case of system/power failure.
			  

Qn. 9:

Is a transaction a cross cutting concern ? How is it implemented by String?

Ans.

Transaction is a cross cutting concern and in Spring it is implemented with usage of @Transactional annotation.

If @Transactional annotation is present on top of the method or entire class, then each call to the method in the class will be proxied by TransactionInterceptor and TransactionAspectSupport classes. Those classes will interact with PlatformTransactionManager to commit transaction upon successful method execution or rollback upon exception. Exact behavior will be dependent on transaction propagation and isolation level settings, which can be set in @Transactional annotation.

Qn. 10:

How are you going to define a transaction in Spring? What does @Transactional do? What is the 
PlatformTransactionManager?

Ans.

When method with @Transactional annotation is called, invocation is proxied by 

TransactionInterceptor & TransactionAspectSupport which are using PlatformTransactionManager to manage transaction.


Qn. 11:

How are you going to define a transaction in Spring? What does @Transactional do? What is the 
PlatformTransactionManager?

Ans.

@Transactional annotation allows you to configure following attributes.

		1. Transaction Manager 

		2. Propagation Type

		3. Isolation Level

		4. Timeout for Transaction

		5. Read only flag

		6. Define which exception types will cause transaction rollback

		7. Define which exception types will not cause transaction rollback

Qn. 12:

How are you going to define a transaction in Spring? What does @Transactional do? What is the PlatformTransactionManager?

Ans.

PlatformTransactionManager is an interface that is used by declarative Spring's APO Transaction 

Management to create, commit and rollback transactions.

PlatformTransactionManager contains following methods.


1. getTransaction - returns currently active transaction or create new one.

2. commit - commits transaction, or rolls back transaction if it was marked for rollback.

3. rollback - performs rollback of transaction.

Qn. 13:

What is a transaction isolation level? How many dowe have an how are they ordered?

Ans.

Transaction Isolation determines how changes made under one transaction are visible in other 

transactions and to other users of the system.

Higher isolation level means that changes from one transaction are not visible and lower isolation level means that changes from one transactions may "slip" into selects executed under 
other transaction.

Higher transaction isolation level make data being visible in more consistent way,lower transaction isolation level makes data less consistent but increases overall throughput and concurreny of the system.

Qn. 14:

What is a transaction isolation level ? how many do we have and how are they ordered?

Ans.

There are 3 challenges that may occur due to Transaction Isolation level.

1. Phantom Read

2. Non-repeatbale read

3. Dirty read


Qn. 15:  What does transaction propagation mean?


Ans.

Transaction propagation defines how existing transaction is re-used  when calling @Transactional method with transaction already running.


REQUIRED - support a current transaction, create a new one if none exists.

SUPPORTS - support a current transaction, execute non-transactionally if none exists.

MANDATORY - support a current transaction, throw an exception if none exists.

REQUIRED_NEW - create a new transaction, and suspend the current transaction if one exists.

NOT_SUPPORTED - execute non-transactionally, suspend the current transaction if one exists.

NEVER - execute non-transaction ,throw an exception if none exists.

NESTED - execute within a nested transaction if a current transaction if one exists


Qns. 16:  What happens if one @Transactional annotated method is calling another @Transactional annotated method on the same object instance?.

Ans.

JDK Proxy and CGLIB Proxy in spring beans AOP dont support self invocation. so when once method with @Transactional annotation calls differnt method with @Transactional annotation from the same class, nothing happens transcation interceptor will not be called.


Qns. 17:

Where can the @Transactional annotation be used ? what is the typical usage if you put it at class level?

@Transactional can be used on top of classes or method interfaces.

If used on top of method, it needs to have public access modifier, if used o top of protected / package-visible  / private method , transaction management will not be applied.

Qns. 18:

What is a Repository interface?

Ans.

Custom Repository interface needs to extends one of following interface.

1. Repository - basic marker repository

2. CrudRepository - adds generic methods for CRUD operations.

3. PagingAndSortingRepository - adds findAll methods for paging / sorting.

4. JpaRepository - JPA specific extendsion of repository.

Qns. 19:

What are the advantages of using spring boot?

Ans.

1. Maximizes Productivity.

2. Simplifies deployment. By allowing to creat executable jar, and also supports traditional 
   deployment on top of application server.

3. Provides automatic configuration which reduces boilerplate configuration and allows easy 
   customization when defaults are not sufficient.
   
4. Allows for dev/prod parity (12 factor).

5. Provides set of managed dependencises.

6. Provides Maven plugins.

7. Provides non-functional features externalized configuration,security , metrics ,health.

8. Integrates wtih Micro service architecture tools for building highly available and fault     
   tolerant applications. Eureka , Ribbon and OpenFeign.
   
9. Uses Ioc/ DI from spring framework.
   

Qns. 20:

Why is it opinionated?

Ans.

Spring boot is "opinionated" framework beacuse it comes with general idea on how application should be organized, provides default configurations and modules setups for technology related aspect of application. [ embedded databases, mvc view resolvers, template rendering engines ].

Opionionated has following advantages:

1. Simplifies application setup.

2. Maximizes productivity, by allowing you to focus on business code instead of setup of technology.

3. Allows you to write configuration only in case when defaults are not a good fit for your case.

4. Allows easy integration with technology modules(Embedded databases, containers).

5. Minimizes amount of setup code.

The main disadvantage of Opionionated framework is that,if you application does not fall into most use cases supported by framework, you will have to override most of default setup, configurations and project organization, which might harm your productivity.

Qns. 21:

What things affect what Spring Boot sets up?

Ans.

Spring Boot uses autoconfiguration to detect dependencises on the class path, based on detected 
dependencises, spring beans are configured to allow integraion with technologies, like JPA, Data Sources, Embedded databases, Template Rendering engines etc.

Spring Boot searches for META-INF/spring.factories on classpath that should contain entry org.springframework.boot.autoconfigure.EnableAutoConfiguration that lists all autoconfiguration classes provided by the autoconfiguration module.

Autoconfiguration class is using @CondionalOn... annotations to specify under which conditions, certain Autoconfiguration should be applied.

Spring Boot  provides starter modules, which are empty jars with set of dependencises specified with correct dependencises versions to allow easy start with the library.

Starter modeul may provide only set of dependencises, or set of dependencises with autoconfiguration code.

Qns. 22:

What is a Spring Boot starter POM? whi is it useful?

Ans.

Spring starter POM is a maven module that represents empty jar with set of dependencises required to work with specified technology. Spring starter may also provide autoconfiguration to create beans required to integrate project with technologies that you intend to use.

Qns. 23:

Spring Boot supports both properties and YML files. Would you recognize and understand them if you saw them?

Ans.

Spring boot allows you to externalize configuration for the application by using properties sotred in properties files that can be in format.

YAML
Java property file.

Yaml is a superset of JSNO and it is convenience for specifiying hierachical data. Spring boot supports yaml propertis with usage of SnakeYAML Library. Which is included by default by spring-boot-starter.

Qns. 24:

Where does Spring boot look for property file by default?

Ans.

Spring Boot looks for properties in following locations.

1. Profile Specific:

   Outside of Jar -> application-{profile}.properties / application-{profile}.yml
   
   Inside of Jar -> application-{profile}.properties / application-{profile}.yml

2. Application Specific:

   Outside of Jar -> application-{profile}.properties / application-{profile}.yml
   
   Inside of Jar -> application-{profile}.properties / application-{profile}.yml

Qns. 25:

Where does spring boot look fo property file by default?

Ans.

You can change name of default configuration file with usage of spring.config.name property.

$ java -jar myproject.jar --spring.config.name = myproject

You can also explicitly point location of configuration file with usage of spring.config.location

$ java -jar myproject.jar --spring.config.location= classpth:/default.properties

Qns. 26:

How do you access the properties defined in the property files?

Ans.

Spring Boot allows you to access properties defined in property files in following ways.

1. @Value("${PROPERTY_NAME}")
   
   Example:
   
   @Value("${app.propertyB}")
   private String propertyB;
   
2. @ConfigurationProperties

   You can define Data Object which will hold properties for defined prefix, you also need to register Configuration properties Data Object with usage of EnableAutoConfigurationProperties.
   
   a.
   
   @ConfigurationProperties(prefix = "app")
   @Getter
   @Setter
   public class Appconfiguration {
      private String propertyA;
	}
	
   b. 
   
   @SpringBootApplication
   @EnableAutoConfigurationProperties(Appconfiguration.class)
   public class SpringBootconsoleApplication implements CommandLineRuuner
   {
   .....
   }
   
3. Environment Property Resolver

   Inject and use Environment object.
   
   @Autowired 
   private Environment environment;
   
   environment.getProperty("app.propertyC");
   
Qns. 27.

What properties do you have to define in order to configure external MySQL?

Ans.

To configure external MYSQL in springboot  you need to sprcify URL, username and Password for datasource by defining following properties.


1.

spring.datasource.url = jdbc:mysqkL//localhost:3306/spring-tutorial
spring.datasource.username=
spring.datasource.password=


2. 

Optinally , you can explicitly specifi driver name.

spring.datasource.driver-class-name =com.mysql.cj.jdbc.Driver

3. 

To initialize database during application startup via data.sql and schema.sql you also need to specify property.

Ans:

spring.datasource.initialization-mode = always


4. 

You also need to specify connector depedency.

<dependency>
  <groupId> mysql </groupId>
  <artifactId>mysql-connector-java</artifactId>
</dependency>


5. You also need to access database , simplest approach is to use JDBC.


spring-boot-starter-data-jpa.


Qns. 28:

What isa fat jar? How is it differnt from the original jar?

Ans.

Fat jar,also called "executale jar", is a jar that contains compiled code for your application and also all dependencies. Spring Boot uses nested jars approach, that means that fat jar contains all dependencis as nested jars. This differs from other approach, which is uber jar that packs all dependencies into single jar archive. Uber jar approach is problematic because it is hard to  see application dependencies and also causes issues when same filename in the same context is used in different jars.

Fat jar is offen called "executable jar" becuase spring boot will generate MANIFEST.MF file which contains Main-Class and Start-Class entries together with JarLauncher code. This manifest together with launcher code will be used to execute standalone jar.

To create fat jar in your project, you need to use spring-boot-maven-plugin. Executing application is as simple as executing one command.

java -jar spring-boot-application-2.0-SNAPSHOT.jar

The differences in comparision to original jar are following.

1. Original jar does not contain all dependencies.

2. Original jar is not executable by default.

Qns. 29:

What is the difference between an embedded containder and a WAR?

Ans.

WAR ( Web Application Archive) is a file that represents web module. WAR cannot be executed in standalone mode, it needs to be deployed to Application Server like tomcat and wildfly.

Embedded container is used to exeucte execuatble jars. Embedded container is packed as dependency in executable jar and will be responsible for executing only single application. WAR approach on the other hand uses Application Server whcih might be used to execute multiple applications at the same time.


Qns. 30:

What embedded containers does spring boot support?

Ans.

Spring boot supports following embedded containers.

1. Tomcat
2. Jetty
3. Undertow

Tomcat is used as defautl embedded container, it will be automatically included when application is using spring-boot-starter-web.

To use Jetty Embedded Container, you need to exclude spring-boot-starter-tomcat and include spring-boot-starter-jetty.

pom.xml :

<dependencies>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
<exclusions>
   <exclusion>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-tomcat</artifactId>
   </exclusion>
  </exclusions>
</dependency>
<depedency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-jetty</artifactId>
</depedency>
</dependencies>


<build>
 <plugins>
   <plug>
       <groupId>org.springframework.boot</groupId>
	   <artifactId>spring-boot-maven-plugin</artifactId>
	   <executions>
	     <execution>
		   <goals>
		     <goal>repackage</goal>
		   </goals>
		</execution>
	   </excutions>
     </plugin>
    </plugins>
 </build>	


Qns. 31:

How does spring boot know what to configure?

Ans.

Spring boot knows what to configure by usage of Auto Configuration classes defined in starter modules. Spring boot searches for META-INF/spring.factories on classpath, whenever entry org.springframework.boot.autoconfigure.EnableAutoConfiguration is encountered in this file, Auto Configureation class pointed by this property is loaded.

Auto Configuration class is a regular @Configuration class annotated  with @ConditionalOn... annotation which specified under which conditions @Configuration class should be loaded.

When conditions from @CoditionalOn... annotation are matched, @Configuration class is loaded wich provides beans that integrates your application with specified technology.

Auto Configuration is often used with starter modules. Starter module provides set of dependencies, and optionally may provide Auto Configuration classes.


Qns. 32:

How does spring boot know what to configure?

Ans.

Spring boot supports following conditional annotations for AutoConfiguration classes.

1. ConditionalOnBean - presence of Spring Bean
2. ConditionalOnMissingBean - absence of spring bean
3. ConditionalOnClas - presence of class on classpath
4. ConditionalOnMissingClas - absence of class on classpath
 
  
   
    
Qns. 33:

What does @EnableAutoConfiguration do?

Ans.

@EnableAutoConfiguration annotation turns on auto-configuration of spring context. Autoconfiguration tries to guess spring beans that should be created for your application based on configured dependencies and configurations with @CondionalOn annotations.

When auto-configuration is turned on, Spring will search for META-INF/spring.factories on classpath, whenever entry org.springframework.boot.autoconfigure.EnableAutoConfiguration is encountered in this file, Auto Configuration class pointed by this property is loaded. When condition present on @ConditionalOn... annotation is matched, beans pointed out by this configuration care created.

@EnableAutoConfiguration annotation should be applied to your application @Configuration class, when using spring boot with @SpringBootApplication annotation, @EnableAutoConfiguration annotation is not required beacuse auto-configuration is turned on by default.

Qns. 34:

What does @SpringBootApplication do?

Ans.

@SpringBootApplication annotation is supposed to be used on top of the class and it was introduced for convenience. It (@SpringBootApplication) is equivalent to usage of following 3 annotations.

1. @Configuration - allows additional bean registration.

2. @EnableAutoConfiguration - enables context auto-configuration.

3. @ComponentScan - turns on scanning for @Component annotated classes.


Qns. 35:

Does spring boot do component scanning ? Where does it look by default?

Ans.

Yes , spring boot is performing component scan, because @SpringBootApplication annotation is enabling component scanning with usage of @ComponentScan annotation.

By default, spring booe will search for @Component annotated classes within the same root
package as @SpringBootApplication annotated class.

You can change this behavior by addming additional packages to scan with scanBasePackages or type-safe version of it scanBasePackagesClasses within @SpringBootApplication annotation.

Qns. 36:

How are DataSource and JdbcTemplate auto-confgured?

Ans.

DataSource and JdbcTemplate are confgured by Autoconfiguration classes defined in spring-boot-autoconfigure module.

DataSoruce is configured by DataSourceAutoConfiguration , JdbcTemplate is configured by JdbcTemplateAutoConfiguration.

DataSourceAutoConfiguration required some properties to be defined, example below shows 
MySQL confiugration.

spring.datasource.url=jdbc:mysql://localhostL3306/spring-tutorial
spring.datasource.usename=dowlath
spring.datasource.password=basha

Above properties will be injected into DataSourceProperties by the prefix spring.datasource and used by DataSourceAutoConfiguration.

After having Auto Configuration enabled by default in spring boot, configured proerties and database connector on your classpath, you can just use @Autowire to inject DataSource or JdbcTemplate.

Qns. 37:

What is spring.factories file for?

Ans.

spring.factories file located META-INF/spring.factories location on the classpath, is used by Auto configuration mechanism to locate Auto configuration classes.Each module that provides auto configuration class needs to have META-INF/spring.factories file with  org.springframework.boot.autoconfigure.EnableAutoConfiguration entry that will point Auto Configuration classes.

META-INF/spring.factories file is consumed by SpringFactoriesLoader class, which is used by AutoConfigurationInportSelector enabled by @SpringBootApplication annotation.

Each Auto Configuration class lists conditions in which it should be applied.

Qns. 38:

How do you customize spring auto configuration?

Ans.

You can customize spring auto configuration by creating your own auto configuration module with Auto configuration class.

To do that, you need to create java jar module which will contain META-INF/spring.factories file that contains,
org.springframework.boot.autoconfigure.EnableAutoConfiguration entry whcih points to your auto configuration class.

Autoconfiguration is a class annotated with @Configuration annotation, usually used together with @ConditionalOnClas annotation. Additionally you can use @PropertyScan annotation with @EnableConfigurationProperties and @ConfigurationProperties annotations to introduce custom properties for your auto-configuration module.

Inside Auto Configuration class you should have @Bean annotated methods, which will provide configured beans when @ConditionalOnClass is met.

Step 1:

mysql.properties

   mysql.host=localhost
   mysql.port=3306
   mysql.dbname=spring-tutorial
   mysql.username=spring-tutorial
   mysql.password=spring-tutorial
   
Step 2:

Enable AutoConfiguration

Step 3:

MySqlAutoConfiguration.java


@Configuration
@EnableAutoConfigurationProperties(MySqlProperties.class)
@ConditionalOnClass({com.mysql.cj.jdbc.Driver.classs})
@PropertySource("classpath:mysql.properties")
public class MySqlAutoConfiguration{

@Autowired
private MySqlProperties mysqlproperties;

....
....

Qns. 39:

What are the two protocols you can use to access actiatpr endpoints?

Ans.

SpringBoot acutator supports two protocols.

1. HTTP
2. JMX

HTTP endpoints can be access by any HTTP Client, like CURL or Web Browser.

1. /actuator/info

2. /actuator/health

Qns. 40:

What is info endpoint for? How do you supply data?

Ans.

SpringBoot Acutuator info endpoint is used to provide arbitrary,non-sensitive,custom defined data, available at runtime that can provide additional information about started application.

info end point is exposed by default via protocols.

1. HTTP at /actuator/info
2. JMX at org.springframework.boot/Endpoint/Info

Info endpoint is usually used to expose information like,

1. Java Runtime used
2  Application name , description, version
3. Git information -see git-commit-id-plugin

   Branch
   Tag
   Commit ID
   
Qns. 41:

What is info endpoint for? How do you supply data?

Ans.

You can supply data to spring boot by using following methods.

1. With Usage of property files,by definig info.* properties.

info.app.name = Spring Boot Application
info.app.description = Actuator
info.app.version=1.0.0
info.java-vendor= &{java.specification.vendor}


2. By implmenting InfoContributor bean

@Component
public class SystemNameInfoContributor implments InfoContributor{
@Override
public void contribute(Info.Builder builder){
builder.withDetail("system-name",System.getProperty("os.name"));
}
}


Qns. 42:

What is @RequestParam used for?

Ans.

@RequestParam is used to bind web request parameters to controller method parameter.

/index?name=Dowlath&city=NYC&country=Usage


@GetMapping("/index")
public String index(@RequestParam("name") String name,@RequestParam("city") String city, @RequestParam("country") String country){
....
}

Because Servlet API compines query parameter and form data into a single parameers map, it is possible to use @RequestParam annotation to map:

1. query parameters
2. form data
3. parts in multipart requests.

@RequestParam also supports additional use cases, like:

1. Mapping all requests parameters to Map.

   /inded?name=Dowlath&city=NYC&country=US
   
   @GetMapping("/index")
   public String index(@RequestParam Map<String,String> parameters){
   ....
   }
   
2. Mapping all values to List

   /index?cities=1,2,3
   
   @GetMapping("/index")
   public String index(@RequestParam("cities") List<String> cities){
   ...
   }
   
Qns. 43:

What are the differences between @RequestParam and @PathVariable?


Ans.

The main difference between @RequestParam and @PathVariable is a purpose of each annotation.

1. @PathVariable is repsonsible for mapping parts of URI, marked with usuage of URI templates variables to controller method parameters. URI templates are identifiers surronded with curly brackets.

/countries/US/cities/DEN

@GetMapping("/countries/{country}/cities/{city}")
public String countryAndCityByCode(@PathVariable("country") String country, @PathVariable(value="city") String city){
...
}


2. @RequestParam is used to bind web request parameters to controller method parameter.

/index?name=Johne&city=NYC&country=US
   
@GetMapping("/indxe")
public String index(@RequestParam("name") String name,@RequestParam("city"),@RequestParam("country") String country){
...
}


Qns. 44:

What are authentication and authorization ? which must come first?

Ans.

Authorization is a process of determining whether an autheticated user is allowerd to access certain resources within the system or allowed to perform  a certain action within the application.

In other words, authorization answers question "What are you allowed to do?"

Spring security allows you to implement authorization within your application on two levels:

1. Web Security level with usage of expression.

   mvcMatchers("/admin/**").hasRole("ADMIN")
   
2. Method Security Level with usage of:

   @Secured annotation
   @PreAuthorize annotation
   @RolesAllowed
   @PermitAll
   @DenyAll
   
Access Control Rules can be expressed :

1. Roles:

   Represents a high level set of privilegs. ROLE_ADMIN , ROLE_STAFF
   
2. Authorities:

   Represents a low-level.
   
Qns. 46:

How to create customized annotation?

Ans.

Steps followed here.

1. @Target(ElementType.METHOD)
2. @Retention(RetentionPoliyc.RUNTIME)
3. public @interace myAnnotation{
   }

Qns. 46:

Is security a cross cutting concern? How is it implemented internally?


Ans.

Security in spring is implemented on two levels:

1. Web Level - based on servlet filters.
2. Method security level - based on spring aop.

Each aspect of security - Authentication and Authorization is handled on both of those levels with different set of components:

Authentication:

1. AuthenticationManager
2. ProviderManager
3. AuthenticationProvider
4. UserDetailsService.

Authorization:

1. AccessDecisionManager -> AccessDecisionVoter
2. AfterInvocationManager
3. Authorities.
   
To enable method level security you need to use :

@EnableGlobalMethodSecurity annotation and enable support to one of annotation types.

prePostEnabled - security pre post annotations - @PreAuthorize

securedEnabled - @Secured annotation - spring security's @Secured annotations.

AuthenticationManager:

API that defines how spring security's filters perform authentication,usually implemented by ProviderManager.

ProviderManager:

ProviderManager is an AuthenticationManager that delegates to list of AuthenticationProvders , it at least AuthenticationProvider will successfully authenticate user, user is logged into the system.

Qns. 47:

What is the security filter chain?

Ans.

Security Filter chain is a collection of spring managed filters that are responsible for authentication and authorization. Usually they include functionalities like username and password authentication.  Logout Management , Session Management , security interceptors etc.

Spring Provides API to customize list of used security filters, by extending WebSecurityConfigurerAdapter and overriding configure method.

Example :

@Configuration 
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter{

@Override
public void configure(WebSecurity web) throws Exception{
...
}

@Override
protected void configure(HttpSecurity http) throws Exception{
...
}

@Override
protected void configure(AuthenticationManagerFilter auth) throws Exception{
...
}

Qns. 48:

What is a security context.?

SecurityContext is an interface, allowing you to access security information associated with the current thread of execution,interface provides two methods.

1. getAuthentication - provides currently authenticated principal, or an authentication request token.

2. setAuthentication - sets currently authenticated principal, or removes the authentication.

SecurityContext can be accessed via SecurityContextHolder.

Qns. 49:

What are the deployment startegies in K8s.?

Ans.

The following are some common deployment strategies.

1. Re-creating deployment
2. Rolling deployment
3. StatefulSet deployments
4. Blue-green deployment
5. Serverless deployments
6. Canary deployments

Qns. 50:

Failure / Fault tolerant & MSA:

Failure-Tolerant Patterns: Circuit Breaker, Retry with Backoff, and Rate Limiter
To enhance system resilience in the face of failures, employ the following patterns:

Circuit breaker

The circuit breaker pattern prevents a cascade of failures in distributed systems. When a particular service or operation fails to a threshold level, the circuit breaker “trips,” and further attempts to invoke the failing service are halted temporarily, thus allowing it to recover and maintain system stability.

Retry with backoff

Retry with backoff involves reattempting failed operations with increasing delays between attempts, increasing the interval (backoff) progressively with each retry until a maximum number of retries is reached. This pattern is particularly useful when handling transient faults by providing time for the issue to resolve before retrying.

Rate limiter

The rate limiter pattern controls the number of requests to a service within a specified time frame, preventing the system from being overwhelmed by a high volume of traffic or sudden spikes in demand. Rate limiting can be applied at various levels: per user, per service, or globally across a system. This pattern ensures that services continue to operate smoothly even under heavy load, reducing the likelihood of degraded performance or system crashes due to resource exhaustion.

These patterns work together to prevent cascading failures in distributed systems, ensuring that failures in one part of the system do not lead to widespread outages.


Domain-Based Patterns: Domain-Driven Design and Decompose by Subdomains

The following patterns focus on aligning software design with business objectives:

Domain-driven design (DDD)

DDD focuses on the core domain logic and its complexity, basing the design on the domain’s behavior and logic. This pattern encourages the creation of a domain model that incorporates business rules and behaviors, leading to a more intuitive correlation between the software and the business requirements it aims to fulfill.

Decompose by subdomains

Decompose by subdomains builds on DDD in a microservice architecture to divide a system into subdomains, each handling a specific business capability. Each microservice manages a subdomain, which improves maintainability and scalability by allowing the microservice to evolve independently based on its unique business requirements.

These patterns are essential for building modular, scalable systems that can evolve as business needs change.
   
Other Cloud Architecture Patterns

In addition to the essential patterns, several other cloud architecture patterns address specific challenges and enhance system design:

Anticorruption layer

The ACL serves as a protective barrier that prevents potentially disruptive data or requests from entering a system segment, thereby preserving the integrity of an application’s core business model. It acts as a translator between different subsystems or external applications, ensuring that data exchanges do not corrupt the domain model.

Strangler fig pattern

This pattern is used for gradually transforming legacy systems by replacing specific pieces of functionality with new applications and services. Over time, these new components “strangle” the old system, eventually replacing it entirely.

Transactional outbox

The transactional outbox pattern is used to ensure reliable messaging between services. When changes are made to the database, records are also added to an “outbox” table within the same transaction. A separate process then sends these messages to the message queue or other destinations, ensuring data consistency across service boundaries.

Sidecar pattern

The sidecar pattern is commonly used in microservice architectures. It involves deploying an additional container or process alongside a service container, providing platform-connected services such as monitoring, logging, configuration, and networking capabilities. The sidecar can be written in any language without strong dependency on the main container and thus provides decoupling. This pattern allows for isolating infrastructure concerns from the application, making it cleaner and more modular.

Backend for frontend (BFF)

This pattern involves creating separate backend services tailored for different types of clients, such as desktop browsers and mobile devices. It allows developers to optimize these backend services for specific user interfaces, enhancing the user experience by reducing overfetching or underfetching of data.

Cellular architecture

Cellular architecture involves designing systems as collections of cells, where each cell operates independently but can communicate with other cells. Each cell is self-contained with its own data store and logic, capable of scaling autonomously. This approach is highly resilient since failure in one cell does not affect the others, and it supports evolutionary architecture by allowing each cell to evolve independently.

Each of these patterns provides targeted solutions to common problems in developing and managing cloud native applications, offering pathways to more resilient, flexible, and scalable architectures. Remember, this is not an exhaustive list of architecture patterns, and the list keeps evolving with the latest developments in system design. However, these patterns together offer robust solutions to various architectural challenges that you might face when designing complex systems.
   
Qns. 51:

Kafka Architecture & Configuration :

Kafka’s configuration levels

Kafka offers several configuration levels for fine-tuning the system to meet specific needs:

Replication factor

This level determines the number of copies of a partition that Kafka will maintain across different brokers. Higher replication increases fault tolerance but also requires more storage and network resources.

Acknowledgments

Producers can configure the level of acknowledgment they require from the brokers. For example, a setting of acks=all ensures that a message is considered successfully written only when all in-sync replicas acknowledge it, enhancing reliability at the cost of higher latency.

Retention policies

Kafka allows configuration of how long records are retained in the topics. Retention can be based on time (e.g., seven days) or size (e.g., 100 GB), ensuring that only relevant data is kept for more effective storage management.

Compression

Message compression in Kafka reduces the amount of data that is transmitted over the network and stored on disk. Different compression algorithms like gzip, snappy, and lz4 can be configured based on the use case.

Kafka provides strong messaging guarantees that ensure the reliable delivery of messages in distributed systems. These guarantees are crucial for maintaining data consistency and integrity across complex data pipelines:

At-most-once delivery

Messages are delivered to consumers at most once, meaning they may be lost but are never delivered more than once. This is the default setting when no retries or acknowledgments are configured.

At-least-once delivery

Every message is delivered at least once. This means that even in the event of a failure, the message will eventually reach the consumer, though duplicates may occur. This guarantee is often used when data loss is unacceptable, and duplicate messages can be handled by the application logic. To manage the potential duplicates, applications consuming messages in an at-least-once delivery setup should implement idempotency in their processing logic.

Note

An operation is idempotent if applying it multiple times has the same effect as applying it once. For example, a consumer might keep track of processed message IDs to prevent processing duplicates. This approach ensures that repeated messages do not result in unintended side effects, maintaining data integrity in applications that require precise processing.

Exactly-once delivery

Exactly-once semantics (EOS) in Kafka ensure that each message is delivered to the consumer exactly once, without any duplicates, even in the face of failures. This is achieved through Kafka’s idempotent producers and transactional APIs, which maintain atomicity across multiple partitions and topics. EOS is particularly valuable in systems where data integrity is critical, such as financial transactions.

These messaging guarantees, along with Kafka’s robust fault tolerance and scalability, make it a reliable choice for building distributed data streaming and processing systems. Kafka’s ability to handle large-scale data streams with low latency has made it a cornerstone in modern distributed systems.


Note : AWS 

A specific problem that arises with IPv4 addresses is the very limited number of addresses available in comparison to the number of devices and networks across the world. The intermittent solution to slow exhaustion of IPv4 addresses is via Classless Inter-Domain Routing (CIDR).


