# BEAN LIFE CYCLE
```
                      +----------------+       +------------------+      +---------------------+     +---------------------+
    Container-------> |     Bean       | ----->|  Dependencies    |----->|   Internal Spring   |---->|  Your Custome Init  |
     Started          |  Instantiated  |       |    injection     |      |      Processing     |     |        method       |
                      +----------------+       +------------------+      +---------------------+     +---------------------+
                                                                                                                |
                                                                                                                |
                                                                                                                |
                                                                                                                V
                                                                                                        Bean is ready for use
                                                                                                    -----------------------------
                                                                                                        Container is shutdown
                                                                                                                |
                                                                                                                |
                                                                                                                V
                                                                                                        +-----------------+
                                                                                                        | Custom Destroy  |----> STOP 
                                                                                                        |     Method      |
                                                                                                        +-----------------+
```
## LIFECYCLE METHODS/ HOOKS
- Add your own custom code during intitalization. 
    - Calling custom business logic. 
    - Setting up handles to resources(db, sockets, file etc)
- Add your own code during bean destruction
    - Calling custom business logic method
    - Clean up handles to resources(db, sockets,file etc)

## Init Method Call 
**Configuration Based Changes**
```xml
    <beans ...>
    <bean id = "myCoach"
          class = "com.luv2code.springdemo.TrackCoach"
          init-method = "doMyStartupStuff">
         
    ...
    </bean>
</beans>
```

## Destroy Method Call
```xml
    <beans ...>
    <bean id = "myCoach"
          class = "com.luv2code.springdemo.TrackCoach"
          init-method = "doMyStartUpStuff"
          destroy-method = "doMyCleanupStuff">
         
    ...
    </bean>
</beans>
```

## Properties of Above Method
- Access modifier
    - The method can have any access modifier (public, protected, private)
- Return type
    - The method can have any return type. 
    - However, "void' is most commonly used.
    - If you give a return type just note that you will not be able to capture the return value. As a result, "void" is commonly used.
- Method name
    - The method can have any method name.
- Arguments
    - The method can not accept any arguments. The method should be no-arg.

## Validating Above Methods
**beanLifeCycle-applicationContext.xml**
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
    	init-method="doMyStartupStuff"
    	destroy-method="doMyCleanupStuff"
    	>
    	
    	<!-- setup the constructor injection -->
    	<constructor-arg ref="myFortune"/>
    </bean>
    
   

</beans>

```
**BeanLifeCycleDemoApp.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class BeanLifeCycleDemoApp {
	public static void main(String[] args) {
		
        // load the spring configuration file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("beanLifeCycle-applicationContext.xml");
		
		// retrieve the first bean from spring container
		Coach theCoach = context.getBean("myCoach",Coach.class);
				System.out.println(theCoach.getDailyWorkout());
		context.close();
	}
}
```

## Points to Keep in Mind
- For "prototype" scoped beans, Spring does not call the destroy method.

- **In contrast to the other scopes, Spring does not manage the complete lifecycle of a prototype bean**: the container instantiates, configures, and otherwise assembles a prototype object, and hands it to the client, with no further record of that prototype instance.

- Thus, although initialization lifecycle callback methods are called on all objects regardless of scope, **in the case of prototypes, configured destruction lifecycle callbacks are not called**. The client code must clean up prototype-scoped objects and release expensive resources that the prototype bean(s) are holding.
