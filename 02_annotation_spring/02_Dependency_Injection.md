# Dependency Injection


## Spring Autowiring
- For dependency injection, Spring can use auto wiring. 
- Spring will look for a class that matches that property. 
    - matches by type: class or interface
- Spring will inject it automatically...hence its autowired

## Autowiring Example
- Injecting FortuneService into a Coach implementation. 
- Spring will scan @Components
- Will ask Any one implements FortuneService interface? 
- If so, let's inject them. For example : HappyFortuneService

## Autowiring Injection Type
- Constructor Injection
- Setter Injection
- Field Injection

## Developement Process 
1. Define the dependency interface class. 

**FortuneService.java**
```Java
package com.luv2code.springdemo;
public interface FortuneService {
	public String getFortune(); 
}
```

**HappyFortuneService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

// registered component
@Component
public class HappyFortuneService implements FortuneService {
	@Override
	public String getFortune() {
		return "Today is your lucky day!";
	}
}
```
2. Create a constructor in your class injections. 

**Coach.java**
```Java
package com.luv2code.springdemo;

public interface Coach {
	public String getDailyWorkout();
	
	public String getDailyFortune();
}
```
**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {

	private FortuneService fortuneService; 
	
	@Autowired
	public TennisCoach(FortuneService fortuneService) {
		this.fortuneService = fortuneService; 
	}
	
	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}
}
```
3. Configure the dependency injection with @Autowired annotation. 

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

#### Output
```
Practice your backhand volley
Today is your lucky day!
```
## FAQ : What if there are multiple FortuneService implementation?
- Spring has special support to handle this case. Use the @Qualifier annotation. 

## Spring 4.3
- Commented `@Autowired` also works
```Java
//@Autowired
    public TennisCoach(FortuneService theFortuneService) {
        System.out.println(" theFortuneService " + theFortuneService);
        fortuneService = theFortuneService;
    }
```
- As of Spring Framework 4.3, an `@Autowired` annotation on such a constructor is no longer necessary if the target bean only defines one constructor to begin with. 
- However, if several constructors are available, at least one must be annotated to teach the container which one to use.

## Setter Injection with Annotation and Autowiring
---
### Developement Process
- Create setter method(s) in your class for injections. 
- Configure the dependency injection with `@Autowired` Annotation. 

**HappyFortuneService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

// registered component
@Component
public class HappyFortuneService implements FortuneService {
	@Override
	public String getFortune() {
		return "Today is your lucky day!";
	}
}
```

**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {

	private FortuneService fortuneService; 
		
	public TennisCoach() {
		 System.out.println(">> TennisCoach:  inside default constructor");
	}
	
	@Autowired
	public void setFortuneService(FortuneService fortuneService) {
		System.out.println(">> TennisCoach: inside setFortuneService() function");
		this.fortuneService = fortuneService; 
	}
	
	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}
	

}
```
#### Output
```
>> TennisCoach:  inside default constructor
>> TennisCoach: inside setFortuneService() function
Practice your backhand volley
Today is your lucky day!
```
## Method Dependency Injection
---
**HappyFortuneService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

// registered component
@Component
public class HappyFortuneService implements FortuneService {
	@Override
	public String getFortune() {
		return "Today is your lucky day!";
	}
}
```

**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {

	private FortuneService fortuneService; 
		
	public TennisCoach() {
		 System.out.println(">> TennisCoach:  inside default constructor");
	}
	
	@Autowired
	public void doSomeCrazyStuff(FortuneService fortuneService) {
		System.out.println(">> TennisCoach: inside setFortuneService() function");
		this.fortuneService = fortuneService; 
	}
	
	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}
	

}
```

#### Output
```
>> TennisCoach:  inside default constructor
>> TennisCoach: inside doSomeCrazyStuff() function
Practice your backhand volley
Today is your lucky day!
```


## Field Injection with Annotations and Autowiring
- Inject dependencies by setting field values on your class directly (even private fields)
- Accomplished by Java Reflection

**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
	@Autowired
	private FortuneService fortuneService; 
	
	
	public TennisCoach() {
		 System.out.println(">> TennisCoach:  inside default constructor");
	}
	
	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
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

- Output
```
>> TennisCoach:  inside default constructor
Practice your backhand volley
Today is your lucky day!
```

## So which one to use ? 
- Choose a style and be consistent in your project
- Please consult team also. 

## If we have multiple defination of a Object. 
- Compilation fails with `NoUniqueBeanDefinationException`
- That's where the `@Qualifier` annotation comes into play. 
```
@Autowired
@Qualifier("happyFortuneService")
```
- Here we are using default bean id.
- We can apply `@Qualifier` annotation to 
    - Construction Injection
    - Setter injection Method
    - Field Injection

## Execution with Qualifier

