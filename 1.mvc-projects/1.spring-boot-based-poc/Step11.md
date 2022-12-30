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

//implementing login page and welcome page.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step11Application {

	public static void main(String[] args) {
		SpringApplication.run(Step11Application.class, args);
	}
}



```
---

### /src/main/java/com/codewithheeren/springboot/controller/SayHelloController.java

```java
package com.codewithheeren.springboot.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class SayHelloController {
	
	@RequestMapping("firsthtml")
	@ResponseBody
	public String sayHelloHtml() {
		StringBuffer sb = new StringBuffer();
		sb.append("<html>");
		sb.append("<head>");
		sb.append("<title> My First HTML Page - Changed</title>");
		sb.append("</head>");
		sb.append("<body>");
		sb.append("My first html page with body - Changed");
		sb.append("</body>");
		sb.append("</html>");
		
		return sb.toString();
	}
	
	@RequestMapping("thymeleafpage")
	public String sayHelloJsp() {
		return "sayHello";
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

	@RequestMapping(value="login",method = RequestMethod.GET)
	public String gotoLoginPage() {
		return "login";
	}

	@RequestMapping(value="login",method = RequestMethod.POST)
	//login?name=codewithheeren&password=123 
	public String gotoWelcomePage(@RequestParam String name, @RequestParam String password, ModelMap model) {
		
		if(authenticationService.authenticate(name, password)) {
			model.put("name", name);
			//Authentication 
			//name - heeren
			//password - 123
			
			return "welcome";
		}
		return "login";
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

### /src/main/resources/templates/sayhello.html

```
<html>
	<head>
		<title> My first HTML Page</title>
	</head>
	<body>
		My first html page with body and thymeleaf mappings.
	</body>
</html>
```
---

### /src/main/resources/templates/login.html

```
<html>
	<head>
		<title>Login Page</title>
	</head>
	<body>
		Welcome to the login page!
		<form method="post">
			Name: <input type="text" name="name">
			Password: <input type="password" name="password">
			<input type="submit">
		</form>
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
		<div>Welcome to Codewithheeren</div>
		<div th:text="'Your Name '+${name}"></div>
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
