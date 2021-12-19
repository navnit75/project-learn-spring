## Form Tags
- Spring MVC Form tags are the building blocks for a web page. 
- Form tags are configurable and are reusable for a web page. 

## Data Binding 
- Sprin MVC form tags can make use of data binding 
- Automatically settin / retrieving data from a Java object/bean. 

## Form tags will generate HTML for you
|Form Tag|Description|
|--------|-----------|
|form:form|main form container|
|form:input|text field|
|form:textarea|multi line text field|
|form:checkbox|check box|
|form:radiobutton|radio buttons|
|form:select|drop down list|
|more...||


## Web Page Structure
- JSP Page with special Spring MVC form tag. 
```html
<html>
    ... regular html ... 
    ... Spring MVC form tag ...
    ... more html ... 
</html>
```

## How to reference Spring MVC tags 
- Specify the spring namespace at the beginning of JSP file.
```java
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
```

## Big Picture
```
                   Student                          Student
student-form.jsp ------------>Student Controller ------------->student-confirmation.jsp   
```

### Showing form 
- In your spring controller 
    - Before you show the form, you must add a model attribute 
    - This is a bean that will hold form data for the data binding. 


## Code snippet from controller 
- We are putting a blank object into model 
- So that it can be edited from the JSP side. 

```Java
@RequestMapping("/showForm")
public String showForm(Model theModel){
    theModel.addAttribute("student",new Student());
    return "student-form";
}
```

## Setting up html form - Data Binding 
- modelAttrribute should match the addAttribute parameter from above. 
- When form is loaded `student.getFirstName()` would be called. 
- When form is submitted `student.setFirstName()` would be called. 

```java
<form:form action="processForm" modelAttribute="student">
 First Name : <form:input path="firstName" />
 <br><br>
 Last Name : <form:input path="lastName" />
 <br><br>
 <input type="submit" value="Submit" />
</form:form>
```

## Handling Form submission in the controller 
- To access a model attribute at the controller side we use `@ModelAttribute`, this will bind the content of `student` to `theStudent`.
```Java
@RequestMapping("/processForm")
public String processForm(@ModelAttribute("student") Student theStudent){

    // log the input data
    System.out.println("theStudent: "+theStudent.getLastName());
    return "student-confirmation";
}
```

## The confirmation Page. 
```html
<html>
    <body> The student is confirmed : ${student.firstName} ${student.lastName}</body>
</html>
```

## Implementation HANDLING TEXT FIELDS
- Create Student Class 

`Student.java`
```Java
package com.luv2code.springdemo.mvc;
public class Student {
	private String firstName; 
	private String lastName; 
	
	public Student() {
		
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}
	
}
```
- Create Student Controller Class
- Create form processing code

`StudentController.java`
```Java
package com.luv2code.springdemo.mvc;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/student")
public class StudentController {
	
	@RequestMapping("/showForm")
	public String showForm(Model theModel) {
		
		// create a Student object
		Student theStudent = new Student(); 
		
		// add student object to the model 
		theModel.addAttribute("student",theStudent);
		
		return "student-form";
	}
	
	@RequestMapping("/processForm")
	public String processForm(@ModelAttribute("student") Student theStudent) {
		
		// printing the log 
		System.out.println("theStudent: " + theStudent.getFirstName() + " " + theStudent.getLastName());
		return "student-confirmation";
	}
}
```

- Create HTML form

`student-form.jsp`
```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html> 
<html> 
	<head>
		<title>Student Registeration Form</title>
	</head>
	<body> 
		<form:form action = "processForm" modelAttribute="student">	
		First Name: <form:input path="firstName" />
		<br>
		Last Name : <form:input path="lastName" />
		<br>
		<input type="submit" value="Submit" />
		</form:form>
	</body>
</html>
```
- Create confirmation page

`student-confirmation.jsp`
```html
<!DOCTYPE html> 
<html>
<head>
	<title>Student Confirmation</title>
</head>
    <body> The student is confirmed : ${student.firstName} ${student.lastName}</body>
</html>
```

`main-menu.jsp`
```html
<!DOCTYPE html> 
<html>
	<body>
		<h2> Spring MVC Demo - Home Page</h2> 
		
		<a href="hello/showForm"> Hello world Form </a>
		<br>
		<a href="student/showForm">Student Registeration Form</a>
		<br>
		<a href="hello/showFormVersionTwo">Hello World Form With Images</a>
	</body>
</html>
```