# Bean Scopes
- Default bean scope is singleton
- Basically all the beans obtained in the main program will point to a single shared object.

## Excplicitly Specify Bean Scope Using Annoatation
```Java
@Component
@Scope("singleton")
public class TennisCoach implements Coach{
    ...
}
```

## Prototype Scope
- Every call to getBean will obtain an object, which will point to different memory location. 
```Java
@Component
@Scope("prototype")
public class TennisCoach implements Coach{
    ...
}
``` 

## Validating

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
**AnnotationBeanScopeDemoApp.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationBeanScopeDemoApp {

	public static void main(String[] args) {
		
		// load spring config file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve bean from spring container
		Coach theCoach = context.getBean("tennisCoach",Coach.class);
		
		Coach alphaCoach = context.getBean("tennisCoach",Coach.class);
		
		// Check if they are the same
		boolean result = (theCoach == alphaCoach);
		
		//print out the results
		System.out.println("Pointing to the same object : " + result);
		System.out.println("Memory Location for theCoach: "+theCoach);
		System.out.println("Memory Location for alphaCoach: "+alphaCoach);
		
		//close the context
		context.close();
	}

}
```
#### Output
```
>> TennisCoach:  inside default constructor
Pointing to the same object : true
Memory Location for theCoach: com.luv2code.springdemo.TennisCoach@51c693d
Memory Location for alphaCoach: com.luv2code.springdemo.TennisCoach@51c693d
```

## Validating Prototype

**TennisCoach.java**
```Java
package com.luv2code.springdemo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
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

**AnnotationBeanScopeDemoApp.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationBeanScopeDemoApp {

	public static void main(String[] args) {
		
		// load spring config file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve bean from spring container
		Coach theCoach = context.getBean("tennisCoach",Coach.class);
		
		Coach alphaCoach = context.getBean("tennisCoach",Coach.class);
		
		// Check if they are the same
		boolean result = (theCoach == alphaCoach);
		
		//print out the results
		System.out.println("Pointing to the same object : " + result);
		System.out.println("Memory Location for theCoach: "+theCoach);
		System.out.println("Memory Location for alphaCoach: "+alphaCoach);
		
		//close the context
		context.close();
	}

}
```
- Output
```
>> TennisCoach:  inside default constructor
>> TennisCoach:  inside default constructor
Pointing to the same object : false
Memory Location for theCoach: com.luv2code.springdemo.TennisCoach@704deff2
Memory Location for alphaCoach: com.luv2code.springdemo.TennisCoach@379614be
```

## BEAN life cycle methods/Hooks
- Bascially adding code for `init() and destroy()`

### Developement process
- Define your methods for init and destroy
- Add annotations: @PostConstruct and @PreDestroy

- Init Method Configuration
```Java
@Component
public class TennisCoach implements Coach{
    @PostContruct
    public void doMyStartupStuff(){
        ...
    }
}
```

- Destroy Method Configuration
```Java
@Component
public class TennisCoach implements Coach{
    @PreDestroy
    public void doMyCleanupStuff(){
        ...
    }
}
```

## Details of method Signatures of `@PostContruct` & `@PreDestroy`
- Access modifier
    - The method can have any access modifier (public, protected, private)
- Return type
    - The method can have any return type. However, "void' is most commonly used. 
    - If you give a return type just note that you will not be able to capture the return value. 
    - As a result, "void" is commonly used.
- Method name
    - The method can have any method name.
- Arguments
    - The method can not accept any arguments. The method should be no-arg.

## In case of Java 9 
- [Add this to your libraries ](https://search.maven.org/remotecontent?filepath=javax/annotation/javax.annotation-api/1.3.2/javax.annotation-api-1.3.2.jar)

## Validating init and destroy
```Java
package com.luv2code.springdemo;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
public class TennisCoach implements Coach {
	
	@Autowired
	@Qualifier("randomFortuneService")
	private FortuneService fortuneService; 
	
	
	public TennisCoach() {
		 System.out.println(">> TennisCoach:  inside default constructor");
	}
	
	// define my init method
	@PostConstruct
	public void doMyStartUpStuff() {
		System.out.println(">> TennisCoach:  inside of doMyStartupStuff()");
	}
	
	// define my destroy method
	@PreDestroy
	public void doMyCleanUpStuff() {
		System.out.println(">> TennisCoach:  inside of doMyCleanStuff()");
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
- Output
```
>> TennisCoach:  inside default constructor
>> TennisCoach:  inside of doMyStartupStuff()
Pointing to the same object : true
Memory Location for theCoach: com.luv2code.springdemo.TennisCoach@5b3f61ff
Memory Location for alphaCoach: com.luv2code.springdemo.TennisCoach@5b3f61ff
>> TennisCoach:  inside of doMyCleanStuff()
```

## IMPORTANT NOTE
- For "prototype" scoped beans, Spring does not call the @PreDestroy method.  Gasp!  

## Practice Code
- File reading in RandomFortuneService

**fortunes.txt**
```
Beware of the wolf in sheep's clothing
Diligence is the mother of good luck
The journey is the reward
```
**RandomFortuneService.java**
```Java
package com.luv2code.springdemo;
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Random;
import java.util.Scanner;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import org.springframework.stereotype.Component;

@Component
public class RandomFortuneService implements FortuneService  {
	
	// create an array of strings
	private String[] data;
	private Scanner sc; 
	private Random myRandom = new Random();
	
	
	@PostConstruct
	private void readDataFromFile() throws FileNotFoundException {
		data = new String[3];
		File file = new File("C:\\Users\\navnit\\Eclipse_Projects\\spring-demo-annotation\\src\\fortunes.txt");
		sc = new Scanner(file);
		int i = 0;
		while(sc.hasNextLine()) {
			data[i++] = sc.nextLine();
		}
		
	
	}
	
	@PreDestroy
	private void closeFile() {
		sc.close();
	}
	
	
	
	
	@Override
	public String getFortune() {
		int index = myRandom.nextInt(data.length);
		return data[index];
	}

}

```

**AnnotationBeanScopeDemo.java**
```Java
package com.luv2code.springdemo;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationBeanScopeDemoApp {

	public static void main(String[] args) {
		
		// load spring config file 
		ClassPathXmlApplicationContext context = 
				new ClassPathXmlApplicationContext("applicationContext.xml");
		
		// retrieve bean from spring container
		Coach theCoach = context.getBean("tennisCoach",Coach.class);
		
		// printing the fortune
		System.out.println(theCoach.getDailyFortune());
		
		//close the context
		context.close();
	}

}
```

