---
layout: post
title: "Building Java Web Application Using Jersey2 REST with Spring"
author: swapna
date: 2013-12-13 10:45:30 -0600
comments: true
categories: [Hibernate, Spring, Maven, Jersey2, REST]
---
This post will show how to create a Student Enrollment Application using MYSQL DB with Hibernate ORM in a REST based Jersey2 Spring environment. This is a simple application that aims to collect the input details from the user during signup, save the details in the MYSQL DB and authenticate the same during login.

<!-- more -->

1. Create Java Web Application Project using Maven Template
-----------------------------------------------------------
To begin with, from the command line, create a Java Maven project with the template of jersey-quickstart-webapp by providing appropriate values for GroupId, Artifact Id, Version and Package for the project. 

```
mvn archetype:generate -DarchetypeGroupId=org.glassfish.jersey.archetypes -DarchetypeArtifactId=jersey-quickstart-webapp -DarchetypeVersion=2.4.1
```

Import the project into the IDE using File->Import->Existing Maven Projects, select Root Directory where the Project is created (ideally the name of the Project or ArtifactId) and click on Finish. The sample web application directory structure is shown below with a standard deployment descriptor web.xml and Maven pom.xml

![Jersey REST Spring Maven Project Layout](/assets/images/elizabetht/jersey-layout.png "Jersey REST Spring Maven Project Layout")

2. Update pom.xml
-----------------
To make the above Maven Java Web Application project support the Hibernate ORM and Jersey Container in Spring framework, add the following dependencies to the existing pom.xml

  - jstl and servlet-api (for Javax Servlet Support)
  - jersey-container-servlet, jersey-media-moxy, jersey-spring3, jersey-server and jersey-mvc-jsp (for Jersey Support)
  - spring-core, spring-context, spring-web and spring-webmvc(for Spring Support)
  - junit (for JUnit Support)
  - commons-lang3 (for standard Java library support)
  - mysql-connector-java (for MYSQL support)
  - spring-jdbc (for data access with JDBC Spring)
  - spring-orm (for ORM data access with Spring)
  - spring-data-jpa (for JPA support)
  - hibernate-validator and hibernate-entitymanager (for Hibernate Support)
  - jta (for transaction support)

```
	<dependencies>
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
			<groupId>org.glassfish.jersey.containers</groupId>
			<!-- <artifactId>jersey-container-servlet-core</artifactId> -->
			<!-- use the above artifactId if you need servlet 2.x compatibility -->
			<artifactId>jersey-container-servlet</artifactId>
			<version>${jersey.version}</version>
		</dependency>
		<!-- uncomment this to get JSON support -->
		<dependency>
			<groupId>org.glassfish.jersey.media</groupId>
			<artifactId>jersey-media-moxy</artifactId>
			<version>${jersey.version}</version>
		</dependency>
		<dependency>
			<groupId>org.glassfish.jersey.ext</groupId>
			<artifactId>jersey-spring3</artifactId>
			<version>${jersey.version}</version>
		</dependency>
		<dependency>
			<groupId>org.glassfish.jersey.core</groupId>
			<artifactId>jersey-server</artifactId>
			<version>${jersey.version}</version>
		</dependency>
		<dependency>
    		<groupId>org.glassfish.jersey.ext</groupId>
    		<artifactId>jersey-mvc-jsp</artifactId>
    		<version>${jersey.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-orm</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.11</version>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-lang3</artifactId>
			<version>3.1</version>
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

	</dependencies>
	<properties>
		<jersey.version>2.4.1</jersey.version>
		<spring.version>3.2.4.RELEASE</spring.version>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
```

3. Modify web.xml
-----------------
Modify the contents of the web.xml to include the following:

  - A Welcome file.
  - The Context Config Location.  
  - A ContextLoaderLister and RequestContextListener to integrate spring with the web application.
  - A Spring Jersey Web Servlet. Specify the location of the provider packages, enable the JSON POJO Mapping Feature, provide the path where the JSP files for the project are stored to the JSP TemplateBasePath and enable the JSP MVC Feature. In this sample, a configuration file named applicationConfig.xml is created under src/main/resources folder  and the JSP files are placed under WEB-INF/jsp folder in the project layout.
  - A servlet-mapping to map the servlet created in the above step that should be invoked when the client specifies the url matching the url pattern.  

