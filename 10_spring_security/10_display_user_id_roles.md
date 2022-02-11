## Spring Security
- Spring Security provides JSP custom tags for accessing user id and roles

## Development Process
- Update POM file for Spring Security JSP Tag Library
- Add Spring Security JSP Tag Library to JSP page
- Display User ID
- Display User Roles

## Step 1: Update POM file for Spring Security JSP Tag Library
**pom.xml**
```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-taglibs</artifactId>
    <version>${springsecurity.version}</version>
</dependency>
```

## Step 2: Add Spring Security JSP Tag Library to JSP page
```jsp
<%@ taglib prefix="security" uri="http://www.springframework.org/security/tags" %>
```

## Step 3: Display User ID
**home.jsp**
```jsp
<%@ taglib prefix="security" uri="http://www.springframework.org/security/tags" %>
…
User: <security:authentication property="principal.username" />
```
## Step 4: Display User Roles
```jsp
<%@ taglib prefix="security" uri="http://www.springframework.org/security/tags" %>
…
Role(s): <security:authentication property="principal.authorities" />
```

## Adding multiple roles
**DemoSecurityConfig.java**
```Java
...
auth.inMemoryAuthentication()
            .withUser(users.username("john").password("test123").roles("EMPLOYEE"))
            .withUser(users.username("mary").password("test123").roles("EMPLOYEE","MANAGER"))
            .withUser(users.username("susan").password("test123").roles("EMPLOYEE","ADMIN"));
...
```