---
layout: post
title: "Building Java Web Application Using JDBC"
author: swapna
date: 2013-11-21 09:41:55 -0600
comments: true
categories: [JDBC, Java]
---
This post will show how to create a Student Enrollment Application using MYSQL DB with JDBC. This is a simple application that aims to collect the input details from the user during signup, save the details in the MYSQL DB and authenticate the same during login.

<!-- more -->

1. Create Java Web Application Project
--------------------------------------
To begin with, in the IDE, create a Java Dynamic Web project for the application. While creating the dynamic web project, enable the checkbox to generate web.xml deployment descriptor.

The sample web application directory structure is shown below with a standard deployment descriptor web.xml

![JDBC Dynamic Web Project Layout](/assets/images/elizabetht/jdbclayout.png "JDBC Dynamic Web Project Layout")

2. Modify web.xml
-----------------
Modify the contents of the web.xml to include the following:

  - A servlet and the corresponding class in the source folder that would handle the HTTP requests.
  - A servlet-mapping to map the servlet created in the above step that should be invoked when the client specifies the url matching the url pattern.
  - A welcome file list, which can be used optionally to include the welcome file for the application.
  
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>StudentEnrollmentWithJDBC</display-name>
  
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  
  <servlet>
  	<servlet-name>studentJDBCServlet</servlet-name>
  	<servlet-class>com.github.elizabetht.controller.StudentController</servlet-class>
  </servlet>
  
  <servlet-mapping>
  	<servlet-name>studentJDBCServlet</servlet-name>
  	<url-pattern>/StudentController</url-pattern>
  </servlet-mapping>
</web-app>
```

3. Add JARs to the project
--------------------------
Add the following JARs from the web (or their latest equivalent) to the WEB-INF/lib folder under WebContent directory in the project.

  - mysql-connector-java-5.1.26-bin.jar (for MYSQL data access support)
  - jstl.jar and standard.jar (for JSTL Expression language support)
  - ojdbc5.jar (for Oracle JDBC Driver support)

The sample lib folder structure is shown below with the necessary JARs added for the project.

![JDBC Library Layout](/assets/images/elizabetht/jdbcliblayout.png "JDBC Library Layout") 

4. Create JSP Files for Student Signup/Login
--------------------------------------------
Create a folder named "content" under WebContent (This is where the jsp files will be created).

Create a file signup.jsp to include a form to get the input details like UserName, Password, FirstName, LastName, DateOfBirth and EmailAddress of the student. A snapshot of the signup page is as follows:

![JDBC Signup Layout](/assets/images/elizabetht/signup.png "JDBC Signup Layout")

Next, create a file login.jsp to include a form with UserName and Password. A snapshot of the login page is as follows:

![JDBC Login Layout](/assets/images/elizabetht/login.png "JDBC Login Layout")

The main actions for this application are as follows:

  - Signup (To Insert the Student Signup details into the Database)
  - Login (To Verify the Student Login details from the Database)
  
In order to display a success (result) page after the login action is complete, create a success.jsp page to indicate the login success. Similarly, to indicate a login failure (result), create a page failure.jsp. These are just pages used to display the contents - no processing logic involved.

This application uses twitter bootstrap [http://getbootstrap.com/](http://getbootstrap.com/) and [http://bootswatch.com/united/](http://bootswatch.com/united/) as style sheets. It also uses a datepicker stylesheet as well to pop up a calendar for the DateOfBirth field in the Student Signup page ([http://www.eyecon.ro/bootstrap-datepicker/](http://www.eyecon.ro/bootstrap-datepicker/)).

A reference link to the files under WebContent folder of this application can be found at [https://github.com/elizabetht/StudentEnrollmentWithJDBC/tree/master/WebContent](https://github.com/elizabetht/StudentEnrollmentWithJDBC/tree/master/WebContent)

5. Create packages
------------------
Create packages each for the Controller, Repository and Model tiers under the src folder. 
Also create package for the utilities class under the src folder.

A sample snapshot of the project after the package creation is as shown below:

![JDBC Package Layout](/assets/images/elizabetht/jdbc-package.png "JDBC Package Layout")

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
	
	public void save(String userName, String password, String firstName, String lastName, String dateOfBirth, String emailAddress) {
		try {
			PreparedStatement prepStatement = dbConnection.prepareStatement("insert into student(userName, password, firstName, lastName, dateOfBirth, emailAddress) values (?, ?, ?, ?, ?, ?)");
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
	
	public boolean findByUserName(String userName) {
		try {
			PreparedStatement prepStatement = dbConnection.prepareStatement("select count(*) from student where userName = ?");
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
		return false;
	}
	
	public boolean findByLogin(String userName, String password) {
		try {
			PreparedStatement prepStatement = dbConnection.prepareStatement("select password from student where userName = ?");
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
		return false;
	}

}
```

