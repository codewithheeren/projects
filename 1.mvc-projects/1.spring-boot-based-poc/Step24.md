## Complete Code Example

### pom.xml

```xml
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
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
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
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-test</artifactId>
			<scope>test</scope>
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
			<groupId>org.webjars</groupId>
			<artifactId>bootstrap-datepicker</artifactId>
			<version>1.9.0</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
	</dependencies>
```
---
### /src/main/resources/application.properties

```properties
spring.mvc.view.suffix = .html
logging.level.org.springframework = info
logging.level.com.codewithheeren.springboot.myfirstwebapp = info
spring.mvc.format.date = yyyy-MM-dd
spring.jpa.hibernate.ddl-auto = create
spring.datasource.url = jdbc:mysql://localhost:3306/studentdb
spring.datasource.username = root
spring.datasource.password = root
spring.jpa.properties.hibernate.format_sql = true
```
---
### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step24Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
// Adding spring security with user module.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
@EnableJpaRepositories("com.codewithheeren.springboot.repository")
@EntityScan("com.codewithheeren.springboot.entity")
public class Step24Application {

	public static void main(String[] args) {
		SpringApplication.run(Step24Application.class, args);
	}
}
```
---

### /src/main/java/com/codewithheeren/springboot/controller/LoginController.java

```java
package com.codewithheeren.springboot.controller;

import java.time.LocalDate;
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

@Controller
@SessionAttributes("name")
public class LoginController {

	@RequestMapping(value="signup",method = RequestMethod.GET)
	public String gotoSignUpPage(ModelMap model) {
		model.put("user", new User());
		return "signup";
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

@Controller
@SessionAttributes("name")
public class TodoController {
	
	@Autowired
	TodoService todoService;
		
	
	@RequestMapping("/user/list-todos")
	public String listAllTodos(ModelMap model) {
		List<Todo> todos = todoService.findByUsername("codewithheeren");
		model.addAttribute("todos", todos);
		return "user/listTodos";
	}

	@RequestMapping(value="/user/add-todo", method = RequestMethod.GET)
	public String showNewTodoPage(ModelMap model) {
		String username = (String)model.get("name");
		Todo todo = new Todo(0, username, "", LocalDate.now().plusYears(1), false);
		model.put("todo", todo);
		return "user/todo";
	}

	@RequestMapping(value="/user/add-todo", method = RequestMethod.POST)
	public String addNewTodo(@Valid @ModelAttribute("todo") Todo todo, BindingResult result,ModelMap model) {
		
		if(result.hasErrors()) {
			return "user/todo";
		}
		
		String username = (String)model.get("name");
		todoService.addTodo(username, todo.getDescription(), 
				todo.getTargetDate(), false);
		return "redirect:/user/list-todos";
	}

	@RequestMapping("/user/delete-todo")
	public String deleteTodo(@RequestParam int id) {
		todoService.deleteById(id);
		return "redirect:/user/list-todos";
		
	}

	@RequestMapping("/user/update-todo")
	public String updateTodo(@RequestParam int id, ModelMap model) {
		System.out.println(id);
		Todo todo = todoService.findById(id);
		model.addAttribute("todo", todo);
		return "user/updatetodo";
	}
	
	@RequestMapping(value="/user/update-todo", method = RequestMethod.POST)
	public String updateTodo(ModelMap model, @Valid Todo todo, BindingResult result) {
		if(result.hasErrors()) {
			return "user/updatetodo";
		}
		
		String username = (String)model.get("name");
		todo.setUsername(username);
		todo.setTargetDate(todo.getTargetDate());
		todoService.updateTodo(todo);
		return "redirect:/user/list-todos";
	}
}
```
---
### /src/main/java/com/codewithheeren/springboot/controller/MainController.java

```java
package com.codewithheeren.springboot.controller;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.SessionAttributes;
import com.codewithheeren.springboot.entity.User;
import com.codewithheeren.springboot.repository.UserRepository;

@Controller
@SessionAttributes("name")
public class MainController {
	
	@Autowired
	UserRepository repository ;

	@GetMapping("")
	public String viewHomePage() {
		return "index";
	}
	
	@GetMapping("/admin/login")
	public String viewAdminLoginPage() {
		return "admin/admin_login";
	}
	
	@GetMapping("/admin/home")
	public String viewAdminHomePage() {
		return "admin/admin_home";
	}
	
	@GetMapping("/user/login")
	public String viewUserLoginPage() {
		return "user/user_login";
	}
	
	@GetMapping("/user/home")
	public String viewUserHomePage(ModelMap model) {
		UserDetails userDetails = (UserDetails)SecurityContextHolder.getContext().getAuthentication().getPrincipal();
		User user = repository.findByUsername(userDetails.getUsername());
		model.put("name", user.getUsername()); 
		model.addAttribute("user", user);
		return "user/profile";
	}	
}

```
---

### /src/main/java/com/codewithheeren/springboot/entity/User.java
```java
package com.codewithheeren.springboot.entity;

import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "users")
public class User {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;

