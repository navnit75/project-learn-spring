## Developement Process
- Download and import Maven starter files []()
- Add maven dependency for jackson project. 
- Create student POJO Java class
- Create main Driver app.

## Implementation
**Address.java**
```Java
package com.luv2code.jackson.json.demo;

public class Address {
	private String street; 
	private String city; 
	private String state; 
	private String zip; 
	private String country; 
	
	public Address() {
		
	}

	public String getStreet() {
		return street;
	}

	public void setStreet(String street) {
		this.street = street;
	}

	public String getCity() {
		return city;
	}

	public void setCity(String city) {
		this.city = city;
	}

	public String getState() {
		return state;
	}

	public void setState(String state) {
		this.state = state;
	}

	public String getZip() {
		return zip;
	}

	public void setZip(String zip) {
		this.zip = zip;
	}

	public String getCountry() {
		return country;
	}

	public void setCountry(String country) {
		this.country = country;
	}

}
```

**Student.java**
```Java
package com.luv2code.jackson.json.demo;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown=true)
public class Student {
	private int id; 
	private String firstName; 
	private String lastName; 
	private boolean active; 
	private Address address; 
	private String languages[];
	



	public Student() {}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
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

	public boolean isActive() {
		return active;
	}

	public void setActive(boolean active) {
		this.active = active;
	}
	
	public Address getAddress() {
		return address;
	}

	public void setAddress(Address address) {
		this.address = address;
	}
	
	public String[] getLanguages() {
		return languages;
	}

	public void setLanguages(String[] languages) {
		this.languages = languages;
	}
}

```

**Driver.java**
```Java
package com.luv2code.jackson.json.demo;

import java.io.File;

import com.fasterxml.jackson.databind.ObjectMapper;

public class Driver {

	public static void main(String[] args) {
		try {
			
			// create object mapper 
			ObjectMapper mapper = new ObjectMapper();
			
			
			// read JSON file and map/convert to Java POJO : data/sample-lite.json
			// Student theStudent = mapper.readValue(new File("data/sample-lite.json"), Student.class);
			Student theStudent = mapper.readValue(new File("data/sample-full.json"), Student.class);
			
			// print first name and last name 
			System.out.println("First Name: " +theStudent.getFirstName());
			System.out.println("Last Name: " + theStudent.getLastName());
			
			// print out the address: street and city
			Address tempAddress = theStudent.getAddress();
			System.out.println("Street: " + tempAddress.getStreet());
			System.out.println("City: " + tempAddress.getCity());
			
			// print out languages
			for(String tempLang: theStudent.getLanguages()) {
				System.out.println(tempLang);
			}
		}
		catch(Exception exc) {a
			exc.printStackTrace();
		}

	}

}

```
**sample-full.json**
```json
{
	"id": 14,
	"firstName": "Mario",
	"lastName": "Rossi",
	"active": true,
	"address": {
		"street": "100 Main St",
		"city": "Philadelphia",
		"state": "Pennsylvania",
		"zip": "19103",
		"country": "USA"
	},
	"languages" : ["Java", "C#", "Python", "Javascript"],
	"company":"Acme Inc"
}
```
**sample-lite.json**
```json
{
	"id": 14,
	"firstName": "Mario",
	"lastName": "Rossi",
	"active": true
}
```

## Output
```
First Name: Mario
Last Name: Rossi
Street: 100 Main St
City: Philadelphia
Java
C#
Python
Javascript
```