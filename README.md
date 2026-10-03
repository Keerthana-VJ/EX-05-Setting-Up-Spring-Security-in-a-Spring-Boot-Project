# Exp 05 - Setting Up Spring Security in a Spring Boot Project
## Name: Keerthana V
## Register Number: 212223220045
## AIM

To develop a Spring Boot application using **Spring Security** to secure endpoints with **Basic Authentication** and **Role-Based Access Control (RBAC)**.

---

## ALGORITHM

### Step 1: Create a Spring Boot Project

Create a new Spring Boot project using **Spring Initializr**.

Add the following dependencies:

* Spring Web
* Spring Security
* Spring Boot DevTools *(Optional)*

### Step 2: Add Spring Security Dependency

If Spring Security was not added while creating the project, add the following dependency to `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### Step 3: Create the Security Configuration

Create a configuration class using `@Configuration` and `@EnableWebSecurity`.

Use `SecurityFilterChain` to configure Spring Security for newer versions of Spring Boot and Spring Security.

### Step 4: Configure Authentication

Create in-memory users using `InMemoryUserDetailsManager`.

Define users with:

* Username
* Password
* Role

For example:

* `user` → `USER`
* `admin` → `ADMIN`

### Step 5: Secure the Endpoints

Configure the application so that:

* `/public` is accessible without authentication.
* `/user` requires the `USER` role.
* `/admin` requires the `ADMIN` role.
* Other endpoints require authentication.

### Step 6: Enable Basic Authentication

Enable HTTP Basic Authentication using Spring Security.

Protected endpoints will require a valid username and password.

### Step 7: Run the Application

Run the Spring Boot application using the IDE or Maven:

```bash
mvn spring-boot:run
```

### Step 8: Test the Endpoints

Use a browser or Postman to test the endpoints.

Verify that:

* Public endpoints can be accessed without authentication.
* Users with the `USER` role can access user endpoints.
* Only users with the `ADMIN` role can access admin endpoints.
* Unauthorized users are denied access.

---

# PROGRAM CODE

## Project Structure

```text
spring-security-demo/
│
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── example/
│                   └── demo/
│                       ├── DemoApplication.java
│                       ├── SecurityConfig.java
│                       └── HelloController.java
│
└── pom.xml
```

---

## 1. pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.2</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>spring-security-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <name>Spring Security Demo</name>
    <description>Spring Boot Security with Basic Authentication</description>

    <dependencies>

        <!-- Spring Boot Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Security -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

    </dependencies>

    <build>
        <plugins>

            <!-- Spring Boot Maven Plugin -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>

        </plugins>
    </build>

</project>
```

---

## 2. SecurityConfig.java

**Spring Boot 3.x / Spring Security 6+**

```java
package com.example.demo;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http)
            throws Exception {

        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public").permitAll()
                .requestMatchers("/user").hasRole("USER")
                .requestMatchers("/admin").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .httpBasic(httpBasic -> {});

        return http.build();
    }

    @Bean
    public InMemoryUserDetailsManager userDetailsService() {

        UserDetails user = User.withDefaultPasswordEncoder()
            .username("user")
            .password("password")
            .roles("USER")
            .build();

        UserDetails admin = User.withDefaultPasswordEncoder()
            .username("admin")
            .password("admin")
            .roles("ADMIN")
            .build();

        return new InMemoryUserDetailsManager(user, admin);
    }
}
```

> **Note:** `User.withDefaultPasswordEncoder()` is suitable for a simple academic demonstration, but it is **not recommended for production applications**. Production applications should use a proper `PasswordEncoder` such as BCrypt.

---

## 3. HelloController.java

**REST Controller**

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/public")
    public String publicEndpoint() {
        return "This is a public endpoint.";
    }

    @GetMapping("/user")
    public String userEndpoint() {
        return "This is a user endpoint. You have USER access!";
    }

    @GetMapping("/admin")
    public String adminEndpoint() {
        return "This is an admin endpoint. You have ADMIN access!";
    }
}
```

---

## 4. DemoApplication.java

**Main Application Class**

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

---

# SECURITY CONFIGURATION

The application contains two in-memory users:

| Username | Password   | Role    |
| -------- | ---------- | ------- |
| `user`   | `password` | `USER`  |
| `admin`  | `admin`    | `ADMIN` |

### Endpoint Access

| Endpoint  | Authentication | Required Role |
| --------- | -------------- | ------------- |
| `/public` | Not required   | None          |
| `/user`   | Required       | `USER`        |
| `/admin`  | Required       | `ADMIN`       |

---

# API ENDPOINTS

## 1. Public Endpoint

```http
GET /public
```

Accessible without authentication.

---

## 2. User Endpoint

```http
GET /user
```

Accessible only to users with the `USER` role.

---

## 3. Admin Endpoint

```http
GET /admin
```

Accessible only to users with the `ADMIN` role.

---

# OUTPUT

## 1. Public Endpoint

The public endpoint can be accessed without providing authentication credentials.

<img width="954" height="431" alt="image" src="https://github.com/user-attachments/assets/de6edbd0-1c37-4ed7-a579-c3f038320160" />


---

## 2. User Endpoint

### With User Access

The `user` account has the `USER` role and can access the user endpoint.

<img width="959" height="448" alt="image" src="https://github.com/user-attachments/assets/1ba23304-80ea-4466-8398-25abbfe794cc" />


### With Admin Access

The `admin` account can also access the user endpoint because it is an authenticated user with elevated privileges.

![Admin Access to User Endpoint](https://github.com/user-attachments/assets/1457f2c1-3a8f-4d2d-8d8d-51e8acd2a12a)

---

## 3. Admin Endpoint

### With User Access

The `user` account does not have the `ADMIN` role, so access to the admin endpoint is denied.

<img width="956" height="412" alt="image" src="https://github.com/user-attachments/assets/0bcf7388-4c2f-4eb7-952a-e845a0dbfe27" />


### With Admin Access

The `admin` account has the `ADMIN` role and can successfully access the admin endpoint.

![Admin Access](https://github.com/user-attachments/assets/e5176d38-a6f8-4421-b41a-3a863b714726)

---

# SECURITY FLOW

```text
                         Client / Browser
                                │
                                ▼
                       Spring Security
                                │
                     ┌──────────┴──────────┐
                     │                     │
              Authentication        Authorization
                     │                     │
                     ▼                     ▼
              Username/Password       Check Role
                     │                     │
                     └──────────┬──────────┘
                                │
               ┌────────────────┼────────────────┐
               │                │                │
               ▼                ▼                ▼
            /public           /user           /admin
               │                │                │
             Public          USER Role        ADMIN Role
               │                │                │
               └────────────────┼────────────────┘
                                ▼
                           REST Response
```

---

# RESULT

Thus, a **Spring Boot application with Spring Security** was successfully developed. The application secured REST endpoints using **Basic Authentication** and implemented **Role-Based Access Control (RBAC)** for `USER` and `ADMIN` roles.