	private String username;

	private String password;

	@Enumerated(EnumType.STRING) // Specifies that a persistent property should be persisted as a enumerated type to String
	private Role role;

	private String firstname;

	private String lastname;

	private int age;

	public User() {
	}

	public User(String username, String password, Role role) {
		this.username = username;
		this.password = password;
		this.role = role;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

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

	public Role getRole() {
		return role;
	}

	public void setRole(Role role) {
		this.role = role;
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
}
```
---

### /src/main/java/com/codewithheeren/springboot/entity/Todo.java
```java
package com.codewithheeren.springboot.entity;

import java.time.LocalDate;
import javax.validation.constraints.Size;
import org.springframework.format.annotation.DateTimeFormat;

public class Todo {

	private int id;
	private String username;

	@Size(min = 10, message = "Enter atleast 10 characters")
	private String description;

	@DateTimeFormat(pattern = "yyyy-MM-dd")
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
### /src/main/java/com/codewithheeren/springboot/entity/Role.java
```java
package com.codewithheeren.springboot.entity;

public enum Role {
	ADMIN, USER
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
### AdminSecurityConfig.java
```java
package com.codewithheeren.springboot.myfirstwebapp;

import javax.sql.DataSource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.annotation.Order;
import org.springframework.security.authentication.dao.DaoAuthenticationProvider;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@Configuration
@Order(1)
public class AdminSecurityConfig extends WebSecurityConfigurerAdapter {

	@Autowired
	private DataSource dataSource;

	@Bean
	public UserDetailsService userDetailsService() {
		return new CustomUserDetailsService();
	}

	@Bean
	public BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}

	@Bean
	public DaoAuthenticationProvider authenticationProvider() {
		DaoAuthenticationProvider authProvider = new DaoAuthenticationProvider();
		authProvider.setUserDetailsService(userDetailsService());
		authProvider.setPasswordEncoder(passwordEncoder());

		return authProvider;
	}

	@Override
	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth.authenticationProvider(authenticationProvider());
	}

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http.authorizeRequests().antMatchers("/").permitAll();

		http.antMatcher("/admin/**").authorizeRequests().anyRequest().hasAuthority("ADMIN").and().formLogin()
				.loginPage("/admin/login").usernameParameter("email").loginProcessingUrl("/admin/login")
				.defaultSuccessUrl("/admin/home").permitAll().and().logout().logoutUrl("/admin/logout")
				.logoutSuccessUrl("/");
	}
}

```
---
### UserSecurityConfig.java
```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.context.annotation.Configuration;
import org.springframework.core.annotation.Order;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@Order(2)
public class UserSecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		
		http.antMatcher("/user/**")
			.authorizeRequests().anyRequest().hasAuthority("USER")
			.and()
			.formLogin()
				.loginPage("/user/login")
				.usernameParameter("email")
				.loginProcessingUrl("/user/login")
				.defaultSuccessUrl("/user/home")
				.permitAll()
			.and()
			.logout()
				.logoutUrl("/user/logout")
				.logoutSuccessUrl("/");		
	}	
}
```
---
### CustomUserDetails.java
```java
package com.codewithheeren.springboot.myfirstwebapp;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import com.codewithheeren.springboot.entity.User;

public class CustomUserDetails implements UserDetails {
	private User user;

	public CustomUserDetails(User user) {
		this.user = user;
	}

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		List<SimpleGrantedAuthority> authorities = new ArrayList<>();

		authorities.add(new SimpleGrantedAuthority(user.getRole().toString()));

		return authorities;
	}

	@Override
	public String getPassword() {
		return user.getPassword();
	}

	@Override
	public String getUsername() {
		return user.getUsername();
	}

	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	@Override
	public boolean isAccountNonLocked() {
		return true;
	}

	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}

	@Override
	public boolean isEnabled() {
		return true;
	}
}
```
---
### CustomUserDetailsService.java
```java
package com.codewithheeren.springboot.myfirstwebapp;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import com.codewithheeren.springboot.entity.User;
import com.codewithheeren.springboot.repository.UserRepository;

