## Introduction
- Special labels/markers added to Java classes. 
- Provide Meta-data about the class. 
- Processed at the run-time for special processing. 

```
Boot 
Color : Silver
Style : Jewel 
Code : 1460
SKU : 10072090
Size US: 8
Size UK: 6
```
- When we type `@Override` it means we are telling the compiler we are overriding a method. 
- At compilation time compiler will check/verify the override. 


## Why Spring configuration with Annotations? 
- XML configuration can be verbose ( lot of work )
- Configure your Spring beans with Annotation. 
- Annotation minmizes the XML configuration. 
- Spring will scan your Java classes for special annotations. 
- Automatically register the beans in the Spring Container. 

## Developement Process
- Add the component scanning in Spring config file. 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ....>
    
    <!--  add entry for context scanning -->
    <context:component-scan base-package="com.luv2code.springdemo" />
</beans>
```
- Add the `@Component` Annotation to your Java class. 
**TennisCoach.java**
```Java
package com.luv2code.springdemo;

import org.springframework.stereotype.Component;

@Component("thatSillyCoach")
public class TennisCoach implements Coach {

	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

}
```

- Retrieve the bean from Spring container. 
**AnnotationDemoApp.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationDemoApp {

	public static void main(String[] args) {
		
        // read spring config file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// get the bean from spring container 
		Coach theCoach = context.getBean("thatSillyCoach",Coach.class);
		
		// call a method on bean 
		System.out.println(theCoach.getDailyWorkout());
		
		// close the context 
		context.close();
	}
}
```

## Spring also supports Default Bean IDs
- Default bean id: The class name, make first letter lower case
```
    Class Name               DefaultBeanId
+----------------+          +---------------+
|  Tennis Coach  |--------->|  tennisCoach  |
+----------------+          +---------------+
```

## Validating 
**annotationContext.xml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
     
     <!-- Intitalization of beans for logs -->
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    		<property name="rootLoggerLevel" value="FINE" />
    		<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
    
    <!--  add entry for context scanning -->
    <context:component-scan base-package="com.luv2code.springdemo" />
</beans>
```

**TennisCoach.java**
```Java
package com.luv2code.springdemo;

import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {

	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

}
```

**AnnotationDemoApp.java**
```Java
package com.luv2code.springdemo;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationDemoApp {

	public static void main(String[] args) {
		// read spring config file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// using the default bean id "tennisCoach"
		Coach theCoach = context.getBean("tennisCoach",Coach.class);
		
		// call a method on bean 
		System.out.println(theCoach.getDailyWorkout());
		
		// close the context 
		context.close();
	}

}
```