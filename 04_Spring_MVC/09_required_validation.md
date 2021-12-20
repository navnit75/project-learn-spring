## Dev Process
- Add validation rule to the customer class

`Customer.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class Customer {

	private String firstName; 
	
	@NotNull
	@Size(min=1,message="is required")
	private String lastName;
	
	
	public Customer() {
		
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
- Display error messages on HTML form 

`customer-form.jsp`
```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html> 
<html> 
	<head>
		<title>Customer Registeration Form</title>
		<style>
			.error{
			color:red
			}
		</style>
	</head>
	<body> 
		
		<i>Fill out the form. Asterix(*) means required. </i>
		<form:form action = "processForm" modelAttribute="customer">	
		
		First Name: <form:input path="firstName" />
		<br>
		Last Name(*): <form:input path="lastName" />
		
		<!-- form:error added here for the displaying the errors.  -->
		<!--  If validation fails we would be displaying the message based on the CSS provided here -->
		<form:errors path="lastName" cssClass="error" />
		<br>
		
		<input type="submit" value="Submit" />
		
		</form:form>
		
	</body>
	
</html>
```
- Perform validation in the Controller 

`CustomerController.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.Valid;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/customer")
public class CustomerController {
	
	@RequestMapping("/showForm")
	public String showForm(Model theModel) {
		theModel.addAttribute("customer",new Customer());
		return "customer-form";
	}
	
    /* Important part here 
     * Basically algo here is to validate the customer object
     * Spring stores the results of validations in the BindingResult object. 
     * In later part of the code, we are using that variable to verify if there are any errors. 
     */ 
	 @RequestMapping("/processForm")
	 public String processForm(
			 @Valid @ModelAttribute("customer") Customer theCustomer,
			 BindingResult theBindingResult) {
		 
		 if(theBindingResult.hasErrors()) 
			 return "customer-form";
		 
		 else
			 return "customer-confirmation";
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
		<a href="student/showForm">Student Registeration Form</a>
		<br>
		<a href="hello/showFormVersionTwo">Hello World Form With Images</a>
		<br>
		<a href="customer/showForm">Customer Form</a>
	</body>
</html>
```
- Update confirmation Page. 

`customer-confirmation.jsp`
```html
<!DOCTYPE html> 
<html>
<head>
	<title>Customer Confirmation</title>
</head>
    <body> 
    The customer is confirmed : ${customer.firstName} ${customer.lastName}
    </body>
</html> 
```
## Things to keep in mind
- When performing Spring MVC validation, the location of the `BindingResult` parameter is very important.
- In the method signature, the BindingResult parameter must appear immediately after the model attribute. 

- If you place it in any other location, Spring MVC validation will not work as desired. 
- In fact, your validation rules will be ignored

```Java
@RequestMapping("/processForm")
        public String processForm(
                @Valid @ModelAttribute("customer") Customer theCustomer,
                BindingResult theBindingResult) {
            ...            
        }
```

## Issues
- Last name field with all the white spaces passed the validation but shoudl have failed
- To handle this kind of situation there is another annotation `@InitBinder`. 

## @InitBinder
- @InitBinder annotation works as a pre-processor. 
- It will pre-process each web request to our controller. 
- Method annotated with @InitBinder is executed. 
- We will be using this to trim Strings. 
    - Remove leading and trailing white space. 
- If only Strings only had the White spaces --- trim it to NULL. 
- Will resolve our validation problem. 

`CustomerController.java`
```Java
....
@InitBinder
public void initBinder(WebDataBinder dataBinder){
    StringTrimmerEditor stringTrimmerEditor = new StringTrimmerEditor(true);
    dataBinder.registerCustomEditor(String.class,stringTrimmerEditor);
}
```

`CustomerController.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.Valid;

import org.springframework.beans.propertyeditors.StringTrimmerEditor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/customer")
public class CustomerController {
	
	// add an init binder ... to convert 
	// remove leading and trailing white spaces 
	// resolve issue for our validation
	@InitBinder
	public void initBinder(WebDataBinder dataBinder){
	    StringTrimmerEditor stringTrimmerEditor = new StringTrimmerEditor(true);
	    dataBinder.registerCustomEditor(String.class,stringTrimmerEditor);
	}
	
	
	@RequestMapping("/showForm")
	public String showForm(Model theModel) {
		theModel.addAttribute("customer",new Customer());
		return "customer-form";
	}
	
	 @RequestMapping("/processForm")
	 public String processForm(
			 @Valid @ModelAttribute("customer") Customer theCustomer,
			 BindingResult theBindingResult) {
		 
		 System.out.println("Last Name : |" + theCustomer.getLastName() + "|");
		 if(theBindingResult.hasErrors()) 
			 return "customer-form";
		 
		 else
			 return "customer-confirmation";
	 }
}
```
- With only this , the message we would get would be value shouldn't be null. 
- If we want to customise it then we can do 

`Customer.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class Customer {

	private String firstName; 
	
	@NotNull(message="is required")
	@Size(min=1, message="is required")
	private String lastName;
	
	
	public Customer() {
		
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
	
	
```