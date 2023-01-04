## Complete Code Example

### /pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.5.3</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.codewithheeren.springboot</groupId>
	<artifactId>myfirstwebapp</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>myfirstwebapp</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>1.8</java.version>
		<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<jakarta-servlet-jsp-jstl.version>3.0.0</jakarta-servlet-jsp-jstl.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>jakarta.servlet.jsp.jstl</groupId>
			<artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.eclipse.jetty</groupId>
			<artifactId>glassfish-jstl</artifactId>
			<version>11.0.13</version>
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

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step17Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//starting task management project
//Creating todo table from welcome page.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step17Application {

	public static void main(String[] args) {
		SpringApplication.run(Step17Application.class, args);
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
import org.springframework.web.bind.annotation.SessionAttributes;
import com.codewithheeren.springboot.service.AuthenticationService;

@Controller
@SessionAttributes("name")
public class LoginController {
	
	@Autowired
	private AuthenticationService authenticationService;
	

	@RequestMapping(value="login",method = RequestMethod.GET)
	public String gotoLoginPage() {
		return "login";
	}

	@RequestMapping(value="login",method = RequestMethod.POST)
	public String gotoWelcomePage(@RequestParam String name, 
			@RequestParam String password, ModelMap model) {
		
		if(authenticationService.authenticate(name, password)) {
			model.put("name", name);
			//Authentication 
			//name - heeren	
			//password - password
			
			return "welcome";
		}
		
		model.put("errorMessage", "Invalid Credentials! Please try again.");
		return "login";
	}
}
```
---

### /src/main/java/com/codewithheeren/springboot/controller/TodoController.java

```java
package com.codewithheeren.springboot.controller;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttributes;
import com.codewithheeren.springboot.entity.Todo;
import com.codewithheeren.springboot.service.TodoService;

@Controller
@SessionAttributes("name")
public class TodoController {

	@Autowired
	private TodoService todoService;
		
	@RequestMapping("list-todos")
	public String listAllTodos(ModelMap model) {
		List<Todo> todos = todoService.findByUsername("heeren");
		model.addAttribute("todos", todos);
		
		return "listtodos";
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
### /src/main/java/com/codewithheeren/springboot/entity/Todo.java

```java
package com.codewithheeren.springboot.entity;

import java.time.LocalDate;

public class Todo {

	private int id;
	private String username;
	private String description;
	private LocalDate targetDate;
	private boolean done;

	public Todo(int id, String username, String description, LocalDate targetDate, boolean done) {
		super();
		this.id = id;
		this.username = username;
		this.description = description;
		this.targetDate = targetDate;
		this.done = done;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public LocalDate getTargetDate() {
		return targetDate;
	}

	public void setTargetDate(LocalDate targetDate) {
		this.targetDate = targetDate;
	}

	public boolean isDone() {
		return done;
	}

	public void setDone(boolean done) {
		this.done = done;
	}

	@Override
	public String toString() {
		return "Todo [id=" + id + ", username=" + username + ", description=" + description + ", targetDate="
				+ targetDate + ", done=" + done + "]";
	}

}

```
---
### /src/main/resources/templates/login.html

```
<html>
	<head>
		<title>Login Page</title>
	</head>
	<body>	
		<div class="container">
			<h1>Login</h1>
			<pre th:text="${errorMessage}" />
			<form method="post">
				Name: <input type="text" name="name">
				Password: <input type="password" name="password">
				<input type="submit">
			</form>
		</div>
		 
	</body>
</html>
```
---

### /src/main/resources/templates/welcome.html

```
<html>
	<head>
		<title>Welcome Page</title>
	</head>
	<body>
		<div class="container">
			<h1 th:text="'welcome '+${name}"></h1>
			<a href="list-todos">Manage</a> your todos
		</div>
	</body>
</html>
```
---
### /src/main/resources/templates/listtodos.html
```
<html>
	<head>
		<link href="webjars/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" >
		<title>List Todos Page</title>		
	</head>
	<body>
		<div class="container">
			<h1>Your Todos</h1>
			<table class="table">
				<thead>
					<tr>
						<th>id</th>
						<th>Description</th>
						<th>Target Date</th>
						<th>Is Done?</th>
					</tr>
				</thead>
				<tbody>		
				<tr th:each="todo: ${todos}">
							<td th:text="${todo.id}" />
							<td th:text="${todo.description}" />
							<td th:text="${todo.targetDate}" />
							<td th:text="${todo.done}" />
				</tr>
				</tbody>
			</table>
		
		</div>
		<script src="webjars/bootstrap/5.1.3/js/bootstrap.min.js"></script>
		<script src="webjars/jquery/3.6.0/jquery.min.js"></script>
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
package com.in28minutes.springboot.myfirstwebapp;

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