**RandomService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

@Component
public class RandomService implements FortuneService {

	@Override
	public String getFortune() {
		return null;
	}

}
```

**DatabaseFortuneService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

@Component
public class DatabaseFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		return null;
	}

}
```

**RESTFortuneService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

@Component
public class RESTFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		return null;
	}
}

```
**HappyFortuneService.java**
```Java
package com.luv2code.springdemo;
import org.springframework.stereotype.Component;

// registered component
@Component
public class HappyFortuneService implements FortuneService {

	@Override
	public String getFortune() {
		return "Today is your lucky day!";
	}
}
```

**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
    // Here it tells to use the happyFortuneService --> i.e default bean id of Class HappyFortuneService
	@Autowired
	@Qualifier("happyFortuneService")
	private FortuneService fortuneService; 
	
	
	public TennisCoach() {
		 System.out.println(">> TennisCoach:  inside default constructor");
	}
	
	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}
}
```
#### Output: 
```
>> TennisCoach:  inside default constructor
Practice your backhand volley
Today is your lucky day!
```

## Using other implementation of FortuneService

**RandomFortuneService.java**
```Java
package com.luv2code.springdemo;
import java.util.Random;

import org.springframework.stereotype.Component;

@Component
public class RandomFortuneService implements FortuneService {
	
	// create an array of strings
	private String[] data = {
			"Beware of the wolf in sheep's clothing",
			"Diligence is the mother of good luck",
			"The journey is the reward"
	};
	
	private Random myRandom = new Random();
	
	@Override
	public String getFortune() {
		int index = myRandom.nextInt(data.length);
		return data[index];
	}

}
```

**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
	@Autowired
	@Qualifier("randomFortuneService")
	private FortuneService fortuneService; 
	
	
	public TennisCoach() {
		 System.out.println(">> TennisCoach:  inside default constructor");
	}
	
	@Override
	public String getDailyWorkout() {
		return "Practice your backhand volley";
	}

	@Override
	public String getDailyFortune() {
		// TODO Auto-generated method stub
		return fortuneService.getFortune();
	}
}
```


**AnnotationDemo.java**
```Java
package com.luv2code.springdemo;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationDemoApp {

	public static void main(String[] args) {
		// read spring config file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
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
#### Output
```
>> TennisCoach:  inside default constructor
Practice your backhand volley
Beware of the wolf in sheep's clothing
```

## Default Bean Names - The Special Case 
- However, for the special case of when BOTH the first and second characters of the class name are upper case, then the name is NOT converted.
- Ex. RESTFortuneService
- Reason being Java uses JavaBeansIntrospector to generate default bean name. 
- So in this case solution is to used specific bean id by using 
```Java
@Component("foo")
public class RESTFortuneService{
    ...
}
```

## Using `@Qualifier` with Constructors
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {

    private FortuneService fortuneService;

    // define a default constructor
    public TennisCoach() {
        System.out.println(">> TennisCoach: inside default constructor");
    }
    
    // The absurd @Qualifier syntax for the Constructors 
    @Autowired
    public TennisCoach(@Qualifier("randomFortuneService") FortuneService theFortuneService) {

        System.out.println(">> TennisCoach: inside constructor using @autowired and @qualifier");
        
        fortuneService = theFortuneService;
    }
       
    
       
    @Override
    public String getDailyWorkout() {
        return "Practice your backhand volley";
    }

    @Override
    public String getDailyFortune() {
        return fortuneService.getFortune();
    }

}
```

### `@Qualifier` with setter injection
```Java
@Autowired
	public void setFortuneService(@Qualifier("randomFortuneService") FortuneService theFortuneService) {
		System.out.println(">> TennisCoach: inside setFortuneService() method");
		this.fortuneService = theFortuneService;
	}
```

### `@Qualifier` with method injection
```Java
    @Autowired
	@Qualifier("randomFortuneService")
	public void setFortuneService(FortuneService theFortuneService) {
		System.out.println(">> TennisCoach: inside setFortuneService() method");
		this.fortuneService = theFortuneService;
	}
```

## Injecting properties using Java Annotation
1. Create a properties file to hold your properties. It will be a name value pair.  
    - New text file:  src/sport.properties
    - Note the location of the properties file is very important. It must be stored in src/sport.properties
```
foo.email=myeasycoach@luv2code.com
foo.team=Silly Java Coders
```
2. Load the properties file in the XML config file.

**File: applicationContext.xml**
- Add the following lines:
```xml
    <context:property-placeholder location="classpath:sport.properties"/>  
```
-  This should appear just after the <context:component-scan .../> line

3. Inject the properties values into your Swim Coach: SwimCoach.java
```Java
    @Value("${foo.email}")
    private String email;
    
    @Value("${foo.team}")
    private String team;
```