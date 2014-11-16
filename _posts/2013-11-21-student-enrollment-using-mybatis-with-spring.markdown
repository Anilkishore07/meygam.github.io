---
layout: post
title: "Building Java Web Application Using MyBatis with Spring"
author: swapna
date: 2013-11-21 09:41:09 -0600
comments: true
categories: [MyBatis, Spring, Maven, Java]
---
This post will show how to create a Student Enrollment Application using MYSQL DB with MyBatis framework in a Spring environment. This is a simple application that aims to collect the input details from the user during signup, save the details in the MYSQL DB and authenticate the same during login.

<!-- more -->

1. Create Java Web Application Project using Maven Template
-----------------------------------------------------------
To begin with, in the IDE, create a Java Maven project with the template of maven-archetype-webapp (Filter the catalog based on the string "webapp") by providing appropriate values for GroupId and Artifact Id for the project. The sample web application directory structure is shown below with a standard deployment descriptor web.xml and Maven pom.xml

![MyBatis Maven Project Layout](/assets/images/elizabetht/mybatislayout.png "MyBatis Project Layout")

2. Update pom.xml
-----------------
To make the above Maven Java Web Application project support the MyBatis framework, add the following dependencies to the existing pom.xml

  - mybatis (for MyBatis support) 
  - mybatis-spring (for MyBatis-Spring integration support) 
  - jstl, spring-webmvc, servlet-api and spring-context-support (for Spring support) 
  - spring-test (may be optional, needed if Spring-test support is needed) 
  - mysql-connector-java (for MYSQL support) 
	
```
<dependency>
    	<groupId>org.mybatis</groupId>
    	<artifactId>mybatis</artifactId>
    	<version>3.1.1</version>
    </dependency>
    <dependency>
    	<groupId>org.mybatis</groupId>
    	<artifactId>mybatis-spring</artifactId>
    	<version>1.1.1</version>
    </dependency>
    <dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-context-support</artifactId>
    	<version>3.1.1.RELEASE</version>
    </dependency>
    <dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-test</artifactId>
    	<version>3.1.1.RELEASE</version>
    	<scope>test</scope>
    </dependency>
    <dependency>
    	<groupId>mysql</groupId>
    	<artifactId>mysql-connector-java</artifactId>
    	<version>5.1.21</version>
    </dependency>
    <dependency>
    	<groupId>javax.servlet</groupId>
    	<artifactId>jstl</artifactId>
    	<version>1.2</version>
    </dependency>
    <dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-webmvc</artifactId>
    	<version>3.2.4.RELEASE</version>
    </dependency>
    <dependency>
    	<groupId>javax.servlet</groupId>
    	<artifactId>servlet-api</artifactId>
    	<version>2.5</version>
    </dependency>
```

3. Modify web.xml
-----------------
Modify the contents of the web.xml to include the following:

  - A servlet and specify the location of the configuration file for the same. In this sample, a configuration file named springConfig.xml is created under WEB-INF/config folder in the project layout. 
  - A servlet-mapping to map the servlet created in the above step that should be invoked when the client specifies the url matching the url pattern.

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5"
xmlns="http://java.sun.com/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

<servlet>
	<servlet-name>myBatisServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/config/springConfig.xml</param-value>
	</init-param>
</servlet>

<servlet-mapping>
	<servlet-name>myBatisServlet</servlet-name>
	<url-pattern>*.html</url-pattern>
</servlet-mapping>

  <display-name>Archetype Created Web Application</display-name>
</web-app>
```

4. Create the Spring Configuration File
---------------------------------------
Create a Spring Bean Configuration file under the folder WEB-INF/config. If STS(Spring Tool Suite) is the IDE, go ahead and enable the context, mvc and tx namespaces. The springConfig.xml will be as shown below

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.1.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">


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

Include the bean for data source, where the properties of the MYSQL DB like url, username and password can be specified. Replace &lt;include connection url&gt; with the actual connection url for connecting to the MYSQL DB. Likewise, replace &lt;include username&gt; and &lt;include password&gt; with the actual username and password values.

```
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver" />
	<property name="url" value="jdbc:mysql//<include connection url>:3306/studentEnrollment?autoReconnect=true&amp;createDatabaseIfNotExist=true&amp;" />
	<property name="username" value="<include username>" />
	<property name="password" value="<include password>" />
