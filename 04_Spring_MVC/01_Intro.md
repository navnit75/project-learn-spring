## INTRODUCTION 
- Framework for building web applications in Java. 
- Based on `Model`, `View` and `Controller` design pattern. 
- Leverages features of the Core spring Framework(IoC, DI). 

## Architecture
```                    
+------------+             +------------------+
|            |------------>| Front Controller |
|            |             +------------------+
|            |                      | MODEL
|            |                      V
|            |               +---------------+
|    Web     |               |  Controller   |
|  Browser   |               +---------------+
|            |                      | MODEL 
|            |                      V
|            |               +---------------+
|            |<--------------| View Template |
+------------+               +---------------+
``` 
- In here `Front Controller` is known as **DispatcherServlet**. 
    - Part of Spring Framework. 
    - Already developed by Spring Dev Team. 
- You will create 
    - Model object ( Container for our object )
    - View Templates 
    - Controller Classes ( Yellow ) - Contains the business logic

## SPRING MVC BENEFITS 
- The spring way of building web apps in UIs in Java. 
- Leverage set of reusable UI components. 
- Help manage application state for web requests . 
- Process form data: Validation, conversion etc. 
- Flexible configuration for the view layer. 

## SPRING MVC DOCUMENTATION
- [Link Here](https://www.luv2code.com/spring-mvc-docs)

## Componentes of Spring MVC application
- A set of web pages to layout UI components. 
- A collection of Spring beans (controller, services, etc..)
- Spring configuration (XML, Annotations or Java)

## CONTROLLER 
- Code created by Developer
- Contains your business logic 
    - Handle the request
    - Store/Retrieve data(db,web service, ...)
    - Place data in model 
- Send to appropriate view template.

## MODEL 
- Model contains your data
- Its like the suitcase which is used to transfer the data , between different part of SPRING MVC. 
- Store / Retrieve data via backend system. 
    - Database, web service , etc...
    - Use a Spring bean if you like
- Place your data in the model 
    - Data can be any Java object/collection

## VIEW
- Spring MVC is flexible 
    - Supports many view templates. 

- Most common is JSP + JSTL 
```
JSP : Java Server Pages. 
JSTL : JSP Standard Tag Library
```
- Developer creates a page. 
    - Displays data. 

- Other view templates supported 
    - Thymeleaf, Groovy
    - Velocity, Freemarker, etc..
- For details see [link](https://www.luv2code.com/spring-mvc-views)

