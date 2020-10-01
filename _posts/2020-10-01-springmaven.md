# Maven

## What is Maven?

Maven is a build automation tool. Building a proejct means compiling source code and runs tests such as unit tests as well as integration tests packaging.

Maven packages the codes into JAR files and bundles theses JAR files into a web archive, or commonly known as WAR files. It then deploys these WAR files onto the server.

# Spring Framwork

## What is Spring?

It is a dependency injection framework, and compliments the existing Java EE standards by making them easier for developers to use.

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

```Java
public void init()
public void destroy()
```

Init will invoke right after the object is created, and the destroy method just before the object is being destroyed

# LifeCycle Methods using spring configuration

**XML file:**

```XML
<bean class="com.spring.springcore.lc.xmlconfig.Patient" name="patient" p:id="123" init-method="hi" destroy-method="bye"/>
```

**Class File:**

```Java
	public void hi() {
		System.out.println("inside HI method");
	}

	public void bye() {
		System.out.println("inside bye");
	}
```

# LifeCycle Methods using spring interfaces

**Class File:**

```Java
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

```Java
	public static void main(String[] args) {
		AbstractApplicationContext ctx = new ClassPathXmlApplicationContext(
				"com/spring/springcore/lc/interfaces/config.xml");
		Patient patient = (Patient) ctx.getBean("patient");
		System.out.println(patient);
		ctx.registerShutdownHook(); //calling void destroy()

	}
```

# LifeCyle Methods using annotations

**Dependency to include in pom.xml:**

```XML
    <dependency>
		<groupId>javax.annotation</groupId>
		<artifactId>javax.annotation-api</artifactId>
		<version>1.3.2</version>
	</dependency>
```

**Class File:**

```Java
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

```XML
<bean class="com.spring.springcore.lc.annotations.Patient" name="patient" p:id="123" />
	<bean class="org.springframework.context.annotation.CommonAnnotationBeanPostProcessor" /> <! --Enable annotation support - PostConstruct and PreDestroy-- >
```

```XML
<context:annotation-config />
```

# Types of Dependency Injection

## **Setter Injection**

### **First Variant: Value as element**

**Within Spring Configuration**  
Create a bean with class attribute reference to POJO Class

```XML
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

```XML
<bean name="emp" class="com.spring.springcore.Employee">
	<property name="id" value="20"/>
	<property name="name" value="John"/>
</bean>
```

---

### **Third Variant: Using P Schema**

**With the same config.xml as the previous example**

```XML
	<bean name="emp" class="com.spring.springcore.Employee"
		p:id="20" p:name="John" />
```

---

### p and c schema for reference type

```XML
p:scores-ref="scores"
c:scores-ref="scores"
```

## **Constructor Injection**

### **Three types of Construction Injection**

**1. As element**

```XML
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

```XML
	<bean
		class="com.spring.springcore.dependencycheck.Prescription"
		name="prescription">
		<constructor-arg value="123" />
		<constructor-arg ref="address" />
	</bean>
```

**3. Using C Schema**

```XML
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

```XML
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

```Java
	@Autowired
	public void setAddress(Address address) {
		this.address = address;
	}
```

```XML
	<context:annotation-config />
	<bean
		class="com.spring.springcoreadvance.autowiring.annotations.Address"
		name="address" p:hno="123" p:street="orchard" p:city="singapore" />
	<bean
		class="com.spring.springcoreadvance.autowiring.annotations.Employee"
		name="employee" />
```

## Qualifier to specify bean

```Java
@Autowired(required=false)
@Qualifier("address123")
private Address address;
```

```XML
<bean class="com.spring.springcoreadvance.autowiring.annotations.Address"
name="address2" p:hno="123" p:street="orchard" p:city="singapore" />
<bean class="com.spring.springcoreadvance.autowiring.annotations.Address"
name="address123" p:hno="567" p:street="orchard" p:city="singapore" />
```

# Standalone Collections

```XML
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

```Java
@Component
public class Instructor {
	private int id;
	private String name;

```

Then we need to let spring knows which packages to scan for components in the configuration file

```XML
<context:component-scan base-package="com.spring.springcoreadvance.stereotype.annotations"/>
```

## @Scope

We can use the @Scope annotation at class level to define the scope such as singleton, prototype, session, request, globalsession.

```Java
@Scope("prototype")
```

## @Value

For Primitive Types

```Java
@Values("20")
```

Collections

```Java
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

```Java
public int com.john.MyClass.multiply(int,int)
```

| Symbols | Can be used at                        |
| ------- | ------------------------------------- |
| \*      | AS,RT,PACK,CLASS,MN                   |
| ..      | pack,current and sub(1) Any Parameter |

To enable AOP Aspect annotations we have to configure our xml files as follows:

```XML
<beans xmlns:aop="http://www.springframework.org/schema/aop"
http://www.springframework.org/schema/aop
	http://www.springframework.org/schema/aop/spring-aop.xsd>

<aop:aspectj-autoproxy />
</beans>
```
