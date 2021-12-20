## Process 
### Add validation rules to Customer class 

`Customer.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class Customer {

	private String firstName; 
	
	@NotNull(message="is required")
	@Size(min=1, message="is required")
	private String lastName;
	
	@Min(value=0, message="must be greater than or equal to zero")
	@Max(value=10, message="must be smaller than or equal to ten")
	private int freePasses; 
	
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
	public int getFreePasses() {
		return freePasses;
	}
	public void setFreePasses(int freePasses) {
		this.freePasses = freePasses;
	} 
}

```
### Display error messages on HTML form. 

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
		
		Free Passes: <form:input path="freePasses" />
		<form:errors path="freePasses" cssClass="error" />
		
		
		<input type="submit" value="Submit" />
		
		</form:form>
		
	</body>
	
</html>
```
### Perform validation on the controller. 
- No validation at customer controller required. 
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
### Update confirmation page.

`customer-confirmation.jsp`
```Java
<!DOCTYPE html> 
<html>
<head>
	<title>Customer Confirmation</title>
</head>
    <body> 
    The customer is confirmed : ${customer.firstName} ${customer.lastName}
    <br>
    Free Passes : ${customer.freePasses}
    </body>
    
</html> 
```