```
<?xml version="1.0" encoding="UTF-8"?>
<!-- This web.xml file is not required when using Servlet 3.0 container,
     see implementation details http://jersey.java.net/nonav/documentation/latest/jax-rs.html -->
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>
	
	<context-param>
  		<param-name>contextConfigLocation</param-name>
    	<param-value>classpath:applicationContext.xml</param-value>
  	</context-param>
  	<listener>
    	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  	</listener>
  	<listener>
    	<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
  	</listener>
  
    <servlet>
        <servlet-name>Spring Jersey Web Servlet</servlet-name>
        <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
        <init-param>
            <param-name>jersey.config.server.provider.packages</param-name>
            <param-value>com.github.elizabetht</param-value>
        </init-param>
        <init-param>
      		<param-name>com.sun.jersey.api.json.POJOMappingFeature</param-name>
      		<param-value>true</param-value>
    	</init-param>
    	<init-param>
        	<param-name>jersey.config.server.mvc.templateBasePath.jsp</param-name>
        	<param-value>/WEB-INF/jsp</param-value>
    	</init-param>
    	<init-param>
    		<param-name>jersey.config.server.provider.classnames</param-name>
    		<param-value>org.glassfish.jersey.server.mvc.jsp.JspMvcFeature</param-value>
		</init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>Spring Jersey Web Servlet</servlet-name>
        <url-pattern>/webapi/*</url-pattern>
    </servlet-mapping>
</web-app>
```

4. Create persistence.xml
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

5. Create the Spring Configuration File
---------------------------------------
As defined in the web.xml, create a file named applicationContext.xml under the folder src/main/resources folder in the project to define JPA and Hibernate related configurations. Note that any file created under src/main/resources folder in a maven project will be automagically added by Maven to the classpath. If STS(Spring Tool Suite) is the IDE, go ahead and enable the context, jpa, mvc and tx namespaces. The applicationContext.xml will be as shown below

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:jpa="http://www.springframework.org/schema/data/jpa"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa-1.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">
		
</beans>
```

After enabling the required namespaces, include the following (in between the &lt;beans&gt; and &lt;/beans&gt; tags) to indicate that the application is annotation driven, base package for context component scan and base package for the jpa repositories scan. 

```
	<mvc:annotation-driven />
				
	<context:annotation-config />
	<context:component-scan base-package="com.github.elizabetht" />
		
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

Thus ends the long configuration in applicationContext.xml

6. Create JSP Files for Student Signup/Login
--------------------------------------------
Create a folder named "jsp" under WEB-INF (This is where the jsp files will be created as indicated in the web.xml for the JSP TemplateBasePath).

Create a file signup.jsp to include a form to get the input details like UserName, Password, FirstName, LastName, DateOfBirth and EmailAddress of the student. A snapshot of the signup page is as follows:

![Jersey REST Spring Hibernate Signup Layout](/assets/images/elizabetht/signup.png "Jersey REST Spring Hibernate Signup Layout")

Next, create a file login.jsp to include a form with UserName and Password. A snapshot of the login page is as follows:

![Jersey REST Spring Hibernate Login Layout](/assets/images/elizabetht/login.png "Jersey REST Spring Hibernate Login Layout")

Also create success.jsp to indicate the login success and failure.jsp to indicate login failure (These are just pages used to display the contents - no processing logic involved). 

