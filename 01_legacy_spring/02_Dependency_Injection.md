# DEPENDENCY INJECTION

- Allows a program design to follow the dependency inversion principle.
- The Client delegates  to calls to another object the responsibility of providing its dependencies. 
- Dependency is same as helper function 
- Example : 
```
                        1                          2
+--------+     Give me CAR object          +---------------+
|  ME    |-------------------------------->|  Car Factory  |
+--------+                                 +---------------+    
                                                    |
                                                    |
                                                    |
                                                    V
                                                Car engine,
                                                Car exhaust, 
                                                     3

```
- Our tool 
``` 
                                                    Spring
+----------------+  give me a "Coach" object   +-------------------+
|   My App       |---------------------------->| Object Factory    |
|                |<----------------------------|                   |
+----------------+                             +-------------------+
                                                        |
                                                        |
                                                   Configuration 
                                                   /    |    \
                                                  /     |     \
                                                 /      |      \
                                            Baseball  Hockey  Cricket
                                             Coach     Coach   Coach
                                                   (dependecies)
                                                   <Helper Objects>
```
- Our coach already provides daily workouts
- Now will also provide daily fortunes 
    - New helper : FortuneService
    - This is a dependency
```
Coach --------> FortuneService
```
## Types of Injection
- Constructor Injection
- Setter Injection
- Auto Wiring in Annotation section. 

## Constructor Injection
---
## Behind the scenes
```Java
HappyFortuneService myFortuneService = new HappyFortuneService(); 
BaseballCoach myCoach = new BaseballCoach(myFortuneService);
```
### Developement Process
- Define the Dependency interface 
- FortuneService.java
```Java
package com.luv2code.springdemo;
public interface FortuneService {
	public String getFortune();
}

```
- Define the Dependecy Class
- HappyFortuneService.java
```Java
package com.luv2code.springdemo;
public class HappyFortuneService implements FortuneService {
	@Override
	public String getFortune() {
		// TODO Auto-generated method stub
		return "Today is your lucky day!";
	}

}

```
- Other Changes 
- Coach.java
```Java
package com.luv2code.springdemo;
public interface Coach {
	// only a specification
	public String getDailyWorkout();
	public String getDailyFortune();
}
```
- TrackCoach.java
```Java
package com.luv2code.springdemo;

public class TrackCoach implements Coach {
	
	// define a private field for dependency
	private FortuneService fortuneService; 
	
	// define a default constructor
	public TrackCoach() {}
	
	// define a parameterized constructor
	public TrackCoach(FortuneService fortuneService) {
		this.fortuneService = fortuneService;
	}

	@Override
	public String getDailyWorkout() {
		// TODO Auto-generated method stub
		return "Run a hard 5k";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method	 stub
		return "Just Do it: " + fortuneService.getFortune();
	}

}
```

- Create the constructor in your class for injection 
- Configure the dependency injection in Spring config file. 
    - To get the proper name of the class, SELECT the classname. 
    - Right Click ---> Choose --> Copy qualified Name


## SETTER INJECTION
---
### DEVELOPEMENT PROCESS
1. Create Setter method(s) in your class for injections

```Java
package com.luv2code.springdemo;
public class CricketCoach implements Coach {
	private FortuneService fortuneService; 
	
	// create a no arg constructor
	public CricketCoach() {
		System.out.println("Cricket Coach : Inside no arg constructor");
	}
	
	// our setter metthod 
	public void setFortuneService(FortuneService fortuneService) {
		System.out.println("Cricket Coach : inside setter method - setFortuneService");
		this.fortuneService = fortuneService;
	}

	@Override
	public String getDailyWorkout() {
		
		return "Practice fast bowling 15 minutes";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}
}
```
2. Configure the dependency injection in the config class. 
```xml
<property name="fortuneService" ref="myFortune"/>
                |
                |
                V
public void setFortuneService(FortuneService fortuneService){...}
```
- Capitalizes the first letter of property name
- Final XML 
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
    
    
    <!-- Intitalization of beans for logs -->
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    		<property name="rootLoggerLevel" value="FINE" />
    		<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
    
    
    <!-- Define a bean for Dependecy First then the Class -->
    <bean id = "myFortune"
    	  class ="com.luv2code.springdemo.HappyFortuneService" >
    </bean>
    
    
    <!-- Class contains the name of fully quantified class name of implemented class -->
    <bean id = "myCoach"
    	class = "com.luv2code.springdemo.TrackCoach">
    	
    	<!-- setup the constructor injection -->
    	<constructor-arg ref="myFortune"/>
    </bean>
    
    <bean id = "myCricketCoach" 
    	  class = "com.luv2code.springdemo.CricketCoach"> 
    	<property name = "fortuneService" ref = "myFortune"/>
    </bean>

</beans>
```
- Setter application demo 
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SetterDemoApp {

	public static void main(String[] args) {
		// load the spring configuration file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve the bean from spring container
		CricketCoach theCoach = context.getBean("myCricketCoach",CricketCoach.class);
		
		// call methods on bean 
		System.out.println(theCoach.getDailyWorkout());
		System.out.println(theCoach.getDailyFortune());
		
		// close the context
		context.close();
	}

}
```
- OUTPUT: 
```
Cricket Coach : Inside no arg constructor
Cricket Coach : inside setter method - setFortuneService
Practice fast bowling 15 minutes
Today is your lucky day!
```
## SETTING THE LITERAL VALUE
---
```xml
<property name = "emailAddress" value="thebestcoach@luv2code.com" />
                        |
                        V
           will look for function name setEmailAddress()

<property name = "team" value= "Sunrisers Hyderabad" />
                        |
                        V
        will look for function name setTeam(String name)
```
#### CricketCoach.java
```Java
package com.luv2code.springdemo;

public class CricketCoach implements Coach {
	private FortuneService fortuneService; 
	
	// add new fields for email address and team 
	private String emailAddress; 
	private String team; 
	


	// create a no arg constructor
	public CricketCoach() {
		System.out.println("Cricket Coach : Inside no arg constructor");
	}
	
	// our setter method 
	public void setFortuneService(FortuneService fortuneService) {
		System.out.println("Cricket Coach : inside setter method - setFortuneService");
		this.fortuneService = fortuneService;
	}
	// setters for the email and team 
	public void setEmailAddress(String emailAddress) {
		System.out.println("Cricket Coach : inside setter method - setEmailAddress");
		this.emailAddress = emailAddress;
	}

	public void setTeam(String team) {
		System.out.println("Cricket Coach : inside setter method - setTeam");
		this.team = team;
	}

	public String getEmailAddress() {
		return emailAddress;
	}

	public String getTeam() {
		return team;
	}

	@Override
	public String getDailyWorkout() {
		
		return "Practice fast bowling 15 minutes";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}

}

```
#### SetterDemoApp.java
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class SetterDemoApp {

	public static void main(String[] args) {
		// load the spring configuration file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve the bean from spring container
		
		CricketCoach theCoach = context.getBean("myCricketCoach",CricketCoach.class);
		
		// call methods on bean 
		System.out.println(theCoach.getDailyWorkout());
		System.out.println(theCoach.getDailyFortune());
		
		// calling our new methods to get literal value
		System.out.println(theCoach.getEmailAddress());
		System.out.println(theCoach.getTeam());
		
		// close the context
		context.close();
	}

}
```
#### applicationContext.xml
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
    
    
    <!-- Intitalization of beans for logs -->
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    		<property name="rootLoggerLevel" value="FINE" />
    		<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
    
    
    <!-- Define a bean for Dependecy First then the Class -->
    <bean id = "myFortune"
    	  class ="com.luv2code.springdemo.HappyFortuneService" >
    </bean>
    
    
    <!-- Class contains the name of fully quantified class name of implemented class -->
    <bean id = "myCoach"
    	class = "com.luv2code.springdemo.TrackCoach">
    	
    	<!-- setup the constructor injection -->
    	<constructor-arg ref="myFortune"/>
    </bean>
    
    <bean id = "myCricketCoach" 
    	  class = "com.luv2code.springdemo.CricketCoach"> 
    	<!--  setup setter injection -->
    	<property name = "fortuneService" ref = "myFortune"/>
    	
    	<!-- inject literal value -->
    	<property name = "emailAddress" value = "kumar.navnit4175@gmail.com"/>
    	<property name= "team" value = "Chennai Superkings" />
    </bean>

</beans>
```

## INJECTING VALUES FROM PROPERTIES FILE
---
### Developement Process
1. Create properties file
    - Add in src, create a new file with the format
```
<name>.properties
sport.properties
```

- In our case
```
foo.email=kumar.navnit4175@gmail.com
foo.team=Mumbai Indians
```
2. Load properties file in spring config file 
```xml
    <!-- load the properties file sport.properties -->
	<context:property-placeholder location = "classpath:sport.properties" />
```
#### applicationContext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!-- load the properties file sport.properties -->
	<context:property-placeholder location = "classpath:sport.properties" />
    
    
    <!-- Define your beans here -->
    
    <!-- Intitalization of beans for logs -->
    <bean id="myLoggerConfig" class="com.luv2code.springdemo.MyLoggerConfig" init-method="initLogger">
    		<property name="rootLoggerLevel" value="FINE" />
    		<property name="printedLoggerLevel" value="FINE"/>
    </bean> 
    
    
    <!-- Define a bean for Dependecy First then the Class -->
    <bean id = "myFortune"
    	  class ="com.luv2code.springdemo.HappyFortuneService" >
    </bean>
    
    
    <!-- Class contains the name of fully quantified class name of implemented class -->
    <bean id = "myCoach"
    	class = "com.luv2code.springdemo.TrackCoach">
    	
    	<!-- setup the constructor injection -->
    	<constructor-arg ref="myFortune"/>
    </bean>
    
    <bean id = "myCricketCoach" 
    	  class = "com.luv2code.springdemo.CricketCoach"> 
    	<!--  setup setter injection -->
    	<property name = "fortuneService" ref = "myFortune"/>
    	
    	<!-- inject literal value -->
    	<property name = "emailAddress" value = "${foo.email}"/>
    	<property name= "team" value = "${foo.team}" />
    </bean>

</beans>
```
3. reference value from Properties file 
```xml
        <!-- inject literal value -->
    	<property name = "emailAddress" value = "${foo.email}"/>
    	<property name= "team" value = "${foo.team}" />
```