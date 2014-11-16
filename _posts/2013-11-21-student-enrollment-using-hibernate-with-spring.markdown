---
layout: post
title: "Building Java Web Application Using Hibernate with Spring"
author: swapna
date: 2013-11-21 09:41:30 -0600
comments: true
categories: [Hibernate, Spring, Maven, Java]
---
This post will show how to create a Student Enrollment Application using MYSQL DB with Hibernate ORM in a Spring environment. This is a simple application that aims to collect the input details from the user during signup, save the details in the MYSQL DB and authenticate the same during login.

<!-- more -->

1. Create Java Web Application Project using Maven Template
-----------------------------------------------------------
To begin with, in the IDE, create a Java Maven project with the template of maven-archetype-webapp (Filter the catalog based on the string "webapp") by providing appropriate values for GroupId and Artifact Id for the project. The sample web application directory structure is shown below with a standard deployment descriptor web.xml and Maven pom.xml

![Hibernate Spring Maven Project Layout](/assets/images/elizabetht/springlayout.png "Spring Hibernate Project Layout")

2. Update pom.xml
-----------------
To make the above Maven Java Web Application project support the Hibernate ORM in Spring framework, add the following dependencies to the existing pom.xml

  - jstl, spring-webmvc and servlet-api (for Spring support)
  - mysql-connector-java (for MYSQL support)
  - spring-jdbc (for data access with JDBC Spring)
  - spring-orm (for ORM data access with Spring)
  - spring-data-jpa (for JPA support)
  - hibernate-validator and hibernate-entitymanager (for Hibernate Support)
  - jta (for transaction support)
	
```
<dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-webmvc</artifactId>
    	<version>3.2.4.RELEASE</version>
    </dependency>
    <dependency>
    	<groupId>javax.servlet</groupId>
    	<artifactId>servlet-api</artifactId>
    	<version>2.5</version>
    	<scope>provided</scope>
    </dependency>
    <dependency>
    	<groupId>javax.servlet</groupId>
    	<artifactId>jstl</artifactId>
    	<version>1.2</version>
    </dependency>
    <dependency>
    	<groupId>mysql</groupId>
    	<artifactId>mysql-connector-java</artifactId>
    	<version>5.1.21</version>
    </dependency>
    <dependency>
    	<groupId>org.hibernate</groupId>
    	<artifactId>hibernate-validator</artifactId>
    	<version>4.2.0.Final</version>
    </dependency>
    <dependency>
    	<groupId>org.hibernate</groupId>
    	<artifactId>hibernate-entitymanager</artifactId>
    	<version>4.1.9.Final</version>
    </dependency>
    <dependency>
    	<groupId>javax.transaction</groupId>
    	<artifactId>jta</artifactId>
    	<version>1.1</version>
    </dependency>
    <dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-jdbc</artifactId>
    	<version>3.2.0.RELEASE</version>
    </dependency>
    <dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-orm</artifactId>
    	<version>3.2.0.RELEASE</version>
    </dependency>
    <dependency>
    	<groupId>org.springframework.data</groupId>
    	<artifactId>spring-data-jpa</artifactId>
    	<version>1.3.0.RELEASE</version>
    	<exclusions>
    		<exclusion>
    			<groupId>org.springframework</groupId>
    			<artifactId>spring-aop</artifactId>
    		</exclusion>
    	</exclusions>
    </dependency>
```

3. Modify web.xml
-----------------
Modify the contents of the web.xml to include the following:

  - A servlet and specify the location of the configuration file for the same. In this sample, a configuration file named springConfig.xml is created under WEB-INF/config folder in the project layout.
  - A servlet-mapping to map the servlet created in the above step that should be invoked when the client specifies the url matching the url pattern.
  - A ContextLoaderListener to integrate spring with the web application and provide the contextConfigLocation where the context files for JPA.

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5"
xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

<servlet>
	<servlet-name>studentHibernateServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/config/servletConfig.xml</param-value>
	</init-param>
</servlet>

<servlet-mapping>
	<servlet-name>studentHibernateServlet</servlet-name>
	<url-pattern>*.html</url-pattern>
</servlet-mapping>

<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:/jpaContext.xml</param-value>
</context-param>