This application uses twitter bootstrap [http://getbootstrap.com/](http://getbootstrap.com/) and [http://bootswatch.com/united/](http://bootswatch.com/united/) as style sheets. It also uses a datepicker stylesheet as well to pop up a calendar for the DateOfBirth field in the Student Signup page ([http://www.eyecon.ro/bootstrap-datepicker/](http://www.eyecon.ro/bootstrap-datepicker/)). 

A reference link to the files under webapp folder of this application can be found at [https://github.com/elizabetht/StudentEnrollmentWithREST/tree/master/src/main/webapp](https://github.com/elizabetht/StudentEnrollmentWithREST/tree/master/src/main/webapp)

7. Create packages for Resource, Service, Repository and Model tier classes
----------------------------------------------------------------------------
Create packages each for the Jersey Resource, Service, Repository and Model classes under the src/main/java folder. 

A sample snapshot of the project after the package creation is as shown below:

![Jersey REST Spring Package Layout](/assets/images/elizabetht/jersey-package.png "Jersey REST Spring Package Layout")

8. Create classes for Model Tier
--------------------------------
Create a POJO class named Student.java inside the package com.github.elizabetht.model to include the details of the Student model entity during signup. Create another POJO class named StudentLogin.java inside the same package com.github.elizabetht.model to include the Student Login details.

Annotate the classes with @Component to be picked by the Context-Component Scan of the Jersey-Spring framework. Also annotate the classes with @XmlRootElement to indicate the XML Element.

A reference link to the files for the Model classes can be found at [https://github.com/elizabetht/StudentEnrollmentWithREST/tree/master/src/main/java/com/github/elizabetht/model](https://github.com/elizabetht/StudentEnrollmentWithREST/tree/master/src/main/java/com/github/elizabetht/model)

9. Create class for Repository Tier
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

10. Create classes for Service Tier
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

11. Create class for Resource Tier
------------------------------------
Create a Resource tier POJO class named StudentResource.java inside the package com.github.elizabetht.resource. This is where a REST API is implemented for each of the operation performed from the front end.

```
@Component
@Path("studentResource")
@XmlRootElement
public class StudentResource {

	@Autowired
	private StudentService studentService;

	@GET
	@Path("signup")
	@Produces(MediaType.TEXT_HTML)
	public Response signup() {
		return Response.ok(new Viewable("/signup")).build();
	}

	@POST
	@Path("signup")
	@Consumes(MediaType.APPLICATION_FORM_URLENCODED)
	@Produces(MediaType.TEXT_HTML)
	public Response signup(@FormParam("userName") String userName,
			@FormParam("password") String password,
			@FormParam("firstName") String firstName,
			@FormParam("lastName") String lastName,
			@FormParam("dateOfBirth") String dateOfBirth,
			@FormParam("emailAddress") String emailAddress)
			throws ParseException {

		if (userName == null || password == null || firstName == null
				|| lastName == null || dateOfBirth == null
				|| emailAddress == null) {
			return Response.status(Status.PRECONDITION_FAILED).build();
		}

		Student student = new Student();
		student.setUserName(userName);
		student.setPassword(password);
		student.setFirstName(firstName);
		student.setLastName(lastName);

		student.setDateOfBirth(new java.sql.Date(new SimpleDateFormat(
				"MM/dd/yyyy").parse(dateOfBirth.substring(0, 10)).getTime()));

		student.setEmailAddress(emailAddress);

		if (studentService.findByUserName(userName)) {
			Map<String, Object> map = new HashMap<String, Object>();
			map.put("message", "User Name exists. Try another user name");
			map.put("student", student);
			return Response.status(Status.BAD_REQUEST)
					.entity(new Viewable("/signup", map)).build();
		} else {
			studentService.save(student);
			return Response.ok().entity(new Viewable("/login")).build();
		}
	}

	@GET
	@Path("login")
	@Produces(MediaType.TEXT_HTML)
	public Response login() {
		return Response.ok(new Viewable("/login")).build();
	}

	@POST
	@Path("login")
	@Consumes(MediaType.APPLICATION_FORM_URLENCODED)
	@Produces(MediaType.TEXT_HTML)
	public Response login(@FormParam("userName") String userName,
			@FormParam("password") String password) {

		if (userName == null || password == null) {
			return Response.status(Status.PRECONDITION_FAILED).build();
		}

		boolean found = studentService.findByLogin(userName, password);
		if (found) {
			return Response.ok().entity(new Viewable("/success")).build();
		} else {
			return Response.status(Status.BAD_REQUEST)
					.entity(new Viewable("/failure")).build();
		}
	}
}
```

12. Create the DB Schema in a MYSQL DB
--------------------------------------
Connect to the MySQL DB which is to be used for this application and create a new DB Schema named studentEnrollment using the MySQL Workbench.
This is necessary as the DB Schema name of studentEnrollment is specified in the dataSource bean in applicationContext.xml

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

13. Deploying the Application on Tomcat Server
----------------------------------------------
Once the above steps are complete and the project is successfully built, the Java web application is ready to deployed on the Tomcat Server 7. 

The Java web application can be deployed locally by right clicking on the project and choosing the "Run As->Run on Server" option. 

The same can be deployed remotely on any native server that supports Tomcat by copying the WAR file (Right click on the project and choose Export as WAR File option) to /var/lib/tomcat7 folder (or appropriate tomcat directory) and restarting the tomcat server.

14. Clone or Download code
--------------------------
If using git, clone a copy of this project here: [https://github.com/elizabetht/StudentEnrollmentWithREST.git](https://github.com/elizabetht/StudentEnrollmentWithREST.git)

In case of not using git, download the project as ZIP or tar.gz file here: [https://github.com/elizabetht/StudentEnrollmentWithREST/releases/tag/1.1](https://github.com/elizabetht/StudentEnrollmentWithREST/releases/tag/1.1)

