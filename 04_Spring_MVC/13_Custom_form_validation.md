## Creating our own validations 
- We are taking a usecase of course code which should always start from LUV. 
- Spring MVC calls our validation rules and custom validation returns either true / false.
- We are going to create a custom annotation called @CourseCode
- Usage

```Java
@CourseCode(value="LUV", message="must start with LUV")
private String courseCode; 
```
## Process
### Create custom validation rules 
#### Created a new Package `com.luv2code.springdemo.mvc.validation`
#### a. Create `@CourseCode` annotation

`CourseCode.java`
```Java
package com.luv2code.springdemo.mvc.validation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import javax.validation.Constraint;
import javax.validation.Payload;

@Constraint(validatedBy=CourseCodeConstraintValidator.class)
@Target({ElementType.METHOD,ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface CourseCode {

	// define default course code
	public String value() default "LUV";
	
	// define default error message
	public String  message() default "must start with LUV";
	
	// define default groups
	public Class<?>[] groups() default{};
	
	// defin default payloads
	public Class<? extends Payload>[] payload() default {};
}
 
```
#### b. Create `CourseCodeConstraintValidator`
- Helper class which contains custom business logic 

```Java
package com.luv2code.springdemo.mvc.validation;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

// In the type provided while implementing 
// CourseCode defines the name of annotation, and String denotes type of data it would be handling
public class CourseCodeConstraintValidator implements ConstraintValidator<CourseCode,String>{

	private String coursePrefix; 
	@Override
	public boolean isValid(String theCode, ConstraintValidatorContext arg1) {
		boolean result; 
		if(theCode!=null)
			 result = theCode.startsWith(coursePrefix);
		else
			result = true;
		return result;
	}
	
	
	// Assigning the value field data provided in annotation parameters to the coursePrefix
	@Override
	public void initialize(CourseCode theCourseCode) {
		coursePrefix = theCourseCode.value();
	}
	
}

```

`Customer.java`
```Java
package com.luv2code.springdemo.mvc;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Pattern;
import javax.validation.constraints.Size;

import com.luv2code.springdemo.mvc.validation.CourseCode;

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
	// NULL cannot handled by primitive type 
	private Integer freePasses; 
	
	
	 @Pattern(regexp="^[a-zA-Z0-9]{5}",message="only five chars/digits")
	private String postalCode; 
	
	@CourseCode(value="TOPS", message="must start with TOPS")
	private String courseCode; 
	
	
	public Customer() {
		
	}
	
	public String getCourseCode() {
		return courseCode;
	}

	public void setCourseCode(String courseCode) {
		this.courseCode = courseCode;
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

## Can we handle multiple prefixes with single annotation

####  Update CourseCode.java to use an array of strings

- Change the value entry to an array of Strings:
```Java
    // define default course code
    public String[] value() default {"LUV"};
```
#### Update CourseCodeConstraintValidator.java to validate against array of strings

- Change the field for coursePrefixes to an array
```Java
private String[] coursePrefixes; 
```
- Update the isValid(...) method to loop through the course prefixes. 
- In the loop, check to see if the code matches any of the course prefixes.
```Java
    @Override
    public boolean isValid(String theCode, 
                        ConstraintValidatorContext theConstraintValidatorContext) {
        boolean result = false;
        
        if (theCode != null) {
            
            //
            // loop thru course prefixes
            //
            // check to see if code matches any of the course prefixes
            //
            for (String tempPrefix : coursePrefixes) {
                result = theCode.startsWith(tempPrefix);
                
                // if we found a match then break out of the loop
                if (result) {
                    break;
                }
            }
        }
        else {
            result = true;
        }
        
        return result;
  }
```

#### Update Customer.java to validate using array of strings
```Java
@CourseCode(value={"TOPS", "LUV"}, message="must start with TOPS or LUV")
private String courseCode;
```