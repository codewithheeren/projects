## Complete Code Example

### /pom.xml

```xml
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
			<groupId>org.webjars</groupId>
			<artifactId>bootstrap</artifactId>
			<version>5.1.3</version>
		</dependency>
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>jquery</artifactId>
			<version>3.6.0</version>
		</dependency>
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>webjars-locator-core</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
</dependencies>
```
---

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step14Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//Profile page is populating user details.
//added User model class.
//added UserService to authenticate credentials and add new user.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step14Application {

	public static void main(String[] args) {
		SpringApplication.run(Step14Application.class, args);
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
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import com.codewithheeren.springboot.entity.User;
import com.codewithheeren.springboot.service.UserService;

@Controller
public class LoginController {

	@Autowired
	private UserService userService;

	@RequestMapping(value = "login", method = RequestMethod.GET)
	public String gotoLoginPage() {
		return "login";
	}

	@RequestMapping(value = "login", method = RequestMethod.POST)
	public String gotoWelcomePage(@RequestParam String username, @RequestParam String password, ModelMap model) {

		if (userService.authenticate(username, password)) {
			model.put("user", userService.getUser(username));
			return "profile";
		}
		return "login";
	}

	@RequestMapping(value = "signup", method = RequestMethod.GET)
	public String gotoSignUpPage() {
		return "signup";
	}

	@RequestMapping(value = "signup", method = RequestMethod.POST)
	public String userProfile(@ModelAttribute("user") User user, ModelMap model) {
		System.out.println(user);
		userService.addUser(user);
		model.put("user", user);
		return "profile";
	}

}
```
---
### /src/main/java/com/codewithheeren/springboot/entity/User.java
```java
package com.codewithheeren.springboot.entity;

public class User {
	private String username;
	private String password;
	private String firstname;
	private String lastname;

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getFirstname() {
		return firstname;
	}

	public void setFirstname(String firstname) {
		this.firstname = firstname;
	}

	public String getLastname() {
		return lastname;
	}

	public void setLastname(String lastname) {
		this.lastname = lastname;
	}

	public User() {
		super();
	}

	public User(String username, String password, String firstname, String lastname) {
		super();
		this.username = username;
		this.password = password;
		this.firstname = firstname;
		this.lastname = lastname;
	}

	@Override
	public String toString() {
		return "User [username=" + username + ", password=" + password + ", firstname=" + firstname + ", lastname="
				+ lastname + "]";
	}
}

```
---

### /src/main/java/com/codewithheeren/springboot/service/AuthenticationService.java

```java
package com.codewithheeren.springboot.service;
import java.util.HashMap;
import java.util.Map;

import org.springframework.stereotype.Service;

@Service
public class AuthenticationService {
	private static Map<String,String> map = new HashMap<>();
	
	public void addCredentials(String username, String password) {
		map.put(username,password);
	}
	public boolean authenticate(String username, String password) {
		String existingPassword = map.get(username);
		if(existingPassword != null) {
		boolean isValidPassword = password.equals(existingPassword);		
			if(isValidPassword)
				return true;
		}
		return false;
	}
}
```
---
### /src/main/java/com/codewithheeren/springboot/service/UserService.java

```java
package com.codewithheeren.springboot.service;

import java.util.HashMap;
import java.util.Map;
import org.springframework.stereotype.Service;
import com.codewithheeren.springboot.entity.User;

@Service
public class UserService {
	private static Map<String, User> users = new HashMap<>();

	public void addUser(User user) {
		users.put(user.getUsername(), user);
	}
	
	public User getUser(String username) {
		return users.get(username);
	}

	public boolean authenticate(String username, String password) {
		User existingUser = users.get(username);
		if (existingUser != null) {
			boolean isValidPassword = password.equals(existingUser.getPassword());
			if (isValidPassword)
				return true;
		}
		return false;
	}
}
```
---
### /src/main/resources/templates/login.html
```html
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
		Name: <input type="text" name="username"> Password: <input
			type="password" name="password"> <input type="submit">
		<a id="link" href="signup">signup</a>
	</form>
</body>
</html>
```
---

### /src/main/resources/templates/profile.html
```html
<html xmlns:th="http://www.thymeleaf.org">
<head>
<title>Profile Page</title>
</head>
<body>
	<span th:text="'Welcome '+${user.firstname}+' '+${user.lastname}"></span>
	<a href="/login"> <input type="button" style="float: right;"
		value="logout" />
	</a>
	<div th:text="'Your firstname: '+${user.firstname}"></div>
	<div th:text="'Your lastname: '+${user.lastname}"></div>
	<div th:text="'Your username: '+${user.username}"></div>
</body>
</html>
```
---
### /src/main/resources/templates/signup.html
```html
<html xmlns:th="http://www.thymeleaf.org">
<style>
body {
	font-family: Arial, Helvetica, sans-serif;
}

/* Full-width input fields */
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

/* Set a style for all buttons */
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

/* Float cancel and signup buttons and add an equal width */
.signupbtn {
	float: left;
	width: 10%;
}

/* Add padding to container elements */
.container {
	padding: 16px;
}
</style>
<body>
	<div class="container">
		<form method="post" th:action="@{/signup}" th:object="${user}">
			<h1>Sign Up</h1>
			<p>Please fill in this form to create an account.</p>
			<label><b>Username</b></label> <input type="text" name="username"
				required> <label><b>Password</b></label> <input
				type="password" name="password" required> <label><b>Firstname</b></label><input
				type="text" name="firstname" required> <label><b>Lastname</b></label><input
				type="text" name="lastname" required>
			<button type="submit" class="signupbtn">Sign Up</button>
		</form>
	</div>
</body>
</html>

```
---

### /src/main/resources/application.properties

```properties
spring.mvc.view.suffix = .html
logging.level.org.springframework = debug

```
---

