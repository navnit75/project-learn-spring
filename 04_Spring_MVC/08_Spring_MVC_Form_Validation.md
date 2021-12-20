## Introduction
- Java has a standard Bean Validation API . 
- Defines a metadata model and API for entity validation. 
- Not tied to either the web tier or the persistence tier. 
- Available for server side apps and also client side JavaFX/Swing Apps. 
- Documentation is available at [link](http://www.beanvalidation.org)


## Spring Support for validation 
- Spring versio 4 and higher supports Bean Validation API.
- Preferred method for validation when Building Spring apps. 
- Simply add Validation JARs to our projects. 

## Validation features 
||
|--------|
|required|
|validate length|
|validate numbers|
|validate with regular expressions|
|Custom Validation|

## Validation Annotation

|Anonotation|Description|
|-----------|-----------|
|@NotNull|Checks that the annoted value is not null|
|@Min|Must be a number >= value|
|@Max|Must be a number <= value|
|@Size|Size must match the give size|
|@Pattern|Must match a regular expression pattern|
|@Future|@Past|Date must be in future or past og a given date|
|others|

## Java Standard Bean Validation API 
- Java's standard Bean Validation API (JSR - 303)
- Only a specification...vendor independent...portable. 
- But we still need an implementation

### The hibernate team to the rescue
- Hibernate started as an ORM project (Object Relational Mapping)
- But in recent years, they have expanded into other ares
- They have a fully compliant JSR - 303 implementation
    - Not ties to ORM or database work...separate project.
    - [Hibernate Validator](http://www.hibernate.org/validator)
- Hibernate Validator 7 is based on Jakarta EE 9. 
- Jakarta EE is the community version of Java EE(rebranded , relicensed) --> (servlets,JSP,JDBC,Enterprise Java Beans,Java Message Service)
- Allows innovation of Jakarta EE with community driver approach. 
- Jakarta EE does not replace Java EE 
    - Last version of Java EE 8 (Aug 2017)
    - Jakarta EE is moving forward with Jakarta EE 9(Dec 2020) and future releases. 


## Jakarta EE 
- At the moment main change with Jakarta EE...Package renaming 
- javax.* package are renamed to jakarta.*. 

For example: 
    - javax.servlet.http.HttpServlet is not jakarta.servlet.http.HttpServlet
    - [Check it out](https://jakarta.ee/about)

## Impact on Hibernate Validator? 
- Hibernate validator 7 is based on Jakarta EE 9. 
- Spring 5 is still based on some components of Java EE (javax.*)
- Spring may use Jakarta EE components in the future...but no news yet. 
- As a result, Spring 5 is not compatible with Hibernate Validator 7.  
- But hibernate 6.2 has the same features as Hibernate Validator 7. 
- If you are using Spring 5.x ---> use Hibernate Validator 6.x
- If you are using Jakarta EE 9--> use Hibernate Validator 7.x 

## Setting up 
- Download the hibernate validator 
- add the libraries provided in the top to the lib 
- Meanwhile add the required libraries to the lib