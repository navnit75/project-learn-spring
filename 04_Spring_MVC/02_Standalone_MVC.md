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

## ADDING DATA TO SPRING MODEL 
- The model is a container for your application data
- Container that is common for Controller and View. 
- Data can be obtained via forms in Controller , with various Mapping funcitonality --> Processed --> Added to the model --> Accessed by View. 
- Model acts as BUFFER between Controller (Server, middleware) and View ( Front End )
- In your controller 
	- We can put anything in model 
	- strings, objects, info from database etc....
- Your view page can access data from model 

### CODE EXAMPLE 
- We want to create new method to process form data 
- Read the form data : student's  name
- Convert the name to upper case
- Add the uppercase version to model 

### Adding variety of data to the model 

```Java
String result = ...
List<Student> studentList = ...
ShoppingCart theShoppingCart = ...

// adding data to the model
model.addAttribute("message",result)
model.addAttribute("students",theStudentList)
model.addAttribute("shoppingCart",theShoppingCart)
```

### Passing model to controller 

`HelloWorldController.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
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
	
	/*************************************************************************************/
	// New controller method to read form data 
	// add the provided data with some processing to the model --> from where application can access
	@RequestMapping("/processFormVersionTwo")
	public String letsShoutDude(HttpServletRequest request , Model model) {
		// read the request parameter from html form 
		String theName = request.getParameter("studentName");
		
		// convert the data to all upper case
		theName = theName.toUpperCase();
		
		// create the message
		String result = "Yo!" + " " + theName; 
		
		// add message to the model 
		model.addAttribute("message",result);
		
		return "helloworld";
	}
	/**************************************************************************************/
}

```

### Changes in the JSP 

`helloworld.jsp`
```html
<!DOCTYPE html> 
<html> 
	<body>
		Hello world of Spring1!
		<br><br> 
		Student name: ${param.studentName}
		<br><br>

		<!-- Accessing data from model -->
		The Message : ${message}
	</body>
</html>
```

`helloworld-form.jsp`
```html
<!DOCTYPE html> 
<html> 
<head> 
	<title>Hello World - Input Form</title>
</head>
<body>
	<form action = "processFormVersion2" method="GET">
		<input type = "text" name="studentName" placeholder="What's your name? "/>
		<input type = 	"submit" />
	</form>
</body>
</html>
```

## Using HTML , CSS and JS combination 
- ![FolderStructure]()
- We need to keep in mind , `${pageContext.request.contextPath}` JSP property to access the JS, and CSS resources and images 

- Changes in configuration 
	- Step 6 is added to define the location of `resources` directory and to read all the subdirectories of resources folder by using `/resources/**`.

`spring-mvc-demo-servlet.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans
    	http://www.springframework.org/schema/beans/spring-beans.xsd
    	http://www.springframework.org/schema/context
    	http://www.springframework.org/schema/context/spring-context.xsd
    	http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

	<!-- Step 3: Add support for component scanning -->
	<context:component-scan base-package="com.luv2code.springdemo" />

	<!-- Step 4: Add support for conversion, formatting and validation support -->
	<mvc:annotation-driven/>

	<!-- Step 5: Define Spring MVC view resolver -->
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/view/" />
		<property name="suffix" value=".jsp" />
	</bean>
	
	<!-- Step 6: Adding Support for HTML , CSS , JS -->
	<mvc:resources mapping="/resources/**" location="/resources/"></mvc:resources>

</beans>
```

- Changes in Controller 
	- Added new mapping to return new view based on HTML and CSS page

`HelloWorldController.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
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
	
	/****************************************************************************/
	// need a controller methdod to show the JS and CSS oriented static page
	@RequestMapping("/showFormVersionTwo")
	public String showFormVersionTwo() {
			return "htmlExample";
	}
	/****************************************************************************/
	
	
	
	
	// New controller method to read form data 
	// add the provided data with some processing to the model --> from where application can access
	
	
	@RequestMapping("/processFormVersionTwo")
	public String letsShoutDude(HttpServletRequest request , Model model) {
		// read the request parameter from html form 
		String theName = request.getParameter("studentName");
		
		// convert the data to all upper case
		theName = theName.toUpperCase();
		
		// create the message
		String result = "Yo!" + " " + theName; 
		
		// add message to the model 
		model.addAttribute("message",result);
		
		return "helloworld";
	}
}
```
- New View made using CSS and JS

`htmlExample.jsp`
```html
<!DOCTYPE html> 
<html>
<head>
    <link rel="stylesheet" type="text/css" href="${pageContext.request.contextPath}/resources/css/my-test.css">
    <script src="${pageContext.request.contextPath}/resources/js/simple-test.js"></script>
</head>

<body>
	
	<h2>Spring MVC Demo - Home Page</h2>
	
	<a href="showForm">Plain Hello World</a>
	
	<br><br>
	
	<img src="${pageContext.request.contextPath}/resources/images/spring-logo.png" />
	
	<br><br>
	
	<input type="button" onclick="doSomeWork()" value="Click Me"/>

</body>

</html>
```
- Front Page Changes 

`main-menu.jsp`
```html
<!DOCTYPE html> 
<html>
	<body>
		<h2> Spring MVC Demo - Home Page</h2> 
		<a href="showForm"> Hello world Form </a>
		<br>
		<!-- Adding an extra link for accessing page based on HTML(JSP) + CSS + Java-->
		<a href="showFormVersionTwo">Hello World Form With Images</a>
	</body>
</html>
```