<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

  <display-name>Archetype Created Web Application</display-name>
</web-app>

```

4. Create the Spring Configuration File
---------------------------------------
Create a Spring Bean Configuration file under the folder WEB-INF/config. If a STS(Spring Tool Suite) is the IDE, go ahead and enable the context and mvc namespaces. The servletConfig.xml will be as shown below

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">


</beans>
```

After enabling the required namespaces, include the following (in between the &lt;beans&gt; and &lt;/beans&gt; tags) to indicate that the application is annotation driven and base package for the context component scan. 

```
<mvc:annotation-driven />

<context:component-scan base-package="com.github.elizabetht" />
```

Include the bean InternalResourceViewResolver of Spring to locate the jsp files

```
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/WEB-INF/jsp/" />
	<property name="suffix" value=".jsp" />
</bean>
```

Include the bean for specifying a properties file (more on this later) which is to be used to store custom messages or properties. The following configuration allows to create a properties file named messages.properties under the src/main/resources folder in the project.

```
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
	<property name="basename" value="messages" />
</bean>
```

5. Create persistence.xml
-------------------------
Create a file named persistence.xml under the folder src/main/resources/META-INF folder in the project to define the persistence unit required by JPA. Add the following to the persistence.xml to define a persistence unit named punit.

```
<persistence xmlns="http://java.sun.com/xml/ns/persistence"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="{http://java.sun.com/xml/ns/persistence} {http://java.sun.com/xml/ns/persistence_2_0.xsd}"
	version="2.0">
	
	<persistence-unit name="punit">
	</persistence-unit>
	
</persistence>
```

6. Create jpaContext.xml
------------------------
As defined in the web.xml, create a file named jpaContext.xml under the folder src/main/resources folder in the project to define JPA and Hibernate related configurations. Note that any file created under src/main/resources folder in a maven project will be automagically added by Maven to the classpath. If STS(Spring Tool Suite) is the IDE, go ahead and enable the context, jpa and tx namespaces. The jpaContext.xml will be as shown below

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa-1.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">
		
<beans>
```

After enabling the required namespaces, include the following (in between the &lt;beans&gt; and &lt;/beans&gt; tags) to indicate that the application is annotation driven and base package for the jpa repositories scan. 

```
<context:annotation-config />
	
<jpa:repositories base-package="com.github.elizabetht.repository" />
```

Next, include the bean PersistenceAnnotationBeanPostProcessor. This is necessary to process the Persistence Unit, Persistence Context annotations and for injecting JPA related resources.

```
<bean class="org.springframework.orm.jpa.support.PersistenceAnnotationBeanPostProcessor" />
```

Include the bean for EntityManagerFactory which lists the various JPA related properties/resources.

```
<bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
		<property name="persistenceUnitName" value="punit" />
		<property name="dataSource" ref="dataSource" />
		<property name="jpaVendorAdapter">
			<bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
				<property name="showSql" value="true" />
			</bean>
		</property>
		<property name="jpaPropertyMap">
			<map>
				<entry key="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" />
				<entry key="hibernate.hbm2ddl.auto" value="validate" />
				<entry key="hibernate.format_sql" value="true" />
			</map>
		</property>
	</bean>
```

Include the bean for data source, where the properties of the MYSQL DB like url, username and password can be specified. Replace &lt;include connection url&gt; with the actual connection url for connecting to the MYSQL DB. Likewise, replace &lt;include username&gt; and &lt;include password&gt; with the actual username and password values.

```
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver" />
		<property name="url" value="jdbc:mysql//<include connection url>:3306/studentEnrollment?autoReconnect=true&amp;createDatabaseIfNotExist=true&amp;" />
		<property name="username" value="<include username>" />
		<property name="password" value="<include password>" />
</bean>
```

Include the bean for transaction manager for scoping/controlling the transactions.

```
<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
		<property name="entityManagerFactory" ref="entityManagerFactory" />
</bean>
	
