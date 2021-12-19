# INVERSION OF CONTROL 
- Its simply the design process of externalizing, the construction and management of your objects.
- So, in a nutshell it basically says that your application's going to outsource the creation and management of the objects.
- And that outsourcing will be handled by an object factory. 
### Ex. 
```
+----------------+     getDailyWorkout()       +-------------------+
|   My App       |---------------------------->| Baseball Coach    |
+----------------+                             +-------------------+

```
- App should be configurable
- Easily change the coach for other sports as well . 

## Code Demo 
---
```
MyApp.java           : Main Method
BaseballCoach.java   
Coach.java           : Interface after refactoring 
TrackCoach.java
```
MyApp.java
```Java
package com.luv2code.springdemo;
public class MyApp {

	public static void main(String[] args) {
		// create the object
		Coach theCoach = new TrackCoach(); 
		
		// use the object
		System.out.println(theCoach.getDailyWorkout());
	}

}

```
Coach.java
```Java
package com.luv2code.springdemo;
public interface Coach {
	// only a specification
	public String getDailyWorkout();
}
```
BaseballCoach.java
```Java
package com.luv2code.springdemo;
public class BaseballCoach implements Coach {
	
	@Override
	public String getDailyWorkout() {
		return "Spend 30 minutes on batting practice";
	}
}
```
TrackCoach.java
```Java
package com.luv2code.springdemo;

public class TrackCoach implements Coach {

	@Override
	public String getDailyWorkout() {
		// TODO Auto-generated method stub
		return "Run a hard 5k";
	}

}
```

- If I create above classes with say one method called getDailyWorkout(). 
- It satisfies my requirement but its not configurable. 

## Spring Containers
---
- Primary Functions
    - Create and Manage Objects (Inversion of Control)
    - Inject object's dependencies (Dependency Injection)
## Spring Bean
- A "Spring Bean" is simply a Java object.
- When Java objects are created by the Spring Container, then Spring refers to them as "Spring Beans".
- Spring Beans are created from normal Java classes .... just like Java objects.
- In the early days, there was a term called "Java Beans".
- Spring Beans have a similar concept but Spring Beans do not follow all of the rigorous requirements of Java Beans.
### What spring does for us? 
```
+----------------+  give me a "Coach" object   +-------------------+
|   My App       |---------------------------->| Object Factory    |
+----------------+                             +-------------------+
                                                        |
                                                        |
                                                   Configuration 
                                                   /    |    \
                                                  /     |     \
                                                 /      |      \
                                              Baseball Hockey Cricket
                                               Coach    Coach  Coach

```
- Three ways of configuring 
    - XML configuration file (legacy, but most legacy app still use this)
    - Java Annotations (modern)
    - Java Source Code (modern)

- Spring Developement Process
    - Configure your Spring Beans
    - Create Spring Container
    - Retrive beans from Spring Container
## ENABLING LOGS 
- Add a class in the project 
- This class will set the parent logger level for the application context. 
- It will also set the logging level for console handler. 
- It sets the logger level to FINE. 
- For more detailed logging info, you can set the logging level to level to FINEST
- MyLoggerConfig.java
```Java
package com.luv2code.springdemo;
 
import java.util.logging.ConsoleHandler;
import java.util.logging.Level;
import java.util.logging.Logger;
import java.util.logging.SimpleFormatter;
 
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
 
public class MyLoggerConfig {
 
	private String rootLoggerLevel;
	private String printedLoggerLevel;
	
	public void setRootLoggerLevel(String rootLoggerLevel) {
		this.rootLoggerLevel = rootLoggerLevel;
	}
 
	public void setPrintedLoggerLevel(String printedLoggerLevel) {
		this.printedLoggerLevel = printedLoggerLevel;
	}
 
	public void initLogger() {
 
		// parse levels
		Level rootLevel = Level.parse(rootLoggerLevel);
		Level printedLevel = Level.parse(printedLoggerLevel);
		
		// get logger for app context
		Logger applicationContextLogger = Logger.getLogger(AnnotationConfigApplicationContext.class.getName());
 
		// get parent logger
		Logger loggerParent = applicationContextLogger.getParent();
 
		// set root logging level
		loggerParent.setLevel(rootLevel);
		
		// set up console handler
		ConsoleHandler consoleHandler = new ConsoleHandler();
		consoleHandler.setLevel(printedLevel);
		consoleHandler.setFormatter(new SimpleFormatter());
		
		// add handler to the logger
		loggerParent.addHandler(consoleHandler);
	}
	
}
```

- Append following to the applicationContext.xml
```xml
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    	<property name="rootLoggerLevel" value="FINE" />
    	<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
```
## XML CONFIGURATION BASED
- Starter Code for 
- applicationContext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Define your beans here -->
    
</beans>
```

- Folder Structure used
![image](https://github.com/navnit75/spring_framework_learning/raw/main/01_legacy_spring/Folder_structure.png)

- xml configuration for the Track Coach class 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Define your beans here -->
    <!-- Class contains the name of fully quantified class name of implemented class -->
    <bean id = "myCoach"
    	class = "com.luv2code.springdemo.TrackCoach">
    	
    </bean>
</beans>

```

- HelloSpringApp.java
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class HelloSpringApp {
	public static void main(String[] args) {
		// load the spring configuration file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve a bean from spring container
        // bean is retrieved in the Object class format , which is generic 
        // the second parameter here helps in casting the object into Coach interface
        // Coach theCoach = (Coach)context.getBean("myCoach"); 
        // this will also work 
		Coach theCoach = context.getBean("myCoach",Coach.class);
		
		// call methods on a bean
		System.out.println(theCoach.getDailyWorkout());
		
		// close the context
		context.close();
	}
}

```
