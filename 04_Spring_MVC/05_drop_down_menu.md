## Creating Drop Down menu
- On form loading spring will call `student.getCountry(...)`
- On submit spring will call `student.setCountry(...)`

```html
<!-- Country defines property for Binding -->
<form:select path="country">
    <form:option value="Brazil" label="Brazil" />
    <form:option value="France" label="France" />
    <form:option value="Germany" label="Germany" />
    <form:option value="India" label="India" />
</form:select>

```

## Implementing the Spring Framework 

`Student.java`
```Java
package com.luv2code.springdemo.mvc;

public class Student {
	private String firstName; 
	private String lastName; 
	private String country; 
	
	public String getCountry() {
		return country;
	}

	public void setCountry(String country) {
		this.country = country;
	}

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
		<br>
		Country: 
			<form:select path="country">
		    	<form:option value="Brazil" label="Brazil" />
		    	<form:option value="France" label="France" />
		    	<form:option value="Germany" label="Germany" />
		    	<form:option value="India" label="India" />
			</form:select>
		</form:form>
	</body>
</html>
```

`student-confirmation.jsp`
```Java
<!DOCTYPE html> 
<html>
<head>
	<title>Student Confirmation</title>
</head>
    <body> 
    The student is confirmed : ${student.firstName} ${student.lastName}
    <br>
    Country: ${student.country} 
    </body>
</html>
```

## Read a list of countries from Java Class - Implementation

`Student.java`
```Java
package com.luv2code.springdemo.mvc;

import java.util.LinkedHashMap;

public class Student {
	private String firstName; 
	private String lastName; 
	private String country; 
	
	// To add country options 
	private LinkedHashMap<String,String> countryOptions; 
	
    // Do remeber the KEY are values which would be later assinged to country
    public Student() {
		countryOptions = new LinkedHashMap<>();
		countryOptions.put("BR", "BRAZIL");
		countryOptions.put("FR", "FRANCE");
		countryOptions.put("DE", "GERMANY");
		countryOptions.put("IN", "INDIA");
		countryOptions.put("US", "UNITED STATES OF AMERICA");
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
	
	public String getCountry() {
		return country;
	}

	public void setCountry(String country) {
		this.country = country;
	}
	
	// To return country options
	public LinkedHashMap<String, String> getCountryOptions() {
		return countryOptions;
	}
}
```

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
		<br>
		Country: 
			<form:select path="country">
		    	<form:options items="${student.countryOptions}" />
			</form:select>
		</form:form>
	</body>
</html>
```

`student-confirmation.jsp`
```html
<!DOCTYPE html> 
<html>
<head>
	<title>Student Confirmation</title>
</head>
    <body> 
    The student is confirmed : ${student.firstName} ${student.lastName}
    <br>
    Country: ${student.country} 
    </body>
    
</html> 
```

`We can also use .properties file to load the option list for drop down menu`