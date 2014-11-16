---
layout: post
title: "Building Java Web Application Using Struts 2"
author: swapna
date: 2013-11-21 09:41:45 -0600
comments: true
categories: [Struts2, Java]
---
This post will show how to create a Student Enrollment Application using MYSQL DB with Struts 2 framework. This is a simple application that aims to collect the input details from the user during signup, save the details in the MYSQL DB and authenticate the same during login.

<!-- more -->

1. Create Java Web Application Project
--------------------------------------
To begin with, in the IDE, create a Java Dynamic Web project for the application. While creating the dynamic web project, enable the checkbox to generate web.xml deployment descriptor.

The sample web application directory structure is shown below with a standard deployment descriptor web.xml

![Struts Dynamic Web Project Layout](/assets/images/elizabetht/strutslayout.png "Struts Dynamic Web Project Layout")

2. Modify web.xml
-----------------
Modify the contents of the web.xml to include the following:

  - A Struts Dispatcher named Struts Prepare and Execute Filter to handle both the preparation and execution phases of Struts dispatching process.
  - A Filter mapping to map the dispatcher created in the above step that should be invoked when the client specifies the url matching the url pattern.

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>StudentEnrollmentWithStruts</display-name>
  <filter>
          <filter-name>struts2</filter-name>
          <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <filter-mapping>
          <filter-name>struts2</filter-name>
          <url-pattern>/*</url-pattern>
  </filter-mapping>  
</web-app>
```

3. Add JARs to the project
--------------------------
Add the following JARs from the web (or their latest equivalent) to the WEB-INF/lib folder under WebContent directory in the project.

  - asm-3.3.jar (for bytecode manipulation framework support)
  - asm-commons-3.3.jar (for bytecode manipulation framework support)
  - commons-fileupload-1.3.jar (for file upload capability to your servlets and web applications support)
  - commons-io-2.0.1.jar (for library of utilities to assist with developing IO functionality)
  - commons-lang-2.4.jar (for manipulation of core classes by providing helper utilities)
  - commons-lang3-3.1.jar (for manipulation of core classes by providing helper utilities)
  - freemarker-2.3.19.jar (for UI tag templates support)
  - javassist-3.11.0.GA.jar (for JAVA programming ASSISTance support)
  - mysql-connector-java-5.1.26-bin.jar (for MYSQL data access support)
  - ognl-3.0.6.jar (for Object Graph Navigation Language (OGNL) support)
  - struts2-bootstrap-plugin-1.6.1.jar (for Bootstrap support)
  - struts2-convention-plugin-2.3.15.1.jar (for the zero configuration and naming convention support)
  - struts2-core-2.3.15.1.jar (for Struts Framework library support)
  - struts2-jquery-plugin-3.6.1.jar (for JQuery support)
  - xwork-core-2.3.15.1.jar (for XWork 2 library support) 

The sample lib folder structure is shown below with the necessary JARs added for the project.

![Struts Library Layout](/assets/images/elizabetht/strutsliblayout.png "Struts Library Layout") 

4. Create JSP Files for Student Signup/Login
--------------------------------------------
Create a folder named "content" under WEB-INF (This is where the jsp files will be created).

Create a file signup.jsp to include a form to get the input details like UserName, Password, FirstName, LastName, DateOfBirth and EmailAddress of the student. A snapshot of the signup page is as follows:

![Struts Signup Layout](/assets/images/elizabetht/struts-signup.png "Struts Signup Layout")

Next, create a file login.jsp to include a form with UserName and Password. A snapshot of the login page is as follows:

![Struts Login Layout](/assets/images/elizabetht/login.png "Struts Login Layout")

The main actions for this application are as follows:

  - Signup (To Insert the Student Signup details into the Database)
  - Login (To Verify the Student Login details from the Database)
  
In order to display a success (result) page after the login action is complete, create a login-success.jsp page to indicate the login success (The page name will follow the semantics: &lt;action name&gt;-&lt;result name&gt;.jsp). Similarly, to indicate a login failure (result), create a page login-failure.jsp (login is the action and failure is the result). Also create a signup-failure.jsp page to indicate the signup failure, possibly due to an already existing username (signup is the action and failure is the result). These are just pages used to display the contents - no processing logic involved.

This application uses twitter bootstrap [http://getbootstrap.com/](http://getbootstrap.com/) and [http://bootswatch.com/united/](http://bootswatch.com/united/) as style sheets. 

The Struts 2 UI component tags are used to create the page elements like the form, textfield, password and hidden fields. The Struts Bootstrap tags are used for the Twitter Bootstrap integration. The Struts jQuery tags are used to include the AJAX functionality of submitting the form outside from the modal window and to use the UI widgets like the datepicker.

A reference link to the files under WebContent folder of this application can be found at [https://github.com/elizabetht/StudentEnrollmentWithStruts/tree/master/WebContent](https://github.com/elizabetht/StudentEnrollmentWithStruts/tree/master/WebContent)

5. Create packages
------------------
Create packages each for the Struts Actions(equivalent to Controller), Service, Repository and Model classes under the src folder. 
Also create package for the utilities class under the src folder.

A sample snapshot of the project after the package creation is as shown below:

![Struts Package Layout](/assets/images/elizabetht/struts-package.png "Struts Package Layout")

6. Create class for Model Tier
--------------------------------
Create a POJO class named Student.java inside the package com.github.elizabetht.model to include the details of the Student model entity.

```
public class Student {
	private String userName;
	private String firstName;
	private String lastName;
	private String password;
	private String emailAddress;
	private Date dateOfBirth;

	public String getUserName() {
		return userName;
	}

	public void setUserName(String userName) {
		this.userName = userName;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getEmailAddress() {
		return emailAddress;
	}

	public void setEmailAddress(String emailAddress) {
		this.emailAddress = emailAddress;
	}

	public Date getDateOfBirth() {
		return dateOfBirth;
	}

	public void setDateOfBirth(Date dateOfBirth) {
		this.dateOfBirth = dateOfBirth;
	}
}
```

7. Create db.properties file
-----------------------------
Create a file named db.properties under the src folder, where the properties of the MYSQL DB like url, username and password can be specified. Replace &lt;include connection url&gt; with the actual connection url for connecting to the MYSQL DB. Likewise, replace &lt;include username&gt; and &lt;include password&gt; with the actual username and password values.

```
dbDriver=com.mysql.jdbc.Driver
connectionUrl=jdbc:mysql://<include connection url>:3306/studentEnrollment
userName=<include username>
password=<include password>
```

8. Create utility class 
------------------------------------
Create a POJO class named DbUtil.java under the package com.github.elizabetht.util to include a helper class functionality that would load the db.properties file and get the database connection.

```
public class DbUtil {
	private static Connection dbConnection = null;

	public static Connection getConnection() {
		if (dbConnection != null) {
			return dbConnection;
		} else {
			try {
				InputStream inputStream = DbUtil.class.getClassLoader()
						.getResourceAsStream("db.properties");
				Properties properties = new Properties();
				if (properties != null) {
					properties.load(inputStream);

					String dbDriver = properties.getProperty("dbDriver");
					String connectionUrl = properties
							.getProperty("connectionUrl");
					String userName = properties.getProperty("userName");
					String password = properties.getProperty("password");

					Class.forName(dbDriver).newInstance();
					dbConnection = DriverManager.getConnection(connectionUrl,
							userName, password);
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
			return dbConnection;
		}
	}
}
```

9. Create class for Repository tier
-----------------------------------
Create a Repository tier POJO class named StudentRepository.java under the package com.github.elizabetht.repository to support the database operations of saving the student details, verifying the student login details and checking if the username exists when a save is attempted.

```
public class StudentRepository {
	private Connection dbConnection;

	public StudentRepository() {
		dbConnection = DbUtil.getConnection();
	}

	public void save(String userName, String password, String firstName,
			String lastName, String dateOfBirth, String emailAddress) {
		if (dbConnection != null) {
			try {
				PreparedStatement prepStatement = dbConnection
						.prepareStatement("insert into student(userName, password, firstName, lastName, dateOfBirth, emailAddress) values (?, ?, ?, ?, ?, ?)");
				prepStatement.setString(1, userName);
				prepStatement.setString(2, password);
				prepStatement.setString(3, firstName);
				prepStatement.setString(4, lastName);				
				
				prepStatement.setDate(5, new java.sql.Date(new SimpleDateFormat("MM/dd/yyyy")
				.parse(dateOfBirth.substring(0, 10)).getTime()));

				prepStatement.setString(6, emailAddress);

				prepStatement.executeUpdate();
			} catch (SQLException e) {
				e.printStackTrace();
			} catch (ParseException e) {				
				e.printStackTrace();
			}
		}
	}

	public boolean findByUserName(String userName) {
		if (dbConnection != null) {
			try {
				PreparedStatement prepStatement = dbConnection
						.prepareStatement("select count(*) from student where userName = ?");
				prepStatement.setString(1, userName);

				ResultSet result = prepStatement.executeQuery();
				if (result != null) {
					while (result.next()) {
						if (result.getInt(1) == 1) {
							return true;
						}
					}
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		return false;
	}

	public boolean findByLogin(String userName, String password) {
		if (dbConnection != null) {
			try {
				PreparedStatement prepStatement = dbConnection
						.prepareStatement("select password from student where userName = ?");
				prepStatement.setString(1, userName);

				ResultSet result = prepStatement.executeQuery();
				if (result != null) {
					while (result.next()) {
						if (result.getString(1).equals(password)) {
							return true;
						}
					}
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
		return false;
	}

}
```

10. Create class for Service Tier
------------------------------------
Create a Controller tier POJO class named StudentService.java inside the package com.github.elizabetht.service. This layer acts as a median between the Controller and Repository tier classes.

```
public class StudentService {

	private StudentRepository studentRepository;

	public StudentService() {
		studentRepository = new StudentRepository();
	}

	public String save(String userName, String password,
			String firstName, String lastName, String dateOfBirth,
			String emailAddress) {
		if (studentRepository != null) {
			if (studentRepository.findByUserName(userName)) {
				return "SignupFailure-UserNameExists";
			}
			studentRepository.save(userName, password, firstName, lastName,
					dateOfBirth, emailAddress);
			return "SignupSuccess";
		} else {
			return "SignupFailure";
		}
	}

	public String findByLogin(String userName, String password) {
		String result = "LoginFailure";
		if (studentRepository != null) {
			boolean status = studentRepository.findByLogin(userName, password);
			if (status) {
				result = "LoginSuccess";
			}
		}
		return result;
	}
}
```

11. Create Action classes for Controller tier
---------------------------------------------
Create a Controller tier POJO class named StudentController.java inside the package com.github.elizabetht.controller. This is where the routing logic of the application goes - whether a signup or login action is called. For simplicity sake, two separate action classes are created for each of the operation.

Each of the Action class has to implement the execute() method. Based on the String result returned from the execute() method of the action class, the appropriate view page is rendered. 

In most cases, the action class is extended from the general ActionSupport class, which has a lot of easy to use convenient features. So, extend the ActionSupport class in the Action class, unless you have a reason not to!

The following snippet shows the SignupAction class.
```
@SuppressWarnings("serial")
public class SignupAction extends ActionSupport {

	private String pageName;
	private String userName;
	private String password;
	private String firstName;
	private String lastName;
	private String dateOfBirth;
	private String emailAddress;

	@Action("signup-input")
	public String input() throws Exception {
		return "signup";
	}

	@Override
	@Action(value = "signup", results = { @Result(name = "login-input", location = "login-input", type = "redirect") })
	public String execute() throws Exception {
		String result = "";
		StudentService studentService = new StudentService();

		if (pageName != null && studentService != null) {
			if (pageName.equals("signup")) {
				result = studentService.save(userName, password, firstName,
						lastName, dateOfBirth, emailAddress);
				if (result.equals("SignupSuccess")) {
					return "login-input";
				} else {
					return "failure";
				}
			}
		}
		return SUCCESS;
	}

	public String getPageName() {
		return pageName;
	}

	public void setPageName(String pageName) {
		this.pageName = pageName;
	}

	public String getUserName() {
		return userName;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "UserName is a required field")
	@StringLengthFieldValidator(type = ValidatorType.FIELD, maxLength = "12", minLength = "6", message = "UserName must be of length between 6 and 12")
	public void setUserName(String userName) {
		this.userName = userName;
	}

	public String getPassword() {
		return password;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "Password is a required field")
	@StringLengthFieldValidator(type = ValidatorType.FIELD, maxLength = "12", minLength = "6", message = "Password must be of length between 6 and 12")
	public void setPassword(String password) {
		this.password = password;
	}

	public String getFirstName() {
		return firstName;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "FirstName is a required field")
	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "LastName is a required field")
	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmailAddress() {
		return emailAddress;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "EmailAddress is a required field")
	@EmailValidator(type = ValidatorType.FIELD, message = "Email Address must be valid")
	public void setEmailAddress(String emailAddress) {
		this.emailAddress = emailAddress;
	}

	public String getDateOfBirth() {
		return dateOfBirth;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "DateOfBirth is a required field")
	public void setDateOfBirth(String dateOfBirth) {
		this.dateOfBirth = dateOfBirth;
	}
}
```

Similar to the SignupAction class, the LoginAction class also implements the execute() method as shown below

```
@SuppressWarnings("serial")
public class LoginAction extends ActionSupport {

	private String pageName;
	private String userName;
	private String password;

	@Action("login-input")
	public String input() throws Exception {
		return "login";
	}

	@Action("login")
	public String execute() throws Exception {
		String result = "";
		StudentService studentService = new StudentService();

		if (pageName != null && studentService != null) {
			if (pageName.equals("login")) {
				result = studentService.findByLogin(userName, password);
				if (result.equals("LoginFailure")) {
					return "failure";
				} else {
					return "success";
				}
			}
		}
		return SUCCESS;
	}

	public String getPageName() {
		return pageName;
	}

	public void setPageName(String pageName) {
		this.pageName = pageName;
	}

	public String getUserName() {
		return userName;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "UserName is a required field")
	@StringLengthFieldValidator(type = ValidatorType.FIELD, maxLength = "12", minLength = "6", message = "UserName must be of length between 6 and 12")
	public void setUserName(String userName) {
		this.userName = userName;
	}

	public String getPassword() {
		return password;
	}

	@RequiredStringValidator(type = ValidatorType.FIELD, message = "Password is a required field")
	@StringLengthFieldValidator(type = ValidatorType.FIELD, maxLength = "12", minLength = "6", message = "Password must be of length between 6 and 12")
	public void setPassword(String password) {
		this.password = password;
	}

}
```

The @Action annotation is used to specify the incoming requested URL and results to be rendered. For results other than SUCCESS (or the equivalent success string), the resulting view will be rendered based on the value of the result. For instance, in SignupAction class, if "failure" string is returned from the execute() method, the result will be appended to the @Action value (which is "signup", in this case) and the signup-failure.jsp page will be rendered. Similarly, in LoginAction class, if "failure" string is returned from the execute() method, the login-failure.jsp page will be rendered.

If SUCCESS (or the equivalent success string) is returned from the execute() method, the actual value of action will be the resulting view, unless there are no results specified using the @Result annotation. For instance, in LoginAction class, there are no @Result annotations used and hence, login-success.jsp will be rendered when execute() method returns a SUCCESS string.

But if there are results specified with @Result annotation as in SignupAction class, the name and location given by the results array will determine the view rendered - in this case, login-input action will be rendered (which is in-turn, an action specified by the LoginAction class).  

12. Add Validators to the Form Fields
-------------------------------------
As shown in the above snippets of SignupAction and LoginAction classes, add the following required validators to the setter methods for the fields.

@RequiredStringValidator is used to check that a String field is not empty. 

@StringLengthFieldValidator is used to check that a String field is of the right length. It assumes that the field is a String. If neither minLength nor maxLength is set, nothing will be done.

@EmailValidator is used to check that a field is a valid e-mail address if it contains a non-empty String.

13. Create the DB Schema in a MYSQL DB
--------------------------------------
Connect to the MySQL DB which is to be used for this application and create a new DB Schema named studentEnrollment using the MySQL Workbench.
This is necessary as the DB Schema name of studentEnrollment is specified in the db.properties file.

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

14. Deploying the Application on Tomcat Server
----------------------------------------------
Once the above steps are complete and the project is successfully built, the Java web application is ready to deployed on the Tomcat Server 7. 

The Java web application can be deployed locally by right clicking on the project and choosing the "Run As->Run on Server" option. 

The same can be deployed remotely on any native server that supports Tomcat by copying the WAR file (Right click on the project and choose Export as WAR File option) to /var/lib/tomcat7 folder (or appropriate tomcat directory) and restarting the tomcat server.

15. Clone or Download code
--------------------------
If using git, clone a copy of this project here: https://github.com/elizabetht/StudentEnrollmentWithStruts.git

In case of not using git, download the project as ZIP or tar.gz file here: [https://github.com/elizabetht/StudentEnrollmentWithStruts/releases/tag/1.4](https://github.com/elizabetht/StudentEnrollmentWithStruts/releases/tag/1.4)