</bean>
```

Include the bean for transaction manager for scoping/controlling the transactions, that takes the data source defined above as reference (dependent)

```
<tx:annotation-driven transaction-manager="transactionManager" />
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource" />
</bean>
```

Coming to the MyBatis specific configurations, include the bean for sqlSessionFactory which is the central configuration in a MyBatis application. This bean takes in three properties
- dataSource (already configured above)&nbsp;
- typeAliasesPackage (location where the model classes of this application resides)&nbsp;
- mapperLocations (location where the mapper xml files for the model resides - this is not needed here as annotation based configurations are used instead)&nbsp;

More details of this can be read at [http://mybatis.github.io/mybatis-3/](http://mybatis.github.io/mybatis-3/)

```
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<property name="typeAliasesPackage" value="com.github.elizabetht.model"/>
	<property name="mapperLocations" value="classpath*:com/github/elizabetht/mappers/*.xml" />
</bean>
```

Include the bean for sqlSession

```
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg index="0" ref="sqlSessionFactory" />
</bean>
```

Next and finally, include the bean for MapperScannerConfigurer

```
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="com.github.elizabetht.mappers" />
</bean>
```

5. Create JSP Files for Student Signup/Login
--------------------------------------------
Create a folder named "jsp" under WEB-INF (This is where the jsp files will be created as indicated in the springConfig.xml for the InternalResourceViewResolver bean).

Create a file signup.jsp to include a form to get the input details like UserName, Password, FirstName, LastName, DateOfBirth and EmailAddress of the student. A snapshot of the signup page is as follows:

![MyBatis Signup Layout](/assets/images/elizabetht/signup.png "MyBatis Signup Layout")

Next, create a file login.jsp to include a form with UserName and Password. A snapshot of the login page is as follows:

![MyBatis Login Layout](/assets/images/elizabetht/login.png "MyBatis Login Layout")

Also create success.jsp to indicate the login success and failure.jsp to indicate login failure (These are just pages used to display the contents - no processing logic involved). 

This application uses twitter bootstrap [http://getbootstrap.com/](http://getbootstrap.com/) and [http://bootswatch.com/united/](http://bootswatch.com/united/) as style sheets. It also uses a datepicker stylesheet as well to pop up a calendar for the DateOfBirth field in the Student Signup page ([http://www.eyecon.ro/bootstrap-datepicker/](http://www.eyecon.ro/bootstrap-datepicker/)). 

A reference link to the files under webapp folder of this application can be found at [https://github.com/elizabetht/StudentEnrollmentWithMyBatis/tree/master/src/main/webapp](https://github.com/elizabetht/StudentEnrollmentWithMyBatis/tree/master/src/main/webapp) 

6. Create packages for Controller, Model, Service and Mappers
-------------------------------------------------------------
Create packages each for the Spring Controller, Model and Service classes under the src/main/java folder. Also create a package for the MyBatis Mapper class under the same src/main/java folder. 

A sample snapshot of the project after the package creation is as shown below:

![MyBatis Package Layout](/assets/images/elizabetht/mybatis-package.png "MyBatis Package Layout")

7. Create classes for Model Tier
--------------------------------
Create a POJO class named Student.java inside the package com.github.elizabetht.model to include the details of the Student model entity during signup. Create another POJO class named StudentLogin.java inside the same package com.github.elizabetht.model to include the Student Login details.

A reference link to the files for the Model classes can be found at [https://github.com/elizabetht/StudentEnrollmentWithMyBatis/tree/master/src/main/java/com/github/elizabetht/model](https://github.com/elizabetht/StudentEnrollmentWithMyBatis/tree/master/src/main/java/com/github/elizabetht/model)

8. Create classes for MyBatis Mapper
------------------------------------
A Mapper in MyBatis framework is similar to the Repository tier in a Spring environment. Crude SQL queries takes its place here.
Create an interface class named StudentMapper.java inside the package com.github.elizabetht.mapper to support the database operations. 

There are two interface methods needed for the application's purpose.

  - To Insert the Student Signup details into the Database
  - To Verify the Student Login details from the Database

```
public interface StudentMapper {
	@Insert("INSERT INTO student(userName, password, firstName,"
			+ "lastName, dateOfBirth, emailAddress) VALUES"
			+ "(#{userName},#{password}, #{firstName}, #{lastName},"
			+ "#{dateOfBirth}, #{emailAddress})")
	@Options(useGeneratedKeys=true, keyProperty="id", flushCache=true, keyColumn="id")
	public void insertStudent(Student student);
		
