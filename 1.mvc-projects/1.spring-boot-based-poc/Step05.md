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
	<artifactId>step05</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>step05</name>
	<description>Demo project for Spring Boot</description>
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

### /src/main/java/com/codewithheeren/springboot/myfirstwebapp/Step05Application.java

```java
package com.codewithheeren.springboot.myfirstwebapp;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//adding thymeleaf dependency
//rander view page

@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class Step05Application {

	public static void main(String[] args) {
		SpringApplication.run(Step05Application.class, args);
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

### /src/main/resources/application.properties

```properties
#spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.html
logging.level.org.springframework=debug

```
---

### /src/test/java/com/codewithheeren/springboot/myfirstwebapp/Step05ApplicationTests.java

```java
package com.in28minutes.springboot.myfirstwebapp;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Step05ApplicationTests {

	@Test
	void contextLoads() {
	}

}
```
---
