## RADIO BUTTONS HANDLING MVC

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
	
	// Added new variable to handle various Languages 
	private String favouriteLanguage;
	
	// do remember that the key are the values that would be saved in the values 
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
	
	public String getFavouriteLanguage() {
		return favouriteLanguage;
	}

	public void setFavouriteLanguage(String favouriteLanguage) {
		this.favouriteLanguage = favouriteLanguage;
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
		Country: 
		<form:select path="country">
		   	<form:options items="${student.countryOptions}" />
		</form:select>
		<br>
		Favourite Language: 
		Java <form:radiobutton path="favouriteLanguage" value="Java"/>
		C# <form:radiobutton path="favouriteLanguage" value="C#"/>
		PHP <form:radiobutton path="favouriteLanguage" value="PHP"/>
		Ruby <form:radiobutton path="favouriteLanguage" value="Ruby"/>
		<br>
		<input type="submit" value="Submit" />
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
    <br>
    Favourite Language: ${student.favouriteLanguage} 
    </body>
    
</html> 
```

## Populating the Radio Buttons with A inner list

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
	
	
	private String favouriteLanguage;
	private LinkedHashMap<String, String> favoriteLanguageOptions;
	
	
	

	// do remember that the key are the values that would be saved in the values 
	public Student() {
		countryOptions = new LinkedHashMap<>();
		countryOptions.put("BR", "BRAZIL");
		countryOptions.put("FR", "FRANCE");
		countryOptions.put("DE", "GERMANY");
		countryOptions.put("IN", "INDIA");
		countryOptions.put("US", "UNITED STATES OF AMERICA");
		
		
		// populate favorite language options
        favoriteLanguageOptions = new LinkedHashMap<>();
        
        // parameter order: value, display label
        favoriteLanguageOptions.put("Java", "Java");
        favoriteLanguageOptions.put("C#", "C#");
        favoriteLanguageOptions.put("PHP", "PHP");
        favoriteLanguageOptions.put("Ruby", "Ruby"); 
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
	
	public String getFavouriteLanguage() {
		return favouriteLanguage;
	}

	public void setFavouriteLanguage(String favouriteLanguage) {
		this.favouriteLanguage = favouriteLanguage;
	}
	
	public LinkedHashMap<String, String> getFavoriteLanguageOptions() {
		return favoriteLanguageOptions;
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
		Country: 
		<form:select path="country">
		   	<form:options items="${student.countryOptions}" />
		</form:select>
		<br>
		Favourite Language: 
		<form:radiobuttons path="favouriteLanguage" items="${student.favoriteLanguageOptions}"  />
		<br>
		<input type="submit" value="Submit" />
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
    <br>
    Favourite Language: ${student.favouriteLanguage} 
    </body>
    
</html> 
```


