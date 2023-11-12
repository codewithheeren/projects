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

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step15Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//populating error message on login page for wrong credentials.
//server side validations added on User class.
//Added bootstrap, jscript and jquery to view page.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step15Application {

	public static void main(String[] args) {
		SpringApplication.run(Step15Application.class, args);
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

	@RequestMapping(value="login",method = RequestMethod.GET)
	public String gotoLoginPage() {
		return "login";
	}

	@RequestMapping(value="login",method = RequestMethod.POST)
	public String gotoWelcomePage(@RequestParam String username, @RequestParam String password, ModelMap model) {
		
		if(userService.authenticate(username, password)) {
			model.put("user",userService.getUser(username));
			return "profile";
		}
		model.put("errorMessage", "Invalid Credentials! Please try again.");
		return "login";
	}
	
	@RequestMapping(value="signup",method = RequestMethod.GET)
	public String gotoSignUpPage(ModelMap model) {
		//-----------------------Don't forget to add this user object--------------------------
		model.put("user", new User());
		return "signup";
	}
	
	@RequestMapping(value="signup",method = RequestMethod.POST)  
	//Note- always put binding result object just after pojo object.
	public String userProfile(@Valid @ModelAttribute("user") User user,BindingResult result ,ModelMap model) { 
		model.put("user", user);
		if(result.hasErrors()) {
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

import javax.validation.constraints.Size;

public class User {
	@Size(min=10, message="Enter atleast 10 characters")
	private String username;
	@Size(min=8, message="password must be minimum 8 characters")
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
	Welcome to the login page!
	<form method="post">
		Name: <input type="text" name="username"> Password: <input
			type="password" name="password"> <input type="submit">
		<a id="link" href="signup">signup</a>
		<pre th:text="${errorMessage}" style="color: red;"></pre>
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
<head>
	<link href="webjars/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" >
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
                        <label for="firstname">FirstName:</label>
                        <input type="text" class="form-control" placeholder="Firstname..." name="firstname"  th:value="${user.firstname}">
                 </div>
         		 <div class="form-group">
                        <label for="firstname">LastName:</label>
                        <input type="text" class="form-control" placeholder="Lastname..." name="lastname"  th:value="${user.lastname}">
                 </div>
        		 <div class="form-group">
          				<label for="email">Username</label>
          				<input type="email" class="form-control" th:field="*{username}"  placeholder="Enter email" }>  
          				<div class="text-warning" th:each="e : ${#fields.errors('username')}" th:text="${e}"></div>    
        		</div>
        		
        		<div class="form-group">
          				<label for="password">Password</label>
          				<input type="password" class="form-control" th:field="*{password}" placeholder="Enter password">
          				<div class="text-warning" th:if="${#fields.hasErrors('password')}" th:errors="*{password}"></div>
        		</div>
                   
                    <button type="submit" class="btn btn-primary btn-customized mt-4" >Signup</button>
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

