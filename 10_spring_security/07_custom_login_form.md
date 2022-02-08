## Implementing Custom Login Form
![](./images/custom_login_form.jpg)

## Developement Process
Development Process
- Modify Spring Security Configuration to reference custom login form
- Develop a Controller to show the custom login form
- Create custom login form
    - HTML (CSS optional)
    - Spring MVC form tag <form:form>

## Step 1: Modify Spring Security Configuration

|Method|Description|
|------|-----------|
|configure(AuthenticationManagerBuilder)|Configure users (in memory, database, ldap, etc)|
|configure(HttpSecurity)|Configure security of web paths in application, login, logout etc|

**DemoSecurityConfig.java**
```Java
@Configuration
@EnableWebSecurity
public class DemoSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    // add our users for in memory authentication
    …
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception { /// configure security of web paths in applications, login , logout etc. 
        http.authorizeRequests() /// restrict access based on the HttpServletRequest
        .anyRequest().authenticated() /// any request to the app must be authenticated(i.e logged in)
        .and()
        .formLogin() /// we are customizing the form login process 
        .loginPage("/showMyLoginPage") /// show our custom form at the request mapping "/showMyLoginPage"
        .loginProcessingUrl("/authenticateTheUser") /// Login form should POST data to this URL for processing (check user id and password)
        .permitAll(); /// Allow everyone to see login page. No need to logged in . 
    }
}
```
- We can give any name to the loginPage and loginProcessingUrl, as long as we stay consistent with the project. 
- To Do : 
    - We need to create a controller for this request mapping `/showMyLoginPage`.
    - No controller mapping required for `/authenticateTheUser` (provided by default by the spring)
     

## Step 2: Develop a controller to show the custom login form
**LoginController.java**
```Java
@Controller
public class LoginController {

    @GetMapping("/showMyLoginPage")
    public String showMyLoginPage() {
        return "plain-login";
    }
}
```
- To do : 
    - We need to create `plain-login.jsp`

## Create Custom login form
- Send data to login processing URL: /authenticateTheUser
    - Must POST the data
- Login processing URL will be handled by Spring Security Filters
- You get it for free … no coding required
- Best practice is to use the Spring MVC Form tag `<form:form>`
    - Provides automatic support for security defenses (more on this later)
- Spring Security defines default names for login form fields
```
User name field: username
Password field: password
```
```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
…
<form:form action="${pageContext.request.contextPath}/authenticateTheUser" method="POST">
    <p>
        User name: <input type="text" name="username" />
    </p>
    <p>
        Password: <input type="password" name="password" />
    </p>
    <input type="submit" value="Login" />
</form:form>
```
## Why use context path
- Allows us to dynamically reference context path of application
- Helps to keep links relative to application context path
- If you change context path of app, then links will still work
- Much better than hard-coding context path …
- Best Practice

## Error Obtained 
1. **No Error Message**: There is no error message shown as we are implementing the html page ourselves. 
    - Default login page provided by spring will have built in support for error messages. 
    - Because we have not added any logic to handle for login error messages, its not shown when we type in wrong userid, pass. 


## Failed Login
- When login fails, by default Spring Security will…
- Send user back to your login page
- Append an error parameter: ?error
- URL Looks like this : 
`http://localhost:8080/myapp/showMyLoginPage?error`

## Development Process
1. Modify custom login form
    - Check the error parameter
    - If error parameter exists, show an error message

## Step 1: Modify form - check for error
**plain-login.jsp**
```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
…
<form:form action="…" method="…">
    <c:if test="${param.error != null}”>
        
        <i>Sorry! You entered invalid username/password.</i>
    </c:if>
    User name: <input type="text" name="username" />
    Password: <input type="password" name="password" />
</form:form>
```
- If error param then show message


