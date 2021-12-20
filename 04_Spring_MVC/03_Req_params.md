## REQUEST PARAMS AND REQUEST MAPPINGS

### Code Example
- We want to create new method to process form data 
- Read the form data : student's  name
- Convert the name to upper case
- Add the uppercase version to model 

## `@RequestParam` use 
- Direct binding of request parameter to a local variable in function parameter

```Java
@RequestMapping("/processFormVersionTwo")
public String letsShoutDude(
    @RequestParam("studentName") String theName,
    Model model 
){
    // now we can use the variable theName
}
```

### Implementing the Coding Example

- Controller Changes 

`HelloWorldController.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

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
	
	// need a controller methdod to show the JS and CSS oriented static page
	@RequestMapping("/showFormVersionTwo")
	public String showFormVersionTwo() {
			return "htmlExample";
	}
	
	
	
	
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
	
    /*************************************************************************************************/
	// New controller method to read form data 
	// add the provided data with some processing to the model --> from where application can access
	// This mapping uses @RequestParam to extract data
	@RequestMapping("/processFormVersionThree")
	public String processFormVersionThree(@RequestParam("studentName") String theName , Model model) {
					
			// convert the data to all upper case
			theName = theName.toUpperCase();
			
			// create the message
			String result = "Hey My Friend from V3!" + " " + theName; 
			
			// add message to the model 
			model.addAttribute("message",result);
			
			return "helloworld";
	}
    /*************************************************************************************************/
}

```
- JSP changes 

`helloworld-form.jsp`
```html
<!DOCTYPE html> 
<html> 
<head> 
	<title>Hello World - Input Form</title>
</head>
<body>
    <!-- Sending request to processFormVersionThree -->
	<form action = "processFormVersionThree" method="GET">
		<input type = "text" name="studentName" placeholder="What's your name? "/>
		<input type = 	"submit" />
	</form>
</body>
</html>
```

## ADDING REQUEST MAPPING TO CONTROLLER 
- Serves as parent mapping for controller
- All request mappings on methods in the controller are relative. 
- Similar to folder directory strucuture 
```
folder/
├── folder1/
└── folder2/

folder_another/
├── folder_a/
└── folder_b/
```  

#### EX: 

```Java
@RequestMapping("/funny")
public class FunnyController{
    @RequestMapping("/showForm")
    public String showForm(){
        ...
    }

    @RequestMapping("/processForm")
    public String processForm(){
        ...
    }
}
```

## ERROR SCENERIOS
- Created a new controller 
- As in our project there is already a handler for /showForm , running with this configuration is going to give HTTP 500 error. 
- Will give IllegalStateException with Ambigious request . 


`SillyController.java`
```Java
package com.luv2code.springdemo.mvc;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class SillyController {
	
	@RequestMapping("/showForm")
	public String displayTheForm() {
		return "silly";
	}
}
```

## Adding request mapping implementation

`HelloWorldController.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.servlet.http.HttpServletRequest;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

// High level mapping is provided 
@Controller
@RequestMapping("/hello")
public class HelloWorldController {
	// All the below mapping becomes relative to /hello
	/*
    * /hello
    * ├── /hello/showForm/
    * ├── /hello/processForm/
    * ├── /hello/showFormVersionTwo/
    * ├── /hello/processFormVersionTwo/
    * └── /hello/processFormVersionThree/
	*/
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
	
	// need a controller methdod to show the JS and CSS oriented static page
	@RequestMapping("/showFormVersionTwo")
	public String showFormVersionTwo() {
			return "htmlExample";
	}
	
	
	
	
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
	
	// New controller method to read form data 
	// add the provided data with some processing to the model --> from where application can access
	// This mapping uses @RequestParam to extract data
	@RequestMapping("/processFormVersionThree")
	public String processFormVersionThree(
			@RequestParam("studentName") String theName ,
			Model model) {
					
			// convert the data to all upper case
			theName = theName.toUpperCase();
			
			// create the message
			String result = "Hey My Friend From v3!" + " " + theName; 
			
			// add message to the model 
			model.addAttribute("message",result);
			
			return "helloworld";
	}
}

```

`SillyController.java`
```Java
package com.luv2code.springdemo.mvc;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class SillyController {
	
	@RequestMapping("/showForm")
	public String displayTheForm() {
		return "silly";
	}
}
```

`main-menu.jsp`
```html
<!DOCTYPE html> 
<html>
	<body>
		<h2> Spring MVC Demo - Home Page</h2> 
		<a href="hello/showForm"> Hello world Form </a>
		<br>
		<a href="hello/showFormVersionTwo">Hello World Form With Images</a>
	</body>
</html>
```

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
	
	<!-- when opening this form , we would have already reached /hello -->
	<a href="showForm">Plain Hello World</a>
	
	<br><br>
	
	<img src="${pageContext.request.contextPath}/resources/images/spring-logo.png" />
	
	<br><br>
	
	<input type="button" onclick="doSomeWork()" value="Click Me"/>

</body>

</html>
```