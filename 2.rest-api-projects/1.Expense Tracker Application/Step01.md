## Complete Code Example


### /pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.5.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.codewithheeren.rest</groupId>
	<artifactId>step01</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>step01</name>
	<description>Expense tracker rest api</description>
	<properties>
		<java.version>1.8</java.version>
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
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
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

</project>

```
---

### /src/main/java/com/codewithheeren/rest/startapp/Step01Application.java

```java
package com.codewithheeren.rest.startapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
//step 01 | page 30
//implement get all expenses rest end point 
//mysql data bases integrated
//dummy data inserted using runner
@SpringBootApplication
@ComponentScan("com.codewithheeren.rest")
@EnableJpaRepositories("com.codewithheeren.rest.repository") 
@EntityScan("com.codewithheeren.rest.entity")
public class Step01Application {

	public static void main(String[] args) {
		SpringApplication.run(Step01Application.class, args);
	}
}


```
---

### /src/main/resources/application.properties

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/expensetracker
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=create
server.servlet.context-path=/api/v1
spring.jpa.show-sql=true
```
---

### /src/main/java/com/codewithheeren/rest/runner/ExpenseRunner.java

```java
package com.codewithheeren.rest.runner;

import java.math.BigDecimal;
import java.sql.Date;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
import com.codewithheeren.rest.entity.Expense;
import com.codewithheeren.rest.service.ExpenseService;

@Component
public class ExpenseRunner implements CommandLineRunner {

	@Autowired
	ExpenseService service;
	
	@Override
	public void run(String... args) throws Exception {
		service.save(new Expense("water bill","water bill month janruary", BigDecimal.valueOf(100.00),"Bills",new java.sql.Date(System.currentTimeMillis())));
		service.save(new Expense("electricity bill","electricity bill month february", BigDecimal.valueOf(254.00),"Bills",new java.sql.Date(System.currentTimeMillis())));
	}
}

```
---


### /src/main/java/com/codewithheeren/rest/entity/Expense.java

```java
package com.codewithheeren.rest.entity;

import java.math.BigDecimal;
import java.sql.Date;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@Entity
@Table(name = "tbl_expenses")
public class Expense {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	
	@Column(name = "expense_name")
	private String name;
	
	
	private String description;
	
	@Column(name = "expense_amount")
	private BigDecimal amount;
	
	private String category;
	
	private Date date;

	public Expense(String name, String description, BigDecimal amount, String category, Date date) {
		super();
		this.name = name;
		this.description = description;
		this.amount = amount;
		this.category = category;
		this.date = date;
	}
}

```
---

### /src/main/java/com/codewithheeren/rest/repository/ExpenseRepository.java

```java
package com.codewithheeren.rest.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import com.codewithheeren.rest.entity.Expense;

@Repository
public interface ExpenseRepository extends JpaRepository<Expense, Long> {	
}

```
---

### /src/main/java/com/codewithheeren/rest/service/ExpenseService.java

```java
package com.codewithheeren.rest.service;

import java.util.List;
import com.codewithheeren.rest.entity.Expense;

public interface ExpenseService {
	
	List<Expense> getAllExpenses();
	
	void save(Expense expense);
}

```
---

### /src/main/java/com/codewithheeren/rest/service/ExpenseServiceImpl.java

```java
package com.codewithheeren.rest.service;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.codewithheeren.rest.entity.Expense;
import com.codewithheeren.rest.repository.ExpenseRepository;

@Service
public class ExpenseServiceImpl implements ExpenseService {

	@Autowired
	private ExpenseRepository expenseRepo;
	
	@Override
	public List<Expense> getAllExpenses(){
		return expenseRepo.findAll();
	}

	@Override
	public void save(Expense expense) {
		expenseRepo.save(expense);
	}
}

```
---

### /src/main/java/com/codewithheeren/rest/controller/ExpenseController.java

```java
package com.codewithheeren.rest.controller;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import com.codewithheeren.rest.entity.Expense;
import com.codewithheeren.rest.service.ExpenseService;

@RestController
public class ExpenseController {

	@Autowired
	private ExpenseService expenseService;
	
	@GetMapping("/expenses")
	public List<Expense> getAllExpenses() {
		return expenseService.getAllExpenses();
	}
}

```
---