## Login form with Boostrap Enabled
**fancy-login.jsp**
```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>


<!doctype html>
<html lang="en">

<head>
	
	<title>Login Page</title>
	<meta charset="utf-8">
	<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
	
	<!-- Reference Bootstrap files -->
	<link rel="stylesheet"
		 href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
	
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.0/jquery.min.js"></script>
	
	<script	src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>

</head>

<body>

	<div>
		
		<div id="loginbox" style="margin-top: 50px;"
			class="mainbox col-md-3 col-md-offset-2 col-sm-6 col-sm-offset-2">
			
			<div class="panel panel-info">

				<div class="panel-heading">
					<div class="panel-title">Sign In</div>
				</div>

				<div style="padding-top: 30px" class="panel-body">

					<!-- Login Form -->
					<form:form action="${pageContext.request.contextPath}/authenticateTheUser" method="POST" class="form-horizontal">

					    <!-- Place for messages: error, alert etc ... -->
					    <div class="form-group">
					        <div class="col-xs-15">
					            <div>
									
									<c:if test="${param.error != null}">		            
										<div class="alert alert-danger col-xs-offset-1 col-xs-10">
											Invalid username and password.
										</div>
									</c:if>
									
									<!--		            
									<div class="alert alert-success col-xs-offset-1 col-xs-10">
										You have been logged out.
									</div>
								    -->

					            </div>
					        </div>
					    </div>

						<!-- User name -->
						<div style="margin-bottom: 25px" class="input-group">
							<span class="input-group-addon"><i class="glyphicon glyphicon-user"></i></span> 
							
							<input type="text" name="username" placeholder="username" class="form-control">
						</div>

						<!-- Password -->
						<div style="margin-bottom: 25px" class="input-group">
							<span class="input-group-addon"><i class="glyphicon glyphicon-lock"></i></span> 
							
							<input type="password" name="password" placeholder="password" class="form-control" >
						</div>

						<!-- Login/Submit Button -->
						<div style="margin-top: 10px" class="form-group">						
							<div class="col-sm-6 controls">
								<button type="submit" class="btn btn-success">Login</button>
							</div>
						</div>

					</form:form>

				</div>

			</div>

		</div>

	</div>

</body>
</html>
```

## Linking CSS file
- What would be required to link a CSS file for Spring Security login form? For example, I'd like to have a CSS file (from src/main/webapp/css/style.css) for the style of the error message when using Spring Security login form. I suspect that it block the request to the CSS if i am not authenticated? 

- Ans: 
1. You can use any type of CSS styling. Just to demonstrate the point, the heading for the login page will be blue. The error messages will be red.
**src/main/webapp/css/demo.css**
```css
.failed {
 color: red;
}
 
h3 {
 color: blue;
}
```
2. Reference the CSS file in your login page. In the head section of the login page, reference the CSS file. Here's the snippet
```html
<head>
 <title>Custom Login Page</title>
 <link rel="stylesheet" type="text/css" href="css/demo.css">
</head>
```

- and here's the complete code for this file.
**src/main/webapp/WEB-INF/view/plain-login.jsp**
```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>  
 
<html>
 
<head>
    <title>Custom Login Page</title>
    <link rel="stylesheet" type="text/css" href="css/demo.css">
</head>
<body>

<h3>My Custom Login Page</h3>
 <form:form action="${pageContext.request.contextPath}/authenticateTheUser"
            method="POST">
    
        <!-- Check for login error -->
        <c:if test="${param.error != null}">
            <i class="failed">Sorry! You entered invalid username/password.</i>
        </c:if>
            
        <p>
            User name: <input type="text" name="username" />
        </p>
 
        <p>
            Password: <input type="password" name="password" />
        </p>
        
        <input type="submit" value="Login" />
        
    </form:form>
 
</body>
 
</html>
```

3. Configure Spring Security to allow unauthenticated requests (permit all)to the "/css" directory
Make note of this snippet. It permits all requests to CSS. No need for authentication. Here's the snippet
```Java
        http.authorizeRequests()
                .antMatchers("/css/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/showMyLoginPage")
                .loginProcessingUrl("/authenticateTheUser")
                .permitAll();        
```
- and here's the complete code for this file.

**DemoSecurityConfig.java**
```java
package com.luv2code.springsecurity.demo.config;
 
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.User.UserBuilder;
 
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
 
    @Override
    protected void configure(HttpSecurity http) throws Exception {
 
        http.authorizeRequests()
                .antMatchers("/css/**").permitAll()
                .anyRequest().authenticated()
            .and()
            .formLogin()
                .loginPage("/showMyLoginPage")
                .loginProcessingUrl("/authenticateTheUser")
                .permitAll();        
    }
        
}
```

4. Configure the all Java configuration to serve content from the "/css" directory.
    - Update your DemoAppConfig to implement the WebMvcConfigurer interface. 
    - This allows you to override a method for serving up content from the "/css" directory. 
    - Here's the snippet
```Java
 @Override
 public void addResourceHandlers(ResourceHandlerRegistry registry) {
     registry.addResourceHandler("/css/**").addResourceLocations("/css/");
 }
```
- and here's the complete code for this file.
```Java
package com.luv2code.springsecurity.demo.config;
 
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
 
@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "com.luv2code.springsecurity.demo")
public class DemoAppConfig implements WebMvcConfigurer {
 
    // define a bean for ViewResolver
 
    @Bean
    public ViewResolver viewResolver() {
 
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
 
        viewResolver.setPrefix("/WEB-INF/view/");
        viewResolver.setSuffix(".jsp");
 
        return viewResolver;
    }
 
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
    }
}
```

- Once you apply these changes then you'll see use of the CSS on the login form. When login form is first loaded you'll see the blue heading based on CSS style. If there is a failed login, you'll see the red error message based on CSS style.