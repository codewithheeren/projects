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

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step13Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//Enabling new account credentials for login.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step13Application {

	public static void main(String[] args) {
		SpringApplication.run(Step13Application.class, args);
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
	public String gotoWelcomePage(@RequestParam String name, @RequestParam String password, ModelMap model) {

		if (authenticationService.authenticate(name, password)) {
			model.put("username", name);
			return "welcome";
		}
		return "login";
	}

	@RequestMapping(value = "signup", method = RequestMethod.GET)
	public String gotoSignUpPage() {
		return "signup";
	}

	@RequestMapping(value = "signup", method = RequestMethod.POST)
	// login?name=codewithheeren&password=password
	public String userProfile(@RequestParam String username, @RequestParam String password,
			@RequestParam String firstname, @RequestParam String lastname, ModelMap model) {
		authenticationService.addCredentials(username, password);
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

import java.util.HashMap;
import java.util.Map;
import org.springframework.stereotype.Service;

@Service
public class AuthenticationService {

	private static Map<String, String> map = new HashMap<>();

	public void addCredentials(String username, String password) {
		map.put(username, password);
	}

	public boolean authenticate(String username, String password) {
		String existingPassword = map.get(username);
		if (existingPassword != null) {
			boolean isValidPassword = password.equals(existingPassword);
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
		Name: <input type="text" name="name"> Password: <input
			type="password" name="password"> <input type="submit">
		<a id="link" href="signup">signup</a>
	</form>
</body>
</html>
```
---

### /src/main/resources/templates/welcome.html
```html
<html>
<head>
<title>Welcome Page</title>
</head>
<body>
	<span th:text="'Welcome '"></span>
	<span th:text="${name} ?: 'To Your Profile Page'"></span>
	<!-- if name will be null then default value will be CodeWithHeeren -->
	<a href="/login"> <input type="button" style="float: right;"
		value="logout" />
	</a>
	<div th:text="'Your Username: '+${username}"></div>
</body>
</html>
```
---
### /src/main/resources/templates/signup.html
```html
<!DOCTYPE html>
<html>
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
spring.mvc.view.suffix = .html
logging.level.org.springframework = debug

```
---

