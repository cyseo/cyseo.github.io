---
layout: post
comments: true
title: "[Spring] STS로 Spring 프로젝트 만들기"
author: Changyoun Seo
date: 2018-01-17 12:00:00 +0900
tags: [Spring, STS, 공부]
---

# Spring

## 프로젝트 만들기
1. File - new - Spring starter project
    - artifact는 `-`를 붙이고
    - package는 무조건 소문자!
1. Available: `Mybatis`, `Mysql`, `Web`

---

## DB설정
- `application.properties` 수정

```
spring.datasource.url=jdbc:mysql://[IP]:[PORT]/[DBNAME]
spring.datasource.username=[USERNAME]
spring.datasource.password=[PASSWORD]
```

- test : src/test/java 내에 `~~ApplicationTests.java` 실행해보면 된다.

---

## 첫 페이지
1. `src/main/java` 아래 패키지에서 New - class
    - 여기서 이름은 `HelloWorldController`로 한다. 
2. 내용

    ```java
    @Controller
    public class HelloWorldController {
        
        @GetMapping("/helloworld")
        @ResponseBody
        public String helloWorld() {
            return "Hello World";
        }
    }
    ```

3. `~~Application.java` 실행. `Spring Boot App`을 선택
4. `http://localhost:8080/helloworld` 가봅시다.

---

## JSP 설정
- 여기부터는 JSP 설정.. 안해도 될듯

1. pom.xml 수정

    ```
    <dependencies>
        ~

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

2. `application.properties` 설정

    ```
    ~
    spring.mvc.view.prefix=/WEB-INF/jsp/
    spring.mvc.view.suffix=.jsp

    application.message=Hello Rookie
    ```

3. Ctrl 수정

    ```java
    @Controller
    public class HelloWorldController {
            
        @Value("${application.message:Hello World}")
        private String message = "Hello World";
        
        @GetMapping("/helloworld")
        @ResponseBody
        public String helloWorld() {
            return "Hello World";
        }
        
        @GetMapping("/")
        public String welcome(Model model) {
            model.addAttribute("time", new Date());
            model.addAttribute("message", this.message);
            return "welcome";
        }
    }
    ```

4. `src/main/webapp/WEB-INF/jsp` 폴더를 만들고 `welcome.jsp` 생성

    ```html
    <!DOCTYPE html>
    <%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    <html lang="en">
    <body>
        Now : ${time}
        <br>
        Message: ${message}
    </body>
    </html>
    ```

5. `http://localhost:8080/` 가봅시다.

---

## DB 쿼리 날려보기
- 원본 참고

---

## 기타
- `Ctrl + Shift + R` 파일 검색 굳
- 라이브러리와 프레임워크의 차이점 : 누가 코드를 사용하는지. 라이브러리 + IoC 는 프레임워크가 될 수 있으

---

## 마치며
- Angular와 함께 쓰기 위한 설정을 구해보자
- [Spring4 MVC Angular](http://websystique.com/springmvc/spring-mvc-4-angularjs-example/)
