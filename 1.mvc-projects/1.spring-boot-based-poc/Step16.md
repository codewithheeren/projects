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
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
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

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step16Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//Assignment - create profile page with good look and feel.
//adding more validations in User entity class.
//add todo link in profile page.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step16Application {

	public static void main(String[] args) {
		SpringApplication.run(Step16Application.class, args);
	}
}

```
---

### /src/main/java/com/codewithheeren/springboot/controller/LoginController.java

```java
package com.codewithheeren.springboot.controller;

import javax.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.validation.BindingResult;
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
		model.put("errorMessage", "Invalid Credentials! Please try again.");
		return "login";
	}

	@RequestMapping(value = "signup", method = RequestMethod.GET)
	public String gotoSignUpPage(ModelMap model) {
		// -----------------------Don't forget to add this user object--------------------------
		model.put("user", new User());
		return "signup";
	}

	@RequestMapping(value = "signup", method = RequestMethod.POST) 
	// Note- always put binding result object just after pojo object.
	public String userProfile(@Valid @ModelAttribute("user") User user, BindingResult result, ModelMap model) {
		model.put("user", user);
		if (result.hasErrors()) {
			return "signup";
		}
		userService.addUser(user);
		return "profile";
	}

}
```
---

### /src/main/java/com/codewithheeren/springboot/entity/User.java
```java
package com.codewithheeren.springboot.entity;

import javax.validation.constraints.Email;
import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;

public class User {
	
	@Email(message="Username must be valid email.")
	@NotEmpty(message="Username can not be empty")
	private String username;
	
	@Size(min=8, message="Password must be minimum 8 characters")
	@Size(max=15, message="Password must not be more then 15 characters")
	private String password;
	
	@NotEmpty(message="Firstname must not empty")
	@NotBlank(message="Firstname must have at least one character")
	@Pattern(regexp="^(?=.{1,40}$)[a-zA-Z]+(?:[-'\\s][a-zA-Z]+)*$",message="Firstname must not contain any number")
	private String firstname;
	
	@NotEmpty(message="Lastname must not empty")
	@NotBlank(message="Lastname must have at least one character")
	@Pattern(regexp="^(?=.{1,40}$)[a-zA-Z]+(?:[-'\\s][a-zA-Z]+)*$",message="Firstname must not contain any number")
	private String lastname;
	
	@Min(message="Age must be minimum 18 yrs", value = 18)
	@Max(message="Age must not be more then 50 yrs", value = 50)
	private int age;

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

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	@Override
	public String toString() {
		return "User [username=" + username + ", password=" + password + ", firstname=" + firstname + ", lastname="
				+ lastname + ", age=" + age + "]";
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
<html xmlns:th="http://www.thymeleaf.org">
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
	<div class="container">
		Welcome to the login page!
		<form method="post">
			Name: <input type="text" name="username"> Password: <input
				type="password" name="password"> <input type="submit">
			<a id="link" href="signup">signup</a>
			<pre th:text="${errorMessage}" style="color: red;"></pre>
		</form>
	</div>
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
<head>
<link href="webjars/bootstrap/5.1.3/css/bootstrap.min.css"
	rel="stylesheet">
<title>SignUp</title>
</head>
<body class="h-100">
	<div class="container h-100">
		<div class="row h-100 justify-content-center align-items-center">
			<div class="col-10 col-md-8 col-lg-4">
				<!-- Form Starts-->
				<form method="post" th:action="@{/signup}" th:object="${user}">
					<h1 class="text-center">Sign Up</h1>

					<div class="form-group">
						<label for="email">Username</label> <input type="email"
							class="form-control" th:field="*{username}"
							placeholder="Enter email">
						<div class="text-warning"
							th:each="e : ${#fields.errors('username')}" th:text="${e}"></div>
					</div>

					<div class="form-group">
						<label for="password">Password</label> <input type="password"
							class="form-control" th:field="*{password}"
							placeholder="Enter password">
						<div class="text-warning" th:if="${#fields.hasErrors('password')}"
							th:errors="*{password}"></div>
					</div>

					<div class="form-group">
						<label for="firstname">FirstName:</label> <input type="text"
							class="form-control" placeholder="Firstname..."
							th:field="*{firstname}">
						<div class="text-warning"
							th:if="${#fields.hasErrors('firstname')}"
							th:errors="*{firstname}"></div>
					</div>

					<div class="form-group">
						<label for="firstname">LastName:</label> <input type="text"
							class="form-control" placeholder="Lastname..."
							th:field="*{lastname}">
						<div class="text-warning" th:if="${#fields.hasErrors('lastname')}"
							th:errors="*{lastname}"></div>
					</div>

					<div class="form-group">
						<label for="age">Age:</label> <input type="text"
							class="form-control" placeholder="Age In Years..."
							th:field="*{age}">
						<div class="text-warning" th:if="${#fields.hasErrors('age')}"
							th:errors="*{age}"></div>
					</div>


					<button type="submit" class="btn btn-primary btn-customized mt-4">Signup</button>
				</form>
				<!-- Form end -->
			</div>
		</div>
	</div>
	<script src="webjars/bootstrap/5.1.3/js/bootstrap.min.js"></script>
	<script src="webjars/jquery/3.6.0/jquery.min.js"></script>
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