## Adding required field to int variable
- In our case freePass

`Customer.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;

public class Customer {

	private String firstName; 
	
	@NotNull(message="is required")
	@Size(min=1, message="is required")
	private String lastName;
	
	
	// We are trying to make the freePasses field REQUIRED
	@NotNull(message="is required")
	@Min(value=0, message="must be greater than or equal to zero")
	@Max(value=10, message="must be smaller than or equal to ten")
	
	// Earlier we have kept this field int
	// The change to wrapper class is imminent because of the fact, the values can also be trimmed into NULL 
	// NULL cannot be handled by primitive type 
	private Integer freePasses; 
	
	
	 @Pattern(regexp="^[a-zA-Z0-9]{5}",message="only five chars/digits")
	private String postalCode; 
	
	
	
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
	public Integer getFreePasses() {
		return freePasses;
	}
	public void setFreePasses(Integer freePasses) {
		this.freePasses = freePasses;
	} 
	public String getPostalCode() {
		return postalCode;
	}
	public void setPostalCode(String postalCode) {
		this.postalCode = postalCode;
	}
	
}
```

`Every other file remains same`

## Handling String input in the Integer form fields

### Create Custom error Messages

`src/resources/message.properties`
```
typeMismatch.customer.freePasses=Invalid Number

------------------------------------------
<error type> <Spring> <field Name> = <Custom message>
             <model >
             <attrib>
             <name>
```



### Load custom messages in Spring config file 

`WebContent/WEB-INF/spring-mvc-demo-servlet.xml`
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
	
	<!-- Step 7: Adding support for custom error messages -->
	<!-- Load the file from resources -->
	<bean id="messageSource"
	      class="org.springframework.context.support.ResourceBundleMessageSource">
	      <property name="basenames" value="resources/messages"  />
	
	</bean>
</beans>
```

## Why this property
- If we print theBindingObject (which stores the errors occurred)
```
TheBindingResult: org.springframework.validation.BeanPropertyBindingResult: 2 errors
Field error in object 'customer' on field 'freePasses': rejected value [adfadsf];
 codes [typeMismatch.customer.freePasses,typeMismatch.freePasses,typeMismatch.java.lang.Integer,typeMismatch]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [customer.freePasses,freePasses]; 
 arguments []; 
 default message [freePasses]]; 
 default message [Failed to convert property value of type 'java.lang.String' to required type 'java.lang.Integer' for property 'freePasses'; 
 nested exception is java.lang.NumberFormatException: For input string: "adfadsf"] Field error in object 'customer' on field 'lastName': rejected value [null]; 
 [NotNull.customer.lastName,NotNull.lastName,NotNull.java.lang.String,NotNull]; 
 arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [customer.lastName,lastName]; 
 arguments [];
 default message [lastName]]; 
 default message [is required]
```
`Here codes [typeMismatch.customer.freePasses]` --> we are just overriding that error. 
