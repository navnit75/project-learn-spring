## SCOPE OF BEAN 
- Its singleton by default
    - Spring container creates only one instance of the bean, by default. 
    - It is cached in memory 
    - All requests for the bean will return a shared reference to the same bean 
```
Coach theCoach = context.getBean("myCoach",Coach.class);-------+                   +----SPRING-------+
...                                                            |                   |  +------------+ |
...                                                            +--------------------->| TrackCoach | |
...                                                            |                   |  +------------+ |
Coach alphaCoach = context.getBean("myCoach",Coach.class);-----+                   +-----------------+

```
- Basically if we call context.get bean multiple times in the App for same bean id. It will point to the same object. 
- We can explicitly specify the scope of the bean 
```xml
<beans ...>
    <bean id = "myCoach"
          class = "com.luv2code.springdemo.TrackCoach"
          scope = "singleton">
    ...
    </bean>
</beans>
```
## VALUES FOR SCOPE PARAMETER IN XML 
|Scope|Description|
|-----|-----------|
|**singleton**| Creates a single shared instance of the bean. DEFAULT SCOPE|
|**prototype**| Creates a new bean request for each bean request|
|**request**| Scoped to an HTTP web request. Only used for web apps.|
|**session**| Scoped to an HTTP web session. Only used for web apps.|
|**global-session**| Scoped to a global HTTP web session. Only used for web apps|

## "prototype" explanation
- Required xml configuration 
```xml
<beans ...>
    <bean id = "myCoach"
          class = "com.luv2code.springdemo.TrackCoach"
          scope = "prototype">
    ...
    </bean>
</beans>
```
- Object creation
``` 
                                                                              +-----SPRING-------+
                                                                              |  +------------+  |
Coach theCoach = context.getBean("myCoach",Coach.class);------------------------>| TrackCoach |  |
...                                                                           |  +------------+  |
...                                                                           |                  |
...                                                                           |   +------------+ | 
Coach alphaCoach = context.getBean("myCoach",Coach.class);----------------------->| TrackCoach | |
                                                                              |   +------------+ |
                                                                              +------------------+
```

## TESTING THE SINGLETON SCOPE
- Spring Config 
**beanScope-applicationContext.xml**
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
    
    <!-- LOGS -->
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    		<property name="rootLoggerLevel" value="FINE" />
    		<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
    
    
    <!-- OBJECT 1: CLASS RANDOM FORTURE SERVICE -->
    <bean id = "myFortune"
    	  class ="com.luv2code.springdemo.RandomFortuneService" >
    </bean>
    
    
    <!-- OBJECT 2 : CLASS TRACK COACH -->
    <bean id = "myCoach"
    	class = "com.luv2code.springdemo.TrackCoach">
    	
    	<!-- setup the constructor injection -->
    	<constructor-arg ref="myFortune"/>
    </bean>
</beans>
```
**BeanScopeDemoApp.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class BeanScopeDemoApp {

	public static void main(String[] args) {
		// load the spring configuration file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("beanScope-applicationContext2.xml");
		
		// retrieve the first bean from spring container
		Coach theCoach = context.getBean("myCoach",Coach.class);
		
		
		// retrieve the second bean from spring container
		Coach alphaCoach = context.getBean("myCoach",Coach.class);
		
		
		// check if they are the same 
		boolean result = (theCoach == alphaCoach);
		
		// display the result 
		System.out.println("\nPointing to the same object: " + result);
		
		System.out.println("Memory location for theCoach: "+theCoach);
		System.out.println("Memory location for alphaCoach: "+alphaCoach);
		
		// close the context
		context.close();
	}
}
```
**output**
```
Pointing to the same object: true
Memory location for theCoach: com.luv2code.springdemo.TrackCoach@3e27aa33
Memory location for alphaCoach: com.luv2code.springdemo.TrackCoach@3e27aa33
```
## TESTING THE PROTOTYPE SCOPE 
- Spring Config 
**beanScope-applicationContext.xml**
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
    
    <!-- LOGS -->
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    		<property name="rootLoggerLevel" value="FINE" />
    		<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
    
    
    <!-- OBJECT 1: CLASS RANDOM FORTURE SERVICE -->
    <bean id = "myFortune"
    	  class ="com.luv2code.springdemo.RandomFortuneService" >
    </bean>
    
    
    <!-- OBJECT 2 : CLASS TRACK COACH -->
    <bean id = "myCoach"
    	class = "com.luv2code.springdemo.TrackCoach"
    	scope = "prototype">
    	
    	<!-- setup the constructor injection -->
    	<constructor-arg ref="myFortune"/>
    </bean>
</beans>
```
**BeanScopeDemoApp.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class BeanScopeDemoApp {

	public static void main(String[] args) {
		// load the spring configuration file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("beanScope-applicationContext2.xml");
		
		// retrieve the first bean from spring container
		Coach theCoach = context.getBean("myCoach",Coach.class);
		
		
		// retrieve the second bean from spring container
		Coach alphaCoach = context.getBean("myCoach",Coach.class);
		
		
		// check if they are the same 
		boolean result = (theCoach == alphaCoach);
		
		// display the result 
		System.out.println("\nPointing to the same object: " + result);
		
		System.out.println("Memory location for theCoach: "+theCoach);
		System.out.println("Memory location for alphaCoach: "+alphaCoach);
		
		// close the context
		context.close();
	}
}
```
**output**
```
Pointing to the same object: false
Memory location for theCoach: com.luv2code.springdemo.TrackCoach@2e385cce
Memory location for alphaCoach: com.luv2code.springdemo.TrackCoach@2ddc9a9f
```