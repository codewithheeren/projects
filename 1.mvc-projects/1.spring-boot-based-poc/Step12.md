## Complete Code Example

### /pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.5.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.codewithheeren.springboot</groupId>
	<artifactId>step11</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>step11</name>
	<description>Spring Boot web mvc based task management system.</description>
	<properties>
		<java.version>8</java.version>
		<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
	<repositories>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>
</project>
```
---

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step11Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//adding create account/Signup page.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step12Application {

	public static void main(String[] args) {
		SpringApplication.run(Step12Application.class, args);
	}
}

```
---

### /src/main/java/com/codewithheeren/springboot/controller/LoginController.java

```java
package com.codewithheeren.springboot.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import com.codewithheeren.springboot.service.AuthenticationService;

@Controller
public class LoginController {

	@Autowired
	private AuthenticationService authenticationService;

	@RequestMapping(value = "login", method = RequestMethod.GET)
	public String gotoLoginPage() {
		return "login";
	}

	@RequestMapping(value = "login", method = RequestMethod.POST)
	// login?name=codewithheeren&password=password
	public String gotoWelcomePage(@RequestParam String name, @RequestParam String password, ModelMap model) {

		if (authenticationService.authenticate(name, password)) {
			model.put("username", name);
			// Authentication
			// name - heeren
			// password - 123

			return "welcome";
		}
		return "login";
	}

	@RequestMapping(value = "signup", method = RequestMethod.GET)
	public String gotoSignUpPage() {
		return "signup";
	}

	@RequestMapping(value = "signup", method = RequestMethod.POST)
//      login?name=codewithheeren&password=password
	public String userProfile(@RequestParam String username, @RequestParam String password,
			@RequestParam String firstname, @RequestParam String lastname, ModelMap model) {
//		System.out.println(username);
//		System.out.println(firstname+" "+lastname);
		model.put("name", firstname + " " + lastname);
		model.put("username", username);
		return "welcome";
	}
}
```
---

### /src/main/java/com/codewithheeren/springboot/service/AuthenticationService.java

```java
package com.codewithheeren.springboot.service;
import org.springframework.stereotype.Service;

@Service
public class AuthenticationService {
	
	public boolean authenticate(String username, String password) {	
		boolean isValidUserName = username.equalsIgnoreCase("heeren");
		boolean isValidPassword = password.equalsIgnoreCase("123");		
		return isValidUserName && isValidPassword;
	}
}
```
---

### /src/main/resources/templates/login.html
```html
```
<html>
	<head>
		<title>Login Page</title>
		<style>
        #link {
             background-color: black;
 			 color: white;
  			 padding: 2px 10px;
             text-decoration: none;
        }
    	</style>
	</head>
	<body>
		Welcome to the login page!
		<form method="post">
			Name: <input type="text" name="name">
			Password: <input type="password" name="password">
			<input type="submit">
			<a id="link" href="signup">signup</a>
		</form>
	</body>
</html>
```
---

### /src/main/resources/templates/welcome.html
```html
```
<html>
	<head>
		<title>Welcome Page</title>
	</head>
	<body>
			<span th:text="'Welcome '"></span>
			<span th:text="${name} ?: 'CodeWithHeeren'"></span>  <!-- if name will be null then default value will be CodeWithHeeren -->
			<div th:text="'Your Username: '+${username}"></div>
	</body>
</html>
```
---
### /src/main/resources/templates/signup.html
```html
```
<!DOCTYPE html>
<html>
<style>
body {
	font-family: Arial, Helvetica, sans-serif;
}

input[type=text], input[type=password] {
	width: 100%;
	padding: 15px;
	margin: 5px 0 22px 0;
	display: inline-block;
	border: none;
	background: #f1f1f1;
}

input[type=text]:focus, input[type=password]:focus {
	background-color: #ddd;
	outline: none;
}

button {
	background-color: #04AA6D;
	color: white;
	padding: 14px 20px;
	margin: 8px 0;
	border: none;
	cursor: pointer;
	width: 100%;
	opacity: 0.9;
}

button:hover {
	opacity: 1;
}

.signupbtn {
	float: left;
	width: 10%;
}

.container {
	padding: 16px;
}
</style>
<body>
	<form method="post" action="/signup">
		<div class="container">
			<h1>Sign Up</h1>
			<p>Please fill in this form to create an account.</p>
			<label><b>Username</b></label> <input type="text" name="username"
				required> <label><b>Password</b></label> <input
				type="password" name="password" required> <label><b>Firstname</b></label><input
				type="text" name="firstname" required> <label><b>Lastname</b></label><input
				type="text" name="lastname" required>
			<button type="submit" class="signupbtn">Sign Up</button>
		</div>
	</form>
</body>
</html>
```
---

### /src/main/resources/application.properties

```properties
spring.mvc.view.suffix=.html
#logging.level.org.springframework=debug

```
---

### /src/test/java/com/codewithheeren/springboot/myfirstwebapp/Step11ApplicationTests.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Step11ApplicationTests {

	@Test
	void contextLoads() {
	}

}
```
---