	@Select("SELECT USERNAME as userName, PASSWORD as password, "
			+ "FIRSTNAME as firstName, LASTNAME as lastName, "
			+ "DATEOFBIRTH as dateOfBirth, EMAILADDRESS as emailAddress "
			+ "FROM student WHERE userName = #{userName}")
	public Student getStudentByUserName(String userName);


}
```

9. Create classes for Service Tier
----------------------------------
Create an interface class named StudentService.java inside the package com.github.elizabetht.service to support the service tier operations.

```
public interface StudentService {
	void insertStudent(Student student);
	boolean getStudentByLogin(String userName, String password);
	boolean getStudentByUserName(String userName);
}
```

Create a service tier implementation class (a POJO indeed) named StudentServiceImpl.java inside the package com.github.elizabetht.service. This is where the application logic goes - either to save the student details into the database or to verify the student (already saved) details from the database.

```
@Service("studentService")
public class StudentServiceImpl implements StudentService {

	@Autowired
	private StudentMapper studentMapper;
	
	@Transactional
	public void insertStudent(Student student) {		
		studentMapper.insertStudent(student);
	}

	public boolean getStudentByLogin(String userName, String password) {		
		Student student = studentMapper.getStudentByUserName(userName);
		
		if(student != null && student.getPassword().equals(password)) {
			return true;
		}
		
		return false;
	}

	public boolean getStudentByUserName(String userName) {
		Student student = studentMapper.getStudentByUserName(userName);
		
		if(student != null) {
			return true;
		}
		
		return false;
	}

}
```

{% blockquote @EduardoMacarron https://twitter.com/EduardoMacarron %}
When using MyBatis with Spring, a mapper can be directly injected into the service tier. This is probably the strongest point of the Spring integration of MyBatis. This is the only tool that I am aware that lets to build the application with no imports to it.
{% endblockquote %}

10. Create class for Controller Tier
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
	public String signup(@ModelAttribute("student") Student student, Model model) {
		if(studentService.getStudentByUserName(student.getUserName())) {
			model.addAttribute("message", "User Name exists. Try another user name");
			return "signup";
		} else {
			studentService.insertStudent(student);
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
	public String login(@ModelAttribute("studentLogin") StudentLogin studentLogin) {
		boolean found = studentService.getStudentByLogin(studentLogin.getUserName(), studentLogin.getPassword());
		if (found) {				
			return "success";
		} else {				
			return "failure";
		}
	}
}

```

11. Create the DB Schema in a MYSQL DB
--------------------------------------
Connect to the MySQL DB which is to be used for this application and create a new DB Schema named studentEnrollment using the MySQL Workbench.
This is necessary as the DB Schema name of studentEnrollment is specified in the dataSource bean in springConfig.xml

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

12. Deploying the Application on Tomcat Server
----------------------------------------------
Once the above steps are complete and the project is successfully built, the Java web application is ready to deployed on the Tomcat Server 7. 

The Java web application can be deployed locally by right clicking on the project and choosing the "Run As->Run on Server" option. 

The same can be deployed remotely on any native server that supports Tomcat by copying the WAR file (Right click on the project and choose Export as WAR File option) to /var/lib/tomcat7 folder (or appropriate tomcat directory) and restarting the tomcat server.

13. Clone or Download code
--------------------------
If using git, clone a copy of this project here: [https://github.com/elizabetht/StudentEnrollmentWithMyBatis.git](https://github.com/elizabetht/StudentEnrollmentWithMyBatis.git)

In case of not using git, download the project as ZIP or tar.gz file here: [https://github.com/elizabetht/StudentEnrollmentWithMyBatis/releases/tag/1.7](https://github.com/elizabetht/StudentEnrollmentWithMyBatis/releases/tag/1.7)
