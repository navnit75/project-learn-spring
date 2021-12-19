## Start MVC configuration Process Part - 1
- Add configuration to file : WEB-INF/web.xml 
1. Configure Spring MVC DispatcherServlet

`web.xml`
```xml
<servlet>
        <!-- Servlet Reference -->
        <!-- We get it for free , as it is part of SPRING -->
		<servlet-name>dispatcher</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		
        <!-- Configuring the Context, and tell where the configuration is located -->
        <init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring-mvc-demo-servlet.xml</param-value>
		</init-param>

		<load-on-startup>1</load-on-startup>
	</servlet>
```
2. Set up URL mappings to Spring MVC Dispatcher Servlet.

`web.xml`
```xml
<servlet>
...
<servlet>
<!-- We want to tell the system any URL coming in, I like you to pass it through dispatcher service  -->
<!-- You can have different pattern as well , here it represents all the requests  -->
<servlet-mapping>
        <!-- sevlet-name needs to match with the intialised servlet-name -->
		<servlet-name>dispatcher</servlet-name>
		<url-pattern>/</url-pattern>
</servlet-mapping>
```

## MVC configuration process part - 2
- Add configuratio to file : WEB-INF/servlet-mvc-demo-servlet.xml 
3. Add support for Spring Component Scanning. 

`servlet-mvc-demo-servlet.xml`
```xml
<context:component-scan base-package="com.luv2code.springdemo" />
```
4. Add support for conversion, formatting and validation. 

`servlet-mvc-demo-servlet.xml`
```xml
<mvc:annotation-driven/>
```

5. Configure Spring MVC View Resolver. 

`servlet-mvc-demo-servlet.xml`
```xml
<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/view/" />
		<property name="suffix" value=".jsp" />
</bean>
```

## Architecture
```                    
+------------+             +------------------+
|            |------------>| Front Controller |
|            |             +------------------+
|  Req Path  |                      | MODEL
|     /      |                      V
|            |               +---------------+
|    Web     |               |  Controller   | (Home Controller)
|  Browser   |               +---------------+
|            |                      | MODEL 
|            |                      V
|            |               +---------------+
|            |<--------------| View Template | (mainmenu.jsp)
+------------+               +---------------+
``` 

## Dev Process 
1. Create controller class
    - Annotate class with `@Controller`
    - `@Controller` inherits from `@Component`... supports scanning
    
    
2. Define Controller method 
3. Add request mapping to controller method
4. Return view name 

`HomeController.java`
```Java
package com.luv2code.springdemo.mvc;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HomeController {
        
    // Get and Post all the methods 
    @RequestMapping("/")
    public String showPage() {
            
        // this is not the complete name of the page
        // we will add information from our config file 
        // /WEB-INF/view/main-menu.jsp
        return "main-menu";
    }
}

```
5. Develop View page. 

`main-menu.jsp`
```html
<!DOCTYPE html> 
<html>
    <body>
        <h2> Spring MVC Demo - Home Page</h2> 
    </body>
</html>
```

## Processing Form Data
- Form opens by `localhost:9090/<project-name>/<mapping>`

- New Controller Created 

`HelloWorldController.java`
```Java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloWorldController {
	// need a controller methdod to show the intial form 
	@RequestMapping("/showForm")
	public String showForm() {
		return "helloworld-form";
	}
			
	// need a controller method to process the HTML form 
	@RequestMapping("/processForm")
	public String processForm() {
		return "helloworld";
	}
}
```
- Two More JSP's added and one edited

`helloworld-form.jsp`
```html
<!DOCTYPE html> 
<html> 
<head> 
	<title>Hello World - Input Form</title>
</head>
<body>
	<form action = "processForm" method="GET">
		<input type = "text" name="studentName" placeholder="What's your name? "/>
		<input type = 	"submit" />
	</form>
</body>
</html>
```

`helloworld.jsp`
```html
<!DOCTYPE html> 
<html> 
	<body>
		Hello world of Spring1!
		<br><br> 
		Student name: ${param.studentName}
	</body>
</html>
```

`main-menu.jsp`
```html
<!DOCTYPE html> 
<html>
	<body>
		<h2> Spring MVC Demo - Home Page</h2> 
		<a href="showForm"> Hello world Form </a>
	</body>
</html>
```