public class CustomUserDetailsService implements UserDetailsService {
	@Autowired 
	private UserRepository repo;

	@Override
	public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
		User user = repo.findByUsername(email);
		if (user == null) {
			throw new UsernameNotFoundException("No user found with the given email");
		}
		return new CustomUserDetails(user);
	}
}
```
---
### /src/main/resources/templates/admin/admin_home.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="ISO-8859-1">
<title>Welcome to CodeWithHeeren Admin Control Panel</title>
</head>
<body>
	<div align="center">
		<h2>Welcome to CodeWithHeeren Admin Control Panel</h2>
		<p>Your user name is: <b>[[${#request.userPrincipal.principal.username}]]</b></p>
        <form th:action="@{/admin/logout}" method="post">
            <input type="submit" value="Logout" />
        </form>		
	</div>
</body>
</html>

```
---
### /src/main/resources/templates/admin/admin_login.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="ISO-8859-1">
<title>Admin Login - CodeWithHeeren</title>
</head>
<body>
<form th:action="@{/admin/login}" method="post" style="max-width: 400px; margin: 0 auto;">
	<h2>Admin Login - CodeWithHeeren</h2>
	
	<div th:if="${param.error}">
		<h4 style="color: red">[[${session.SPRING_SECURITY_LAST_EXCEPTION.message}]]</h4>
	</div>
			
	<table>
    	<tr>
    		<td>E-mail: </td>
    		<td><input type="email" name="email" required /></td>
        </tr>
    	<tr>
    		<td>Password: </td>
    		<td><input type="password" name="password" required /></td>
        </tr> 
        <tr><td>&nbsp;</td></tr>
    	<tr>
        	<td colspan="2" align="center"><input type="submit" value="Login" /></td>
    	</tr>
    </table>
</form>
</body>
</html>

```
---
### /src/main/resources/templates/error/403.html
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Forbidden Error</title>
</head>
<body>
	<div align="center">
		<h2>You don't have permission to access the requested page</h2>
	</div>
</body>
</html>
```
---
### /src/main/resources/templates/user/fragments/header.html
```html
<html xmlns:th="http://www.thymeleaf.org">
	<head>
		<link rel="stylesheet" type="text/css" href="/webjars/bootstrap/css/bootstrap.min.css" />
		<script type="text/javascript" src="/webjars/jquery/jquery.min.js"></script>
		<script type="text/javascript" src="/webjars/bootstrap/js/bootstrap.min.js"></script>
		<link href="webjars/bootstrap-datepicker/1.9.0/css/bootstrap-datepicker.standalone.min.css" rel="stylesheet" >
		
		<title>Manage Your Todos</title>		
	</head>
	<body>

```
---
### /src/main/resources/templates/user/fragments/footer.html
```html
		<script type="text/javascript" src="/webjars/jquery/jquery.min.js"></script>
		<script type="text/javascript" src="/webjars/bootstrap/js/bootstrap.min.js"></script>
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
### /src/main/resources/templates/user/fragments/navigation.html
```html
<nav class="navbar navbar-expand-md navbar-light bg-light mb-3 p-1">
	<a class="navbar-brand m-1" href="https:github.com/codewithheeren">CodeWithHeeren</a>
	<div class="collapse navbar-collapse">
		<ul class="navbar-nav">
			<li class="nav-item"><a class="nav-link" href="/">Home</a></li>
			<li class="nav-item"><a class="nav-link" href="/user/list-todos">Todos</a></li>
		</ul>
	</div>
	<ul class="navbar-nav">
		<li class="nav-item">
			<form th:action="@{/user/logout}" method="post">
          	  <input type="submit" value="Logout" />
        	</form>
       </li>
	</ul>	
</nav>
```
---

### /src/main/resources/templates/user/listtodos.html
```html
<div th:replace="user/fragments/header.html"></div>
<div th:replace="user/fragments/navigation.html"></div>


		<div class="container">
			<h1>Your Todos</h1>
			<table class="table">
				<thead>
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
							<td> <a th:href="@{'../user/delete-todo?id='+${todo.id}}" class="btn btn-warning">Delete</a>   </td>
							<td> <a th:href="@{'update-todo?id='+${todo.id}}" class="btn btn-success">Update</a>   </td>
				</tr>
				</tbody>
			</table>
			<a href="add-todo" class="btn btn-success">Add Todo</a>
		
		</div>