<tx:annotation-driven transaction-manager="transactionManager" />
```

Thus ends the long configuration in jpaContext.xml

7. Create JSP Files for Student Signup/Login
--------------------------------------------
Create a folder named "jsp" under WEB-INF (This is where the jsp files will be created as indicated in the servletConfig.xml for the InternalResourceViewResolver bean).

Create a file signup.jsp to include a form to get the input details like UserName, Password, FirstName, LastName, DateOfBirth and EmailAddress of the student. A snapshot of the signup page is as follows:

![Spring Hibernate Signup Layout](/assets/images/elizabetht/signup.png "Spring Hibernate Signup Layout")

Next, create a file login.jsp to include a form with UserName and Password. A snapshot of the login page is as follows:

![Spring Hibernate Login Layout](/assets/images/elizabetht/login.png "Spring Hibernate Login Layout")

Also create success.jsp to indicate the login success and failure.jsp to indicate login failure (These are just pages used to display the contents - no processing logic involved). 

This application uses twitter bootstrap [http://getbootstrap.com/](http://getbootstrap.com/) and [http://bootswatch.com/united/](http://bootswatch.com/united/) as style sheets. It also uses a datepicker stylesheet as well to pop up a calendar for the DateOfBirth field in the Student Signup page ([http://www.eyecon.ro/bootstrap-datepicker/](http://www.eyecon.ro/bootstrap-datepicker/)). 

A reference link to the files under webapp folder of this application can be found at [https://github.com/elizabetht/StudentEnrollmentWithSpring/tree/master/src/main/webapp](https://github.com/elizabetht/StudentEnrollmentWithSpring/tree/master/src/main/webapp)

8. Create packages for Controller, Model, Repository and Service tier classes
-----------------------------------------------------------------------------
Create packages each for the Spring Controller, Model, Repository and Service classes under the src/main/java folder. 

A sample snapshot of the project after the package creation is as shown below:

![Spring Hibernate Package Layout](/assets/images/elizabetht/spring-hibernate-package.png "Spring Hibernate Package Layout")

9. Create classes for Model Tier
--------------------------------
Create a POJO class named Student.java inside the package com.github.elizabetht.model to include the details of the Student model entity during signup. Create another POJO class named StudentLogin.java inside the same package com.github.elizabetht.model to include the Student Login details.

A reference link to the files for the Model classes can be found at [https://github.com/elizabetht/StudentEnrollmentWithSpring/tree/master/src/main/java/com/github/elizabetht/model](https://github.com/elizabetht/StudentEnrollmentWithSpring/tree/master/src/main/java/com/github/elizabetht/model)

10. Create class for Repository Tier
--------------------------------------
Create an interface class named StudentRepository.java inside the package com.github.elizabetht.repository to support the repository tier database operations.

There are two interface methods needed for the application's purpose.

  - To Insert the Student Signup details into the Database
  - To Verify the Student Login details from the Database

```
@Repository("studentRepository")
public interface StudentRepository extends JpaRepository<Student, Long> {
	
	@Query("select s from Student s where s.userName = :userName")
	Student findByUserName(@Param("userName") String userName);
	
}
```

The save() method is supported by the Hibernate implementation and hence no separate SQL statements are required for the data insert.

11. Create classes for Service Tier
------------------------------------
Create an interface class named StudentService.java inside the package com.github.elizabetht.service to support the service tier operations.

```
public interface StudentService {
	Student save(Student student);
	boolean findByLogin(String userName, String password);
	boolean findByUserName(String userName);
}
```

Create a service tier implementation class (a POJO indeed) named StudentServiceImpl.java inside the package com.github.elizabetht.service. This is where the application logic goes - either to save the student details into the database or to verify the student (already saved) details from the database.

```
@Service("studentService")
public class StudentServiceImpl implements StudentService {

	@Autowired
	private StudentRepository studentRepository;
	
	@Transactional
	public Student save(Student student) {
		return studentRepository.save(student);
	}

	public boolean findByLogin(String userName, String password) {	
		Student stud = studentRepository.findByUserName(userName);
		
		if(stud != null && stud.getPassword().equals(password)) {
			return true;
		} 
		
		return false;		
	}

	public boolean findByUserName(String userName) {
		Student stud = studentRepository.findByUserName(userName);
		
		if(stud != null) {
			return true;
		}
		
		return false;
	}

}
```

12. Create class for Controller Tier
------------------------------------
Create a Controller tier POJO class named StudentController.java inside the package com.github.elizabetht.controller. This is where the routing logic of the application goes - whether a signup or login action is called.

```
@Controller
@SessionAttributes("student")
public class StudentController {
	
	@Autowired
	private StudentService studentService;
		
	@RequestMapping(value="/signup", method=RequestMethod.GET)
	public String signup(Model model) {
		Student student = new Student();		
		model.addAttribute("student", student);		
		return "signup";
	}
	
	@RequestMapping(value="/signup", method=RequestMethod.POST)
	public String signup(@Valid @ModelAttribute("student") Student student, BindingResult result, Model model) {		
		if(result.hasErrors()) {
			return "signup";
		} else if(studentService.findByUserName(student.getUserName())) {
			model.addAttribute("message", "User Name exists. Try another user name");
			return "signup";
		} else {
			studentService.save(student);
			model.addAttribute("message", "Saved student details");
			return "redirect:login.html";
		}
	}

	@RequestMapping(value="/login", method=RequestMethod.GET)
	public String login(Model model) {			
		StudentLogin studentLogin = new StudentLogin();		
		model.addAttribute("studentLogin", studentLogin);
		return "login";
	}
	
	@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(@Valid @ModelAttribute("studentLogin") StudentLogin studentLogin, BindingResult result) {
		if (result.hasErrors()) {
			return "login";
		} else {
			boolean found = studentService.findByLogin(studentLogin.getUserName(), studentLogin.getPassword());
			if (found) {				
				return "success";
			} else {				
				return "failure";
			}
		}
		
	}
}
```

13. Create messages.properties file
----------------------------------
As seen above, the @Valid annotation is used to validate the input parameters of the form reaching the method and the result of the validation is stored in BindingResult object. In order to validate specific fields, (refer the classes created for the Model tier - [https://github.com/elizabetht/StudentEnrollmentWithSpring/tree/master/src/main/java/com/github/elizabetht/model](https://github.com/elizabetht/StudentEnrollmentWithSpring/tree/master/src/main/java/com/github/elizabetht/model)), use annotations like @NotEmpty, @Size, @Email and @NotNull from the various validations available from the Hibernate Validator.

The Custom messages that should be displayed when any of the above mentioned validators fail is specified in the messages.properties file. Create a file named messages.properties under src/main/resources folder and include the following

```
NotEmpty=Field cannot be blank
NotNull=Field cannot be blank

Email=Email Address not valid/well-formed
Past=Date of Birth must be in the past 

Size={0} must be between {2} and {1} characters long
typeMismatch=Invalid format
```

14. Create the DB Schema in a MYSQL DB
--------------------------------------
Connect to the MySQL DB which is to be used for this application and create a new DB Schema named studentEnrollment using the MySQL Workbench.
This is necessary as the DB Schema name of studentEnrollment is specified in the dataSource bean in jpaContext.xml

Once the studentEnrollment DB Schema is created, create a table named student inside the DB Schema using the CREATE TABLE statement as follows:

```
CREATE TABLE `student` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `dateOfBirth` datetime NOT NULL,
  `emailAddress` varchar(255) NOT NULL,
  `firstName` varchar(255) NOT NULL,
  `lastName` varchar(255) NOT NULL,
  `password` varchar(8) NOT NULL,
  `userName` varchar(20) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=43 DEFAULT CHARSET=latin1;
```

15. Deploying the Application on Tomcat Server
----------------------------------------------
Once the above steps are complete and the project is successfully built, the Java web application is ready to deployed on the Tomcat Server 7. 

The Java web application can be deployed locally by right clicking on the project and choosing the "Run As->Run on Server" option. 

The same can be deployed remotely on any native server that supports Tomcat by copying the WAR file (Right click on the project and choose Export as WAR File option) to /var/lib/tomcat7 folder (or appropriate tomcat directory) and restarting the tomcat server.

16. Clone or Download code
--------------------------
If using git, clone a copy of this project here: [https://github.com/elizabetht/StudentEnrollmentWithSpring.git](https://github.com/elizabetht/StudentEnrollmentWithSpring.git)

In case of not using git, download the project as ZIP or tar.gz file here: [https://github.com/elizabetht/StudentEnrollmentWithSpring/releases/tag/1.6](https://github.com/elizabetht/StudentEnrollmentWithSpring/releases/tag/1.6)
