---
layout: post
title: "Building Jersey2 REST Client Using Spring with JUnit and JMock"
author: swapna
date: 2013-12-23 12:10:30 -0600
comments: true
categories: [Hibernate, Spring, Maven, Jersey2, JUnit/JMock, EclEmma, Java]
---
This post will show how to create a Jersey2 REST Client in a Spring environment and test the same using JUnit and JMock frameworks. The details of the actual application are explained in the earlier post given by the link [Building Java Web Application Using Jersey REST With Spring](http://meygam.github.io/blog/2013/12/13/student-enrollment-using-jersey-rest-with-spring/).

<!-- more -->

1. Update pom.xml
-----------------
To make the Maven Java Web Application project ([Building Java Web Application Using Jersey REST With Spring](http://meygam.github.io/blog/2013/12/13/student-enrollment-using-jersey-rest-with-spring/)) support the JUnit testing framework, add the following dependency to the existing pom.xml

```
<dependency>
	<groupId>org.jmock</groupId>
	<artifactId>jmock-junit4</artifactId>
	<version>2.6.0</version>
</dependency>
```

2. Create packages for Client tier classes
-------------------------------------------
Create package for the Jersey Client classes under the src/main/java folder. 

A sample snapshot of the project after the package creation is as shown below:

![Jersey REST Client Package Layout](/assets/images/elizabetht/jersey-client-package.png "Jersey REST Client Package Layout") 

3. Create classes for Client tier
---------------------------------
Create an interface class named StudentClientInterface.java inside the package com.github.elizabetht.client to support the client operations.

```
public interface StudentClientInterface {

	public Response getSignup();

	public Response postSignup(String userName, String password,
			String firstName, String lastName, String dateOfBirth,
			String emailAddress) throws Exception;

	public Response getLogin();

	public Response postLogin(String userName, String password);

}
```

Create a client tier implementation class (a POJO indeed) named StudentClient.java inside the package com.github.elizabetht.client. This is where the client logic goes - either to access the GET/POST methods in the StudentResource.java class.

```
public class StudentClient implements StudentClientInterface {
	private WebTarget target;

	public StudentClient(WebTarget target) {
		this.target = target;
	}

	public Response getSignup() {		
		Response response = target.path("signup").request().get(Response.class);
		return response;
	}

	public Response postSignup(String userName, String password,
			String firstName, String lastName, String dateOfBirth,
			String emailAddress) throws Exception {
		
		Form form = new Form().param("userName", userName)
				.param("password", password).param("firstName", firstName)
				.param("lastName", lastName).param("dateOfBirth", dateOfBirth)
				.param("emailAddress", emailAddress);
		Response response = target.path("signup").request()
				.post(Entity.form(form));

		if (response.getStatus() == Status.INTERNAL_SERVER_ERROR
				.getStatusCode()) {
			throw new Exception();
		}

		if (response.getStatus() != Status.OK.getStatusCode()) {
			throw new RuntimeException();
		}

		return response;
	}

	public Response getLogin() {
		Response response = target.path("login").request().get(Response.class);

		return response;
	}

	public Response postLogin(String userName, String password) {
		Form form = new Form().param("userName", userName).param("password",
				password);

		Response response = target.path("login").request()
				.post(Entity.form(form));

		if (response.getStatus() != Status.OK.getStatusCode()) {
			throw new RuntimeException();
		}

		return response;
	}
}
```

4. Create packages for Client Test tier classes
------------------------------------------------
Create package for the Client Test classes by right-clicking on the StudentClient.java class and choosing "New->JUnit Test Case" option. Specify the source folder as StudentEnrollmentWithREST/src/test/java folder (create the src/test/java folder if the folder does not exist) and specify the name for the test class. 

A sample snapshot of the project after the package creation is as shown below:

![Jersey REST Client Test Package Layout](/assets/images/elizabetht/jersey-client-test-package.png "Jersey REST Client Test Package Layout") 

5. Extract Interfaces for Model and Resource tier classes
----------------------------------------------------------
In order to support the use of JMock mocking framework alongside JUnit test cases, extract the interfaces out of the Model(Student.java) and Resource (StudentResource.java) tier classes. This is necessary in order to mock the objects.

Extracting interfaces can be done by right-clicking on the class file and choosing "Refactor->Extract Interface" option. 

The StudentInterface.java (in src/main/java/com.github.elizabetht.model package) after the extraction looks as below.

```
public interface StudentInterface {

	public Long getId();

	public void setId(Long id);

	public String getUserName();

	public void setUserName(String userName);

	public String getFirstName();

	public void setFirstName(String firstName);

	public String getLastName();

	public void setLastName(String lastName);

	public String getPassword();

	public void setPassword(String password);

	public String getEmailAddress();

	public void setEmailAddress(String emailAddress);

	public Date getDateOfBirth();

	public void setDateOfBirth(Date dateOfBirth);

}
```

Similarly, the StudentResourceInterface.java (in src/main/java/com.github.elizabetht.resource package) looks as below.

```
public interface StudentResourceInterface {

	public Response signup();

	public Response signup(String userName, String password, String firstName,
			String lastName, String dateOfBirth, String emailAddress)
			throws ParseException;

	public Response login();

	public Response login(String userName, String password);

}
```

6. Create Unit Test cases for StudentClient class
--------------------------------------------------
As a part of unit testing, each of the unit must be testable individually. While in reality, each unit would depend on other modules. This is where JMock comes into picture - to help to test a unit individually by mocking the dependencies.

@Before annotation helps to define the environment that needs to be setup before running each test case. In this example, the context and mock objects are setup using the @Before annotation.

```
public class StudentClientUnitTest {
	private Mockery context;
	private StudentClient studentClient;
	private WebTarget target;
	private Builder builder;

	@Before
	public void beforeEachTest() {
		context = new Mockery();
		studentClient = new StudentClient(
				ClientBuilder
						.newClient()
						.target("http://localhost:8080/StudentEnrollmentWithREST/webapi/studentResource/"));
		target = context.mock(WebTarget.class);
		builder = context.mock(Builder.class);
	}

	@Test
	public void getSignupTest() {
		studentClient = new StudentClient(target);

		final Response response = Response.ok(new Viewable("/signup")).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).get(Response.class);
				will(returnValue(response));
			}
		});
		studentClient.getSignup();
		assertEquals(response.getStatus(), Status.OK.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test
	public void postSignupTest() throws Exception {
		String userName = "jersey";
		String password = "jersey";
		String firstName = "jersey";
		String lastName = "jersey";
		String dateOfBirth = "12-21-2013";
		String emailAddress = "jersey@gmail.com";
		studentClient = new StudentClient(target);

		final Response response = Response.ok(new Viewable("/success")).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postSignup(userName, password, firstName, lastName,
				dateOfBirth, emailAddress);
		assertEquals(response.getStatus(), Status.OK.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test(expected = Exception.class)
	public void postSignupInvalidDateFormatTest() throws Exception {
		String userName = "jersey";
		String password = "jersey";
		String firstName = "jersey";
		String lastName = "jersey";
		String dateOfBirth = "12/21/2013";
		String emailAddress = "jersey@gmail.com";
		studentClient = new StudentClient(target);

		final Response response = Response.status(Status.PRECONDITION_FAILED)
				.build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postSignup(userName, password, firstName, lastName,
				dateOfBirth, emailAddress);
		assertEquals(response.getStatus(), Status.PRECONDITION_FAILED.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test(expected = Exception.class)
	public void postSignupBadRequestTest() throws Exception {
		String userName = null;
		String password = null;
		String firstName = null;
		String lastName = null;
		String dateOfBirth = null;
		String emailAddress = null;
		studentClient = new StudentClient(target);

		final Response response = Response.status(Status.PRECONDITION_FAILED)
				.build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postSignup(userName, password, firstName, lastName,
				dateOfBirth, emailAddress);
		assertEquals(response.getStatus(), Status.PRECONDITION_FAILED.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test(expected = RuntimeException.class)
	public void postSignupExistingUserTest() throws Exception {
		String userName = "jersey";
		String password = "jersey";
		String firstName = "jersey";
		String lastName = "jersey";
		String dateOfBirth = "12/21/2013";
		String emailAddress = "jersey@gmail.com";
		studentClient = new StudentClient(target);

		final Response response = Response.status(Status.BAD_REQUEST).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postSignup(userName, password, firstName, lastName,
				dateOfBirth, emailAddress);
		assertEquals(response.getStatus(), Status.BAD_REQUEST.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test
	public void getLoginTest() {
		studentClient = new StudentClient(target);

		final Response response = Response.ok(new Viewable("/login")).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).get(Response.class);
				will(returnValue(response));
			}
		});
		studentClient.getLogin();
		assertEquals(response.getStatus(), Status.OK.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test
	public void postLoginTest() {
		String userName = "jersey";
		String password = "jersey";
		studentClient = new StudentClient(target);

		final Response response = Response.ok(new Viewable("/success")).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postLogin(userName, password);
		assertEquals(response.getStatus(), Status.OK.getStatusCode());

		context.assertIsSatisfied();
	}
	
	@Test(expected=RuntimeException.class)
	public void postLoginInvalidTest() {
		String userName = "jersey";
		String password = "jersey123";
		studentClient = new StudentClient(target);

		final Response response = Response.status(Status.BAD_REQUEST).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postLogin(userName, password);
		assertEquals(response.getStatus(), Status.BAD_REQUEST.getStatusCode());

		context.assertIsSatisfied();
	}
	
	@Test(expected=RuntimeException.class)
	public void postLoginBadRequestTest() {
		String userName = null;
		String password = null;
		studentClient = new StudentClient(target);

		final Response response = Response.status(Status.PRECONDITION_FAILED).build();
		context.checking(new Expectations() {
			{
				oneOf(target).path(with(any(String.class)));
				will(returnValue(target));
				oneOf(target).request();
				will(returnValue(builder));
				oneOf(builder).post(with(any(Entity.class)));
				will(returnValue(response));
			}
		});
		studentClient.postLogin(userName, password);
		assertEquals(response.getStatus(), Status.PRECONDITION_FAILED.getStatusCode());

		context.assertIsSatisfied();
	}
}
```

7. Create Unit Test cases for StudentResource class
----------------------------------------------------
In similar lines, create unit test cases for StudentResource class by mocking the external dependencies which the class depends on for its operation.

```
public class StudentResourceUnitTest {
	private Mockery context;
	private StudentService studentService;
	private StudentResourceInterface studentResourceInterface;

	@Before
	public void beforeEachTest() throws Exception {
		context = new Mockery();
		studentService = context.mock(StudentService.class);
		studentResourceInterface = new StudentResource();

		Field field = studentResourceInterface.getClass().getDeclaredField(
				"studentService");
		field.setAccessible(true);
		field.set(studentResourceInterface, studentService);
	}

	@Test
	public void postSignupResourceTest() throws ParseException {
		final String userName = "jersey";
		String password = "jersey";
		String firstName = "jersey";
		String lastName = "jersey";
		String dateOfBirth = "12/21/2013";
		String emailAddress = "jersey@gmail.com";
		final StudentInterface student = context.mock(StudentInterface.class);

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByUserName(userName);
				will(returnValue(false));
				oneOf(studentService).save(with(any(StudentInterface.class)));
				will(returnValue(student));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);

		context.assertIsSatisfied();
	}

	@Test
	public void postSignupResourceForExistingUserTest() throws ParseException {
		final String userName = "jersey";
		String password = "jersey";
		String firstName = "jersey";
		String lastName = "jersey";
		String dateOfBirth = "12/21/2013";
		String emailAddress = "jersey@gmail.com";

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByUserName(userName);
				will(returnValue(true));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);

		context.assertIsSatisfied();
	}

	@Test
	public void postSignupResourceForBadRequestTest() throws ParseException {
		String userName = null;
		String password = null;
		String firstName = null;
		String lastName = null;
		String dateOfBirth = null;
		String emailAddress = null;
		final StudentResourceInterface studentResourceInterface = context
				.mock(StudentResourceInterface.class);
		final Response response = Response.status(Status.PRECONDITION_FAILED)
				.build();
		final Response response1 = Response.ok().entity(new Viewable("/login"))
				.build();

		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).signup(
						with(aNull(String.class)), with(aNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		userName = "jersey";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).signup(
						with(aNonNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)),
						with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		password = "jersey";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).signup(
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		firstName = "jersey";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).signup(
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)),
						with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);

		lastName = "jersey";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).signup(
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNull(String.class)), with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		dateOfBirth = "12/20/2013";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface)
						.signup(with(aNonNull(String.class)),
								with(aNonNull(String.class)),
								with(aNonNull(String.class)),
								with(aNonNull(String.class)),
								with(aNonNull(String.class)),
								with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		emailAddress = "jersey@gmail.com";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).signup(
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)),
						with(aNonNull(String.class)));
				will(returnValue(response1));
			}
		});
		studentResourceInterface.signup(userName, password, firstName,
				lastName, dateOfBirth, emailAddress);
		assertEquals(response1.getStatus(), Status.OK.getStatusCode());

		context.assertIsSatisfied();
	}

	@Test
	public void postLoginResourceTest() throws ParseException {
		final String userName = "jersey";
		final String password = "jersey";

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByLogin(userName, password);
				will(returnValue(true));
			}
		});
		studentResourceInterface.login(userName, password);

		final String password1 = "jersey123";
		context.checking(new Expectations() {
			{
				oneOf(studentService).findByLogin(userName, password1);
				will(returnValue(false));
			}
		});
		studentResourceInterface.login(userName, password1);

		context.assertIsSatisfied();
	}

	@Test
	public void postLoginResourceForBadRequestTest() throws ParseException {
		final String userName = null;
		final String password = null;
		final StudentResourceInterface studentResourceInterface = context
				.mock(StudentResourceInterface.class);
		final Response response = Response.status(Status.PRECONDITION_FAILED)
				.build();
		final Response response1 = Response.ok().entity(new Viewable("/login"))
				.build();

		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).login(
						with(aNull(String.class)), with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.login(userName, password);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		final String userName1 = "jersey";

		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface)
						.login(with(aNonNull(String.class)),
								with(aNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.login(userName1, password);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		final String password1 = "jersey";
		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface)
						.login(with(aNull(String.class)),
								with(aNonNull(String.class)));
				will(returnValue(response));
			}
		});
		studentResourceInterface.login(userName, password1);
		assertEquals(response.getStatus(),
				Status.PRECONDITION_FAILED.getStatusCode());

		context.checking(new Expectations() {
			{
				oneOf(studentResourceInterface).login(
						with(aNonNull(String.class)),
						with(aNonNull(String.class)));
				will(returnValue(response1));
			}
		});
		studentResourceInterface.login(userName1, password1);
		assertEquals(response1.getStatus(), Status.OK.getStatusCode());

		context.assertIsSatisfied();
	}
}
```

8. Create Unit Test cases for StudentService class
----------------------------------------------------
Create unit test cases for StudentService class by mocking the external dependencies which the class depends on for its operation.

```
public class StudentServiceUnitTest {
	private Mockery context;
	private StudentRepository studentRepository;
	private StudentService studentService;

	@Before
	public void beforeEachTest() throws Exception {
		context = new Mockery();
		studentRepository = context.mock(StudentRepository.class);
		studentService = new StudentServiceImpl();

		Field field = studentService.getClass().getDeclaredField(
				"studentRepository");
		field.setAccessible(true);
		field.set(studentService, studentRepository);
	}

	@Test
	public void findByLoginTest() {
		final String userName = "j2eee";
		final String password = "j2ee";
		final StudentInterface studentInterface = null;

		context.checking(new Expectations() {
			{
				oneOf(studentRepository).findByUserName(userName);
				will(returnValue(studentInterface));
			}
		});
		studentService.findByLogin(userName, password);
		assertNull(studentInterface);

		final String userName1 = "j2ee";
		final StudentInterface studentInterface1 = context
				.mock(StudentInterface.class);

		context.checking(new Expectations() {
			{
				oneOf(studentRepository).findByUserName(userName1);
				will(returnValue(studentInterface1));
				oneOf(studentInterface1).getPassword();
				will(returnValue(password));
			}
		});
		studentService.findByLogin(userName1, password);
		assertNotNull(studentInterface1);
		assertEquals("j2ee", password);

		final String password1 = "j2eee";

		context.checking(new Expectations() {
			{
				oneOf(studentRepository).findByUserName(userName1);
				will(returnValue(studentInterface1));
				oneOf(studentInterface1).getPassword();
				will(returnValue(password1));
			}
		});
		studentService.findByLogin(userName1, password1);
		assertNotNull(studentInterface1);
		assertNotEquals("j2ee", password1);

		context.assertIsSatisfied();
	}

	@Test
	public void findByLoginWithNullParametersTest() {
		final String userName = null;
		final String password = null;
		studentService = context.mock(StudentService.class);

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByLogin(with(aNull(String.class)),
						with(aNull(String.class)));
				will(returnValue(false));
			}
		});
		studentService.findByLogin(userName, password);

		final String userName1 = "j2ee";

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByLogin(with(aNonNull(String.class)),
						with(aNull(String.class)));
				will(returnValue(false));
			}
		});
		studentService.findByLogin(userName1, password);

		final String password1 = "j2eee";

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByLogin(with(aNull(String.class)),
						with(aNonNull(String.class)));
				will(returnValue(false));
			}
		});
		studentService.findByLogin(userName, password1);

		context.checking(new Expectations() {
			{
				oneOf(studentService).findByLogin(with(aNonNull(String.class)),
						with(aNonNull(String.class)));
				will(returnValue(false));
			}
		});
		studentService.findByLogin(userName1, password1);

		context.assertIsSatisfied();
	}

	@Test
	public void findByEmptyLoginTest() {
		final String userName = "";
		final String password = "";
		final StudentInterface studentInterface = null;

		context.checking(new Expectations() {
			{
				oneOf(studentRepository).findByUserName(userName);
				will(returnValue(studentInterface));
			}
		});
		studentService.findByLogin(userName, password);
		assertNull(studentInterface);

		context.assertIsSatisfied();
	}

	@Test
	public void findByUserNameTest() {
		final String userName = "j2ee";

		context.checking(new Expectations() {
			{
				oneOf(studentRepository)
						.findByUserName(with(any(String.class)));
				will(returnValue(with(any(StudentInterface.class))));
			}
		});
		studentService.findByUserName(userName);

		context.assertIsSatisfied();
	}

	@Test
	public void findByBadUserNameTest() {
		final String userName = "j2eee";
		final StudentInterface studentInterface = null;

		context.checking(new Expectations() {
			{
				oneOf(studentRepository)
						.findByUserName(with(any(String.class)));
				will(returnValue(studentInterface));
			}
		});
		studentService.findByUserName(userName);
		assertNull(studentInterface);

		final String userName1 = "j2ee";
		final StudentInterface studentInterface1 = context
				.mock(StudentInterface.class);

		context.checking(new Expectations() {
			{
				oneOf(studentRepository).findByUserName(userName1);
				will(returnValue(studentInterface1));
			}
		});
		studentService.findByUserName(userName1);
		assertNotNull(studentInterface1);

		context.assertIsSatisfied();
	}
}
```

9. Running Test cases
----------------------
Any of the test case or test class can be run by right clicking on the name of the test case or test class and choosing "Run As->JUnit test case". Each of the test case developed should be tested to give a success output (indicated by the green bar in the JUnit output)

10. Code Coverage
------------------
Using tools like EclEmma, code coverage for the project can be measured. To install EclEmma, choose "Help->Eclipse Marketplace" and search for EclEmma in the search toolbar. Install the tool using the steps on-screen.

Once the tool is installed, the code coverage for the project can be measured by choosing "Coverage As->JUnit test case" from the right click options on the project.

Achieving a code coverage of about or above 80% is normally preferred according to industrial standards. Not only writing unit test cases for existing code, but developing test scenarios for which code is not in place is an important step in Test Driven Development (TDD). By iteratively following the TDD approach, the stability of the code could be significantly improved.

11. Clone or Download code
--------------------------
If using git, clone a copy of this project here: [https://github.com/elizabetht/StudentEnrollmentWithREST.git](https://github.com/elizabetht/StudentEnrollmentWithREST.git)

In case of not using git, download the project as ZIP or tar.gz file here: [https://github.com/elizabetht/StudentEnrollmentWithREST/releases/tag/1.2](https://github.com/elizabetht/StudentEnrollmentWithREST/releases/tag/1.2)


