## Development Process
1. Create Spring Security Initializer
2. Create Spring Security Configuration (@Configuration)
3. Add users, passwords and roles

## Spring Security Web App Initializer
- Spring Security provides support for security initialization
- Your security code is used to initialize the servlet container
- Special class to register the Spring Security Filters
`AbstractSecurityWebApplicationInitializer`
- Its a special class to register **Spring Secuirity Filter**.
- Your TO DO list
    - Extend this abstract base class
    - that’s it!

## Impl
**pom.xml**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.luv2code</groupId>
	<artifactId>spring-security-demo</artifactId>
	<version>1.0</version>
	<packaging>war</packaging>

	<name>spring-security-demo</name>

	<properties>
		<springframework.version>5.3.13</springframework.version>
		<springsecurity.version>5.6.0</springsecurity.version>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>

	<dependencies>

		<!-- Spring MVC support -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${springframework.version}</version>
		</dependency>

		<!-- Servlet, JSP and JSTL support -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.1.0</version>
		</dependency>
		
		<!-- Spring Security-->
		<!-- spring-security-web spring-security-config-->
		<!-- Make sure to find the compatible versions-->
		<dependency>
		    <groupId>org.springframework.security</groupId>
		    <artifactId>spring-security-web</artifactId>
		    <version>${springsecurity.version}</version>
		</dependency>
		
		<dependency>
		    <groupId>org.springframework.security</groupId>
		    <artifactId>spring-security-config</artifactId>
		    <version>${springsecurity.version}</version>
		</dependency>

		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>javax.servlet.jsp-api</artifactId>
			<version>2.3.1</version>
		</dependency>

		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>

		<!-- to compensate for java 9+ not including jaxb -->
		<dependency>
		    <groupId>javax.xml.bind</groupId>
		    <artifactId>jaxb-api</artifactId>
		    <version>2.3.0</version>
		</dependency>

		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>3.8.1</version>
			<scope>test</scope>
		</dependency>

	</dependencies>

	<!-- TO DO: Add support for Maven WAR Plugin -->
	<build>
		<pluginManagement>
		    <plugins>
		        <plugin>
		            <!-- Add Maven coordinates (GAV) for: maven-war-plugin -->
		            <groupId>org.apache.maven.plugins</groupId>
		            <artifactId>maven-war-plugin</artifactId>
		            <version>3.2.0</version>
		        </plugin>
		    </plugins>
		</pluginManagement>
	</build>
	
	
</project>

```
**SecurityWebApplicationInitializer.java**
```Java
import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;
public class SecurityWebApplicationInitializer extends AbstractSecurityWebApplicationInitializer {
}
```
**DemoSecurityConfig.java**
```Java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
@Configuration
@EnableWebSecurity
public class DemoSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // add our users for in memory authentication
        UserBuilder users = User.withDefaultPasswordEncoder();

        auth.inMemoryAuthentication()
            .withUser(users.username("john").password("test123").roles("EMPLOYEE"))
            .withUser(users.username("mary").password("test123").roles("MANAGER"))
            .withUser(users.username("susan").password("test123").roles("ADMIN"));
    }
}
```

## Issue - 1 
- During dev and testing you may have noticed an issue... 
- `the user stays logged in`
- Once you change the JSP page and refresh, you will see that the user is not Logged Out.
- After logging in, if we open new tab. Any type the same URL , it will open the user logged in. 
- Reason
	- User login is based on a "Web browser session". 
- Solution 
	- Start a new web browser. ( This gives us a  NEW Session) 
	- Quit browser and start again 
	- New private window