10. Create class for Controller Tier
------------------------------------
Create a Controller tier POJO class named StudentController.java inside the package com.github.elizabetht.controller. This is where the servicing logic of the application goes - whether a signup or login action is called.

```
@SuppressWarnings("serial")
public class StudentController extends HttpServlet {
	private StudentRepository studentRepository;

	private static String STUDENT_SIGNUP = "content/signup.jsp";
	private static String STUDENT_LOGIN = "content/login.jsp";
	private static String LOGIN_SUCCESS = "content/success.jsp";
	private static String LOGIN_FAILURE = "content/failure.jsp";

	/**
	 * @see HttpServlet#HttpServlet()
	 */
	public StudentController() {
		super();
		studentRepository = new StudentRepository();
	}

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse
	 *      response)
	 */
	protected void doGet(HttpServletRequest request,
			HttpServletResponse response) throws ServletException, IOException {	
		String forward = STUDENT_SIGNUP;
		RequestDispatcher view = request.getRequestDispatcher(forward);
		view.forward(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse
	 *      response)
	 */
	protected void doPost(HttpServletRequest request,
			HttpServletResponse response) throws ServletException, IOException {
		String pageName = request.getParameter("pageName");
		String forward = "";		
		
		if (studentRepository != null) {
			if (pageName.equals("signup")) {
				if (studentRepository.findByUserName(request
						.getParameter("userName"))) {
					request.setAttribute("message", "User Name exists. Try another user name");
					forward = STUDENT_SIGNUP;
					RequestDispatcher view = request
							.getRequestDispatcher(forward);
					view.forward(request, response);
					return;
				}

				studentRepository.save(request.getParameter("userName"),
						request.getParameter("password"),
						request.getParameter("firstName"),
						request.getParameter("lastName"),
						request.getParameter("dateOfBirth"),
						request.getParameter("emailAddress"));
				forward = STUDENT_LOGIN;
			} else if (pageName.equals("login")) {
				boolean result = studentRepository.findByLogin(
						request.getParameter("userName"),
						request.getParameter("password"));
				if (result == true) {
					forward = LOGIN_SUCCESS;
				} else {
					forward = LOGIN_FAILURE;
				}
			}
		}
		RequestDispatcher view = request.getRequestDispatcher(forward);
		view.forward(request, response);
	}
}
```

11. Create the DB Schema in a MYSQL DB
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

12. Deploying the Application on Tomcat Server
----------------------------------------------
Once the above steps are complete and the project is successfully built, the Java web application is ready to deployed on the Tomcat Server 7. 

The Java web application can be deployed locally by right clicking on the project and choosing the "Run As->Run on Server" option. 

The same can be deployed remotely on any native server that supports Tomcat by copying the WAR file (Right click on the project and choose Export as WAR File option) to /var/lib/tomcat7 folder (or appropriate tomcat directory) and restarting the tomcat server.

13. Clone or Download code
--------------------------
If using git, clone a copy of this project here: https://github.com/elizabetht/StudentEnrollmentWithJDBC.git

In case of not using git, download the project as ZIP or tar.gz file here: [https://github.com/elizabetht/StudentEnrollmentWithJDBC/releases/tag/1.2](https://github.com/elizabetht/StudentEnrollmentWithJDBC/releases/tag/1.2)
