---
title: "Spring Framework Notes"
categories:
  - Programming Language
tags:
  - java spring
---

Notes on Spring framework derived from Spring Framewords in Easy Steps by Bharath Thippireddy on Udemy and some concepts from Martin Fowler (WIP)

# Maven

## What is Maven?

Maven is a build automation tool. Building a proejct means compiling source code and runs tests such as unit tests as well as integration tests packaging.

Maven packages the codes into JAR files and bundles theses JAR files into a web archive, or commonly known as WAR files. It then deploys these WAR files onto the server.

# Spring Framwork

## What is Spring?

It is a dependency injection framework, and compliments the existing java EE standards by making them easier for developers to use.

---

## What is inversion of control?

**Inversion of control(IoC) is a generic term.**  
It is a design pattern that gives away the object creation control from application code to an external framework. Control of objects or portions of a program is transferred to a container or framework.

---

## What is Dependency Injection?

**Dependency Injection(DI)** is a form of IoC, this term was derived due to confusion with the generic term of IoC.  
DI is a design pattern whcih makes our programming loosely coupled. It removes dependency from programming codes so that we can easily test our application.  
_The basic idea of DI is to have a separate object, an assembler, that populates a field in the lister class with an appropriate implementation for the finder interface._ [Martin Fowler](https://martinfowler.com/articles/injection.html#InversionOfControl)

- Implementations are passed into an object through constructors/setters/service lookups, which the object will 'depend' on in order to behave correctly [StackOverflow](https://stackoverflow.com/questions/6550700/inversion-of-control-vs-dependency-injection)

---

## Advantages of using Spring Framework

- Loose Coupling of objects
- Singleton design pattern

# Spring Container

- Creating object and holding them in memory
- Injecting them into another object as required
- Maintains complete lifecycle of an object from creation to destruction
- 2 Types of container:
  - BeanFactory
  - ApplicationContext

### The spring container provides 2 lifecycle for every bean it creates:

```java
public void init()
public void destroy()
```

Init will invoke right after the object is created, and the destroy method just before the object is being destroyed

# LifeCycle Methods using spring configuration

**xml file:**

```xml
<bean class="com.spring.springcore.lc.xmlconfig.Patient" name="patient" p:id="123" init-method="hi" destroy-method="bye"/>
```

**Class File:**

```java
	public void hi() {
		System.out.println("inside HI method");
	}

	public void bye() {
		System.out.println("inside bye");
	}
```

# LifeCycle Methods using spring interfaces

**Class File:**

```java
public class Patient implements InitializingBean, DisposableBean {
    	@Override
	public void afterPropertiesSet() throws Exception {
		System.out.println("Inside after properties set");
	}

	@Override
	public void destroy() throws Exception {
		System.out.println("Inside Destroy");
	}
}
```

```java
	public static void main(String[] args) {
		AbstractApplicationContext ctx = new ClassPathxmlApplicationContext(
				"com/spring/springcore/lc/interfaces/config.xml");
		Patient patient = (Patient) ctx.getBean("patient");
		System.out.println(patient);
		ctx.registerShutdownHook(); //calling void destroy()

	}
```

# LifeCyle Methods using annotations

**Dependency to include in pom.xml:**

```xml
    <dependency>
		<groupId>javax.annotation</groupId>
		<artifactId>javax.annotation-api</artifactId>
		<version>1.3.2</version>
	</dependency>
```

**Class File:**

```java
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
    @PostConstruct
	public void hi() {
		System.out.println("inside HI method");
	}

	@PreDestroy
	public void bye() {
		System.out.println("inside bye");
	}
```

**To include in config.xml:**

```xml
<bean class="com.spring.springcore.lc.annotations.Patient" name="patient" p:id="123" />
	<bean class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor" /> <! --Enable annotation support - PostConstruct and PreDestroy-- >
```

```xml
<context:annotation-config />
```

# Types of Dependency Injection

## **Setter Injection**

### **First Variant: Value as element**

**Within Spring Configuration**  
Create a bean with class attribute reference to POJO Class

```xml
<bean name="emp" class="com.spring.springcore.Employee">
	<property name="id">
		<value>20</value>
	</property>
	<property name="name">
		<value>John</value>
	</property>
</bean>

```

---

### **Second Variant: Value as attribute**

**With the same config.xml as the previous example**

```xml
<bean name="emp" class="com.spring.springcore.Employee">
	<property name="id" value="20"/>
	<property name="name" value="John"/>
</bean>
```

---

### **Third Variant: Using P Schema**

**With the same config.xml as the previous example**

```xml
	<bean name="emp" class="com.spring.springcore.Employee"
		p:id="20" p:name="John" />
```

---

### p and c schema for reference type

```xml
p:scores-ref="scores"
c:scores-ref="scores"
```

## **Constructor Injection**

### **Three types of Construction Injection**

**1. As element**

```xml
	<bean
		class="com.spring.springcore.dependencycheck.Prescription"
		name="prescription">
		<constructor-arg>
			<value>123</value>
		</constructor-arg>
		<constructor-arg>
			<value>john</value>
		</constructor-arg>
	</bean>
```

**2. As attribute**

```xml
	<bean
		class="com.spring.springcore.dependencycheck.Prescription"
		name="prescription">
		<constructor-arg value="123" />
		<constructor-arg ref="address" />
	</bean>
```

**3. Using C Schema**

```xml
<bean
	class="com.spring.springcore.dependencycheck.Prescription"
	name="prescription" c:id="123" c:address-ref="address">
	</bean>
```

---

# Injecting Collections

## **List**

```xml
<bean name="hospital"
		class="com.spring.springcore.list.Hospital">
		<property name="name">
			<value>Global Hospital</value>
		</property>
		<property name="departments">
			<list>
				<value>Front Office</value>
				<value>In Patient</value>
				<value>Out Patient</value>
			</list>
		</property>
	</bean>
```

If there is only 1 value within the list, we can omit the \<list> tag:

```xml
	<property name="name">
			<value>Global Hospital</value>
		</property>
		<property name="departments">
				<value>Front Office</value>
		</property>
```

---

## **Set**

Similar to list with \<set> enclosing tags

```xml
<bean name="hospital"
		class="com.spring.springcore.set.Hospital">
		<property name="name">
			<value>Global Hospital</value>
		</property>
		<property name="departments">
			<set>
				<value>Front Office</value>
				<value>In Patient</value>
				<value>Out Patient</value>
			</set>
		</property>
	</bean>
```

---

## **Map**

Map stores properties within key-value pair enclosed within \<map> tags.  
**First Variant: Key and Value as attributes**

```xml
<bean name="hospital"
		class="com.spring.springcore.map.Customer">
		<property name="products"> <!--name of the map-->
			<map>
			<entry key="100" value="IPhone"/>
			</map>
		</property>
		</bean>
```

**Second Variant: Value as element**

```xml
<property name="products"> <!--name of the map-->
			<map>
			<entry key="100">
				<value>IPhone</value>
			</entry>
			</map>
		</property>
```

**Third Variant: Key as element**

```xml
<property name="products"> <!--name of the map-->
			<map>
			<entry value="IPhone">
				<key>100</key>
			</entry>
			</map>
		</property>
```

---

# Injection Reference Types

We can inject reference types with the \<ref bean=""> tag:

```xml
<bean name="scores" class="com.spring.springcore.reftypes.Scores" p:maths="80" p:physics="90" p:chemistry="78"/>

<bean name="student" class="com.spring.springcore.reftypes.Student">
<property name="scores">
<ref bean="scores">
</property>
</bean>
```

\*Note: always start with the dependency bean, in this case, the Scores POJO class

## Auto-Wiring(by type, by name, by constructor)

```xml
<bean
		class="com.spring.springcoreadvance.autowiring.Address"
		name="address" p:hno="123" p:street="orchard" p:city="singapore" />
	<bean
		class="com.spring.springcoreadvance.autowiring.Employee"
		name="employee" autowire="byType" />
```

---

## Auto-Wiring annotations

Can be used at constructor/field/setter

```java
	@Autowired
	public void setAddress(Address address) {
		this.address = address;
	}
```

```xml
	<context:annotation-config />
	<bean
		class="com.spring.springcoreadvance.autowiring.annotations.Address"
		name="address" p:hno="123" p:street="orchard" p:city="singapore" />
	<bean
		class="com.spring.springcoreadvance.autowiring.annotations.Employee"
		name="employee" />
```

## Qualifier to specify bean

```java
@Autowired(required=false)
@Qualifier("address123")
private Address address;
```

```xml
<bean class="com.spring.springcoreadvance.autowiring.annotations.Address"
name="address2" p:hno="123" p:street="orchard" p:city="singapore" />
<bean class="com.spring.springcoreadvance.autowiring.annotations.Address"
name="address123" p:hno="567" p:street="orchard" p:city="singapore" />
```

# Standalone Collections

```xml
	<util:list list-class="java.util.LinkedList"
		id="productNames">
		<value>Macbook</value>
		<value>Iphone</value>
	</util:list>
	<bean
		class="com.spring.springcoreadvance.standalone.collections.ProductsList" name="productsList">
		<property name="productNames">
			<ref bean="productNames" />
		</property>
	</bean>
```

# Stereotype Annotations

## @Component

We can use the @Components annotation at class level to let the spring container knows that object of a specific class needs to be created:

```java
@Component
public class Instructor {
	private int id;
	private String name;

```

Then we need to let spring knows which packages to scan for components in the configuration file

```xml
<context:component-scan base-package="com.spring.springcoreadvance.stereotype.annotations"/>
```

## @Scope

We can use the @Scope annotation at class level to define the scope such as singleton, prototype, session, request, globalsession.

```java
@Scope("prototype")
```

## @Value

For Primitive Types

```java
@Values("20")
```

Collections

```java
@Value("#{myList}")
```

Object types uses @Autowired

# Interface Injection

# Aspect Oriented Programming AOP

Process of applying external services such as transaction managment or logging to our application classes without modify the code.

## Terminologies

- Aspect
  - A class that represents external services
- Advice
  - Method that is defined in an Aspect class
  - Often known as implementation of an **Aspect**
- PointCut
  - An expression that is used to tell which methods in the application needs an Advice
  - However it does not tell which advice is required
- JoinPoint
  - Joins an Advice with a PointCut
- Target
  - An object of the business class that needs Advice
- Weaving
  - A process of applying the Advices to the Target object methods using the JoinPoints
- Proxy
  - A class that is generated as a result of the weaving process

### Pointcut expression

(Access Specifier)(Return type)(package.class.methodName())

```java
public int com.john.MyClass.multiply(int,int)
```

| Symbols | Can be used at                        |
| ------- | ------------------------------------- |
| \*      | AS,RT,PACK,CLASS,MN                   |
| ..      | pack,current and sub(1) Any Parameter |

To enable AOP Aspect annotations we have to configure our xml files as follows:

```xml
<beans xmlns:aop="http://www.springframework.org/schema/aop"
http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd>

<aop:aspectj-autoproxy />
</beans>
```
We can do the following in the class level code to make use of AOP

```java
@Aspect
public class LoggingAspect {

	@Before("execution(* com.spring.springaop.ProductServiceImpl.multiply(..))")
	public void logBefore(JoinPoint joinPoint) {
		System.out.println("Before calling the method");
	}

	@After("execution(* com.spring.springaop.ProductServiceImpl.multiply(..))")
	public void logAfter(JoinPoint joinPoint) {
		System.out.println("After the method invocation");
	}
}
```

# Spring Expression Language

Spring expression language supports parsing and executing expressions with the help of the @Value annotation. The pound(#) character is used to evaluate an expression in Spring Expression Language

```java
// Example 1
@Value("#{66+44}")
// Example 2
@Value("#{5>6?22:23}")
```

The container will add these 2 integers that is within the @Value annotation and inject the value.  
In the second example, we can also specify a teneray operator within the @value annotation

## Using static methods inside expression

We can use static methods within the expression. For instance, we have:

```java
@Value("#{-99}")
```

This expression will evaluate the negative numbers. However, if we want to always take in a positive number, we could invoke a static method within the expression: T(class).method(param)

```java
@Value("#{T(java.lang.Math).abs(-99)}")
```

## Create objects inside expression

To create an object within the expression we can do as follows:

```java
@Value("#{new Integer(88)}")
```

_If we access a static variable_

```java
@Value("#{T(java.lang.Integer).MIN_VALUE}")
```

Here we are assigning the minimum value an integer can hold and injecting it with the value annotation

## Creating string type

The syntax to creating string type within expressions are as follows:

```java
@Value("#{'John Doe'}")

// To invoke methods on the string
@Value("#{'John Doe'.toUpperCase()}")

// Can also create String with the new operator
@Value("#{new java.lang.String('John Doe')}")
```

## Creating boolean type

```java
@Value("#{2+4>5}") // returns true
```

# Spring JDBC

When we use JDBC, we have to write a lot of codes such as creating Connection, creating Statement/PreparedStatement.

Spring simplifies JDBC by providing us with the JDCBTemplate class.

JDBCTemplate = JDBC Technology + Template Design Pattern

To use JDBCTemplate from Spring, we need to provide it with javax.sql.DataSource() interface. Spring provides an implementation class of this interface, DriverManagerDataSource.

We have to create a bean of DriverManagerDataSource and inject it into the JDBCTemplate.

The DriverManagerDataSource takes in 4 parameters namely, driverClassName, url, userName, password.

```xml
<bean class="org.springframework.jdbc.datasource.DriverManagerDataSource" name="dataSource" p:driverClassName="com.mysql.jdbc.Driver" p:url="jdbc:mysql://localhost:3306/mydb?useSSL=false" p:username="root"
p:password="root" />

<bean class="org.springframework.jdbc.core.JdbcTemplate"
		name="jdbcTemplate" p:dataSource-ref="dataSource" />
```

```java
ApplicationContext context = new ClassPathXmlApplicationContext("com/training/spring/springjdbc/config.xml");
		JdbcTemplate jdbcTemplate = (JdbcTemplate) context.getBean("jdbcTemplate");
		String sql = "insert into employee values(?,?,?)";
		int result = jdbcTemplate.update(sql, new Integer(1),"John","Doe");
		System.out.println("Number of records inserted are: "+result);
```