<div th:replace="user/fragments/footer.html"></div>
```
---
### /src/main/resources/templates/user/login.html
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
		<h2>Login</h2>
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
### /src/main/resources/templates/user/profile.html
```html
<div th:replace="user/fragments/header.html"></div>
<div th:replace="user/fragments/navigation.html"></div>

	<div class="container">
			<span th:text="'Welcome '+${user.firstname}+' '+${user.lastname}"></span>	
			<div th:text="'Your firstname: '+${user.firstname}"></div>
			<div th:text="'Your lastname: '+${user.lastname}"></div>
			<div th:text="'Your username: '+${user.username}"></div>
			<a href="list-todos">Manage</a> your todos
	</div>
<div th:replace="user/fragments/footer.html"></div>	
```
---
### /src/main/resources/templates/user/todo.html
```html
<div th:replace="user/fragments/header.html"></div>
<div th:replace="user/fragments/navigation.html"></div>
		<div class="container">
			<h1>Enter Todo Details</h1>
				
			<form method="post" th:action="@{../user/add-todo}" th:object="${todo}"> <!-- th:object is use for model attribute - todo -->
			<fieldset class="mb-3">	
			Description: <input type="text"  placeholder="Enter Task Description" required="required" th:field="*{description}"/>
				<div class="text-warning" th:if="${#fields.hasErrors('description')}" th:errors="*{description}"></div>
			</fieldset>
			
			<fieldset class="mb-3">		
			Target Date: <input type="text" required="required" class="date-picker" th:field="*{targetDate}"/>
				<div class="text-warning" th:if="${#fields.hasErrors('targetDate')}" th:errors="*{targetDate}"></div>
			</fieldset>	
		
				
			<input type="hidden" th:field="*{id}"/>
			<input type="hidden" th:field="*{done}"/>
			<input type="submit" class="btn btn-success"/>
			</form>
			
		</div>
<div th:replace="user/fragments/footer.html"></div>	
```
---
### /src/main/resources/templates/user/updatetodo.html
```html
<div th:replace="user/fragments/header.html"></div>
<div th:replace="user/fragments/navigation.html"></div>
		<div class="container">
			<h1>Update Todo Details</h1>
				
			<form method="post" th:action="@{../user/update-todo}" th:object="${todo}"> <!-- th:object is use for model attribute - todo -->
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
<div th:replace="user/fragments/footer.html"></div>	
```
---
### /src/main/resources/templates/user/user_home.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="ISO-8859-1">
<title>Welcome to CodeWithHeeren</title>
</head>
<body>
	<div align="center">
		<h2>Welcome to CodeWithHeeren User Home</h2>
		<p>Your user name is: <b>[[${#request.userPrincipal.principal.username}]]</b></p>
		<h3 th:text="${user.email}">User ID</h3>
        <form th:action="@{/user/logout}" method="post">
            <input type="submit" value="Logout" />
        </form>		
	</div>
</body>
</html>
```
---
### /src/main/resources/templates/user/user_login.html
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="ISO-8859-1">
<title>User Login - CodeWithHeeren</title>
</head>
<body>
<form th:action="@{/user/login}" method="post" style="max-width: 400px; margin: 0 auto;">
	<h2>User Login - CodeWithHeeren</h2>
	
	<div th:if="${param.error}">
		<h4 style="color: red">[[${session.SPRING_SECURITY_LAST_EXCEPTION.message}]]</h4>
	</div>
		
	<table>
    	<tr>
    		<td>E-mail: </td>
    		<td><input type="email" name="email" required /></td>
        </tr>
    	<tr>
    		<td>Password: </td>
    		<td><input type="password" name="password" required /></td>
        </tr> 
        <tr><td>&nbsp;</td></tr>
    	<tr>
        	<td colspan="2" align="center"><input type="submit" value="Login" /></td>
    	</tr>
    </table>
</form>
</body>
</html>
```
---
### /src/main/resources/templates/index.html
```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="ISO-8859-1">
	<title>Welcome to CodeWithHeeren</title>
	<link rel="stylesheet" type="text/css" href="/webjars/bootstrap/css/bootstrap.min.css" />
	<script type="text/javascript" src="/webjars/jquery/jquery.min.js"></script>
	<script type="text/javascript" src="/webjars/bootstrap/js/bootstrap.min.js"></script>
</head>
<body>
	<div class="container">
		<h2>Welcome to CodeWithHeeren</h2>
		<h4><a th:href="@{/admin/login}">Admin Login</a></h4>
		<p/>
		<h4><a th:href="@{/user/login}">User Login</a></h4>
	</div>
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