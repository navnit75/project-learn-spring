## JAVA CONFIGURATION
- Instead of configuring Spring container using XML. 
- Configure the Spring container with Java code. 

## Developement Process
1. Create a Java class and annotate as @Configuration
2. Add component scanning support. @ComponentScan (optional)


`SportConfig.java`
```Java
package com.luv2code.springdemo;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
// This is java configuration
@Configuration
@ComponentScan("com.luv2code.springdemo")
public class SportConfig {
	
}
```
3. Read Spring Java configuration class. 
4. Retrieve bean from Spring container. 
`JavaConfigDemoApp.java`
```Java
package com.luv2code.springdemo;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;


public class JavaConfigDemoApp {

	public static void main(String[] args) {
		// read spring config file 
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext(SportConfig.class);
		
		// get the bean from spring container 
		Coach theCoach = context.getBean("tennisCoach",Coach.class);
		
		// call a method on bean 
		System.out.println(theCoach.getDailyWorkout());
		
		// call method to get the daily fortune
		System.out.println(theCoach.getDailyFortune());
		
		// close the context 
		context.close();
	}

}
```
`OUTPUT`
```
>> TennisCoach:  inside default constructor
>> TennisCoach:  inside of doMyStartupStuff()
Practice your backhand volley
Diligence is the mother of good luck
>> TennisCoach:  inside of doMyCleanStuff()
```

- Things to notice here 
	- XML based configuration line is changed.
	- Even though we have specified the `Beans` using Annotation format they are read. 
	- Later in this note we will also see, new way to specify `Beans` using `@Beans` annotation. 

## LOG SUPPORT
---
- Newer version of Spring doesn't support LOGGING. 
- To make logging possible we use below given addittional code. 

1. Create src/mylogger.properties 
```
root.logger.level=FINE
printed.logger.level=FINE
```
2. Add class `MyLoggerConfig.java` in package `com.luv2code.springdemo`
```Java
package com.luv2code.springdemo;
 
import java.util.logging.ConsoleHandler;
import java.util.logging.Level;
import java.util.logging.Logger;
import java.util.logging.SimpleFormatter;
 
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
 
public class MyLoggerConfig {
 
	public MyLoggerConfig(String rootLoggerLevel, String printedLoggerLevel) {
 
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
3. Edit your configuration file `SportConfig.java`
```Java
package com.luv2code.springdemo;
 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
 
@Configuration
@PropertySource("classpath:mylogger.properties")
public class SportConfig {
 
	@Bean
	public MyLoggerConfig myLoggerConfig(@Value("${root.logger.level}") String rootLoggerLevel,
										 @Value("${printed.logger.level}") String printedLoggerLevel) {
 
		MyLoggerConfig myLoggerConfig = new MyLoggerConfig(rootLoggerLevel, printedLoggerLevel);
 
		return myLoggerConfig;
	}
  
}
```
## Prequisites 
---
`Coach.java`
```Java
package com.luv2code.springdemo;

public interface Coach {
	public String getDailyWorkout();
	
	public String getDailyFortune();
}
``` `FortuneService.java`
```Java
package com.luv2code.springdemo;

public interface FortuneService {
	
	public String getFortune(); 
}
```

`SadFortuneService.java`
```Java
package com.luv2code.springdemo;

public class SadFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		
		return "Today is a sad day";
	}
}
```
`SwimCoach.java`
```Java
package com.luv2code.springdemo;

public class SwimCoach implements Coach {
	
	private FortuneService fortuneService; 
	
	public SwimCoach(FortuneService thefortuneService) {
		fortuneService = thefortuneService; 
	}
	@Override
	public String getDailyWorkout() {
		// TODO Auto-generated method stub
		return "Swim 1000 m as a warmup";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}

}
```
## Defining Bean with Java Code
1. Defining method to expose bean 
2. Inject bean dependencies 

`SportConfig.java`
```Java
package com.luv2code.springdemo;
 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
 
@Configuration
@PropertySource("classpath:mylogger.properties")
public class SportConfig {
    
	// For Logging
	@Bean
	public MyLoggerConfig myLoggerConfig(@Value("${root.logger.level}") String rootLoggerLevel,
										 @Value("${printed.logger.level}") String printedLoggerLevel) {
 
		MyLoggerConfig myLoggerConfig = new MyLoggerConfig(rootLoggerLevel, printedLoggerLevel);
 
		return myLoggerConfig;
	}
 
	// define bean for our sad fortune service
	@Bean
	public FortuneService sadFortuneService() {
		return new SadFortuneService();
	}
 
	// define bean for our swim coach AND inject dependency
	@Bean
	public Coach swimCoach() {
		// Injection of dependency
		SwimCoach mySwimCoach = new SwimCoach(sadFortuneService());
 
		return mySwimCoach;
	}
 
}
```
3. Read Spring Java configuration class 
4. Retrieve bean from Spring container

`JavaConfigDemoApp.java`
```Java
package com.luv2code.springdemo;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class JavaConfigDemoApp {

	public static void main(String[] args) {
		// read spring config file 
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext(SportConfig.class);
		
		// get the bean from spring container 
		Coach theCoach = context.getBean("swimCoach",Coach.class);
		
		// call a method on bean 
		System.out.println(theCoach.getDailyWorkout());
		
		// call method to get the daily fortune
		System.out.println(theCoach.getDailyFortune());
		
		// close the context 
		context.close();
	}
}
```
`OUTPUT`
```
Swim 1000 m as a warmup
Today is a sad day
```

## Behind the Scenes
```Java
@Bean 
  public Coach swimCoach() {   
   SwimCoach mySwimCoach = new SwimCoach();   
   return mySwimCoach; 
  }
```
- At a high-level, Spring creates a bean component manually. By default the scope is singleton. So any request for a "swimCoach" bean, will get the same instance of the bean since singleton is the default scope.
- However, let's break it down line-by-line

`@Bean`

- The @Bean annotation tells Spring that we are creating a bean component manually. We didn't specify a scope so the default scope is singleton

`public Coach swimCoach(){`

- This specifies that the bean will bean id of "swimCoach". 
- The method name determines the bean id. The return type is the Coach interface. 
- This is useful for dependency injection. This can help Spring find any dependencies that implement the Coach interface.
- The @Bean annotation will intercept any requests for "swimCoach" bean. 
- Since we didn't specify a scope, the bean scope is singleton. 
- As a result, it will give the same instance of the bean for any requests.

` SwimCoach mySwimCoach = new SwimCoach();`

- This code will create a new instance of the SwimCoach.

`return mySwimCoach;`

- Now let's step back and look at the method in it's entirety.
```Java
 @Bean 
 public Coach swimCoach() {   
   SwimCoach mySwimCoach = new SwimCoach();   
   return mySwimCoach; 
 }
```
- It is important to note that this method has the @Bean annotation. 
- The annotation will intercept ALL calls to the method "swimCoach()". 
- Since no scope is specified the @Bean annotation uses singleton scope. 
- Behind the scenes, during the @Bean interception, it will check in memory of the Spring container (applicationContext) and see if this given bean has already been created.
- If this is the first time the bean has been created then it will execute the method as normal. 
- It will also register the bean in the application context. 
- So that is knows that the bean has already been created before. 
- Effectively setting a flag.
- The next time this method is called, the @Bean annotation will check in memory of the Spring container (applicationContext) and see if this given bean has already been created. 
- Since the bean has already been created (previous paragraph) then it will immediately return the instance from memory. 
- It will not execute the code inside of the method. Hence this is a singleton bean.
- The code for
```
 SwimCoach mySwimCoach = new SwimCoach(); 
 return mySwimCoach;
```
- is not executed for subsequent requests to the method public Coach swimCoach() . 
- This code is only executed once during the initial bean creation since it is singleton scope.
- That explains how @Bean annotation works for the swimCoach example.
`return new SwimCoach(sadFortuneService())`
- This code creates an instance of SwimCoach. Note the call to the method sadFortuneService(). 
- We are calling the annotated method above. The @Bean will intercept and return a singleton instance of sadFortuneService. 
- The sadFortuneService is then injected into the swim coach instance.
- This is effectively dependency injection. It is accomplished using all Java configuration (no xml).