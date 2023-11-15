## Complete Code Example

### pom.xml

```xml
<properties>
		<java.version>1.8</java.version>
		<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
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
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>bootstrap-datepicker</artifactId>
			<version>1.9.0</version>
		</dependency>
</dependencies>
```
---

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step21Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//Adding Target Date Field to Todo Page and update toda page
//Task - Add drop down for 'IS DONE' on update todo page, is done should show completed or pending instead of true or false using thymeleaf tag.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step21Application {

	public static void main(String[] args) {
		SpringApplication.run(Step21Application.class, args);
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
import org.springframework.web.bind.annotation.SessionAttributes;
import com.codewithheeren.springboot.entity.User;
import com.codewithheeren.springboot.service.UserService;

@Controller
@SessionAttributes("name")
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
			model.put("name", username);
			return "profile";
		}
		model.put("errorMessage", "Invalid Credentials! Please try again.");
		return "login";
	}

	@RequestMapping(value = "signup", method = RequestMethod.GET)
	public String gotoSignUpPage(ModelMap model) {
		model.put("user", new User());
		return "signup";
	}

	@RequestMapping(value = "signup", method = RequestMethod.POST)
	public String userProfile(@Valid @ModelAttribute("user") User user, BindingResult result, ModelMap model) {
		model.put("user", user);
		model.put("name", user.getUsername());
		if (result.hasErrors()) {
			return "signup";
		}
		userService.addUser(user);
		return "profile";
	}

}
```
---
### /src/main/java/com/codewithheeren/springboot/controller/TodoController.java

```java
package com.codewithheeren.springboot.controller;

import java.time.LocalDate;
import java.util.List;
import javax.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttributes;
import com.codewithheeren.springboot.entity.Todo;
import com.codewithheeren.springboot.service.TodoService;
//import javax.validation.Valid;
//don't import this
//import jakarta.validation.Valid;

@Controller
@SessionAttributes("name")
public class TodoController {

	@Autowired
	TodoService todoService;

	@RequestMapping("list-todos")
	public String listAllTodos(ModelMap model) {
		List<Todo> todos = todoService.findByUsername("codewithheeren");
		model.addAttribute("todos", todos);
		return "listTodos";
	}

	@RequestMapping(value = "add-todo", method = RequestMethod.GET)
	public String showNewTodoPage(ModelMap model) {
		String username = (String) model.get("name");
		Todo todo = new Todo(0, username, "", LocalDate.now().plusYears(1), false);
		model.put("todo", todo);
		return "todo";
	}

	@RequestMapping(value = "add-todo", method = RequestMethod.POST)
	public String addNewTodo(@Valid @ModelAttribute("todo") Todo todo, BindingResult result, ModelMap model) {

		if (result.hasErrors()) {
			return "todo";
		}

		String username = (String) model.get("name");
		todoService.addTodo(username, todo.getDescription(), todo.getTargetDate(), false);
		return "redirect:list-todos";
	}

	@RequestMapping("delete-todo")
	public String deleteTodo(@RequestParam int id) {
		todoService.deleteById(id);
		return "redirect:list-todos";

	}

	@RequestMapping("update-todo")
	public String showUpdateTodoPage(@RequestParam int id, ModelMap model) {
		Todo todo = todoService.findById(id);
		model.addAttribute("todo", todo);
		return "updatetodo";
	}

	@RequestMapping(value = "update-todo", method = RequestMethod.POST)
	public String updateTodo(ModelMap model, @Valid Todo todo, BindingResult result) {
		if (result.hasErrors()) {
			return "updatetodo";
		}

		String username = (String) model.get("name");
		todo.setUsername(username);
		todo.setTargetDate(todo.getTargetDate());
		todoService.updateTodo(todo);
		return "redirect:list-todos";
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

	@Email(message = "Username must be valid email.")
	@NotEmpty(message = "Username can not be empty")
	private String username;

	@Size(min = 3, message = "Password must be minimum 8 characters")
	@Size(max = 15, message = "Password must not be more then 15 characters")
	private String password;

	@NotEmpty(message = "Firstname must not empty")
	@NotBlank(message = "Firstname must have at least one character")
	@Pattern(regexp = "^(?=.{1,40}$)[a-zA-Z]+(?:[-'\\s][a-zA-Z]+)*$", message = "Firstname must not contain any number")
	private String firstname;

	@NotEmpty(message = "Lastname must not empty")
	@NotBlank(message = "Lastname must have at least one character")
	@Pattern(regexp = "^(?=.{1,40}$)[a-zA-Z]+(?:[-'\\s][a-zA-Z]+)*$", message = "Firstname must not contain any number")
	private String lastname;

	@Min(message = "Age must be minimum 18 yrs", value = 18)
	@Max(message = "Age must not be more then 50 yrs", value = 50)
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

### /src/main/java/com/codewithheeren/springboot/entity/Todo.java
```java
package com.codewithheeren.springboot.entity;

import java.time.LocalDate;
import javax.validation.constraints.Size;

public class Todo {

	private int id;
	private String username;

	@Size(min = 10, message = "Enter atleast 10 characters")
	private String description;

	private LocalDate targetDate;
	private boolean done;

	public Todo() {
	}

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
### /src/main/java/com/codewithheeren/springboot/service/TodoService.java
```java
package com.codewithheeren.springboot.service;

import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;
import java.util.function.Predicate;
import javax.validation.Valid;
import org.springframework.stereotype.Service;
import com.codewithheeren.springboot.entity.Todo;

@Service
public class TodoService {

	private static List<Todo> todos = new ArrayList<>();

	private static int todosCount = 0;

	static {
		todos.add(new Todo(++todosCount, "heeren", "Learn AWS", LocalDate.now().plusYears(1), false));
		todos.add(new Todo(++todosCount, "heeren", "Learn DevOps", LocalDate.now().plusYears(2), false));
		todos.add(new Todo(++todosCount, "heeren", "Learn Full Stack Development", LocalDate.now().plusYears(3), false));
	}

	public List<Todo> findByUsername(String username) {
		return todos;
	}

	public void addTodo(String username, String description, LocalDate targetDate, boolean done) {
		Todo todo = new Todo(++todosCount, username, description, targetDate, done);
		todos.add(todo);
	}

	public void deleteById(int id) {
		Predicate<? super Todo> predicate = todo -> todo.getId() == id;
		todos.removeIf(predicate);
	}

	public Todo findById(int id) {
		Predicate<? super Todo> predicate = todo -> todo.getId() == id;
		Todo todo = todos.stream().filter(predicate).findFirst().get();
		return todo;
	}

	public void updateTodo(@Valid Todo todo) {
		deleteById(todo.getId());
		todos.add(todo);
	}
}
```
---
### /src/main/resources/templates/listtodos.html
```html
<html>
	<head>
		<link href="webjars/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" >
		<title>List Todos Page</title>		
	</head>
	<body>
		<div class="container">
			<h1>Your Todos</h1>
			<table class="table">
				<thead class="table-secondary">
					<tr>
						<th>id</th>
						<th>Description</th>
						<th>Target Date</th>
						<th>Is Done?</th>
						<th>Delete</th>
						<th>Update</th>
					</tr>
				</thead>
				<tbody>		
				<tr th:each="todo: ${todos}">
							<td th:text="${todo.id}" />
							<td th:text="${todo.description}" />
							<td th:text="${todo.targetDate}" />
							<td th:text="${todo.done}" />
							<td> <a th:href="@{'/delete-todo?id='+${todo.id}}" class="btn btn-warning">Delete</a>   </td>
							<td> <a th:href="@{'/update-todo?id='+${todo.id}}" class="btn btn-success">Update</a>   </td>
				</tr>
				</tbody>
			</table>
			<a href="add-todo" class="btn btn-success">Add Todo</a>
			<a href="/login" class="btn btn-danger">Logout</a>	
		</div>
		<script src="webjars/bootstrap/5.1.3/js/bootstrap.min.js"></script>
		<script src="webjars/jquery/3.6.0/jquery.min.js"></script>
	</body>
</html>
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
			Name: <input type="text" name="username">
			Password: <input type="password" name="password">
			<input type="submit">
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
				<a href="/login">
					<input type="button" style="float: right;" value="logout"/>
				</a>	
			<div th:text="'Your firstname: '+${user.firstname}"></div>
			<div th:text="'Your lastname: '+${user.lastname}"></div>
			<div th:text="'Your username: '+${user.username}"></div>
			<a href="list-todos">Manage</a> your todos
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
          				<label for="email">Username</label>
          				<input type="email" class="form-control" th:field="*{username}"  placeholder="Enter email">  
          				<div class="text-warning" th:each="e : ${#fields.errors('username')}" th:text="${e}"></div>    
        		</div>
        		
        		<div class="form-group">
          				<label for="password">Password</label>
          				<input type="password" class="form-control" th:field="*{password}" placeholder="Enter password">
          		<div class="text-warning" th:if="${#fields.hasErrors('password')}" th:errors="*{password}"></div>
        		</div>
              
         		<div class="form-group">
                        <label for="firstname">FirstName:</label>
                        <input type="text" class="form-control" placeholder="Firstname..." th:field="*{firstname}">
                        <div class="text-warning" th:if="${#fields.hasErrors('firstname')}" th:errors="*{firstname}"></div>
                 </div>
                 
         		 <div class="form-group">
                        <label for="firstname">LastName:</label>
                        <input type="text" class="form-control" placeholder="Lastname..." th:field="*{lastname}">
                        <div class="text-warning" th:if="${#fields.hasErrors('lastname')}" th:errors="*{lastname}"></div>
                 </div>
                 
                 <div class="form-group">
                        <label for="age">Age:</label>
                        <input type="text" class="form-control" placeholder="Age In Years..." th:field="*{age}">
                        <div class="text-warning" th:if="${#fields.hasErrors('age')}" th:errors="*{age}"></div>
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
### /src/main/resources/templates/todo.html
```html
<html xmlns:th="http://www.thymeleaf.org">
	<head>
		<link href="webjars/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" >
		<title>Add Todo Page</title>		
	</head>
	<body>
		<div class="container">
			<h1>Enter Todo Details</h1>
				
			<form method="post" th:action="@{/add-todo}" th:object="${todo}"> <!-- th:object is use for model attribute - todo -->
			<fieldset class="mb-3">	
			Description: <input type="text"  placeholder="Enter Task Description" required="required" th:field="*{description}"/>
			<div class="text-warning" th:if="${#fields.hasErrors('description')}" th:errors="*{description}"></div>
			</fieldset>
			
			<fieldset class="mb-3">		
			Target Date: <input type="text" required="required" th:field="*{targetDate}"/>
			<div class="text-warning" th:if="${#fields.hasErrors('targetDate')}" th:errors="*{targetDate}"></div>
			</fieldset>	
		
				
				<input type="hidden" th:field="*{id}"/>
				<input type="hidden" th:field="*{done}"/>
				<input type="submit" class="btn btn-success"/>
			</form>
			
		</div>
		<script src="webjars/bootstrap/5.1.3/js/bootstrap.min.js"></script>
		<script src="webjars/jquery/3.6.0/jquery.min.js"></script>		
		<script src="webjars/bootstrap-datepicker/1.9.0/js/bootstrap-datepicker.min.js"></script>		
		<script type="text/javascript">
		$('#targetDate').datepicker({
		    format: 'yyyy-mm-dd',		   
		});
		</script>	
	</body>
</html>
```
---
### /src/main/resources/templates/updatetodo.html
```html
<html xmlns:th="http://www.thymeleaf.org">
	<head>
		<link href="webjars/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" >
		<title>Update Todo Page</title>		
	</head>
	<body>
		<div class="container">
			<h1>Update Todo Details</h1>
			<form method="post" th:action="@{/update-todo}" th:object="${todo}"> <!-- th:object is use for model attribute - todo -->
			<fieldset class="mb-3">	
			Description: <input type="text"  placeholder="Enter Task Description" required="required" th:field="*{description}"/>
			<div class="text-warning" th:if="${#fields.hasErrors('description')}" th:errors="*{description}"></div>
			</fieldset>
			<fieldset class="mb-3">		
			Target Date: <input type="text" required="required" th:field="*{targetDate}"/>
			<div class="text-warning" th:if="${#fields.hasErrors('targetDate')}" th:errors="*{targetDate}"></div>
			</fieldset>	
			<input type="hidden" th:field="*{id}"/>
			<input type="hidden" th:field="*{done}"/>
			<input type="submit" class="btn btn-success"/>
			</form>
		</div>
		<script src="webjars/bootstrap/5.1.3/js/bootstrap.min.js"></script>
		<script src="webjars/jquery/3.6.0/jquery.min.js"></script>		
		<script src="webjars/bootstrap-datepicker/1.9.0/js/bootstrap-datepicker.min.js"></script>
		<script type="text/javascript">
		
		$('#targetDate').datepicker({
		    format: 'yyyy-mm-dd',		   
		});
		
		</script>	
	</body>
</html>
```
---
### /src/main/resources/application.properties

```properties
spring.mvc.view.suffix=.html
logging.level.org.springframework=info
logging.level.com.codewithheeren.springboot.myfirstwebapp=info
spring.mvc.format.date=yyyy-MM-dd
```
---