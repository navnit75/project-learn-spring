## Injecting Values from Properties file 
1. Create properties file

`src/sport.properties`
```
foo.email=kumar.navnit4175@gmail.com
foo.team=Awesome Java Coders
```
2. Load properties file in spring config. Using `@PropertySource("classpath:sport.properties)`

`SportConfig.java`
```Java
package com.luv2code.springdemo;
 
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
 
@Configuration
@PropertySource("classpath:mylogger.properties")
@PropertySource("classpath:sport.properties")
public class SportConfig {
 
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
		SwimCoach mySwimCoach = new SwimCoach(sadFortuneService());
 
		return mySwimCoach;
	}
 
}
```


3. Reference values from Properties file. Using `@Value("{foo.email}")`

`SwimCoach.java`
```Java
package com.luv2code.springdemo;

import org.springframework.beans.factory.annotation.Value;

public class SwimCoach implements Coach {
	
	private FortuneService fortuneService; 
	
	@Value("${foo.email}")
	private String email; 
	
	@Value("${foo.team}")
	private String team; 
	
	
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
	
	public String getEmail() {
		return email;
	}
	public String getTeam() {
		return team;
	}

}

```

4. Running the main file 

`SwimCoachConfigDemoApp.java`
```Java
package com.luv2code.springdemo;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class JavaConfigDemoApp {

	public static void main(String[] args) {
		// read spring config file 
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext(SportConfig.class);
		
		// get the bean from spring container 
		SwimCoach theCoach = context.getBean("swimCoach",SwimCoach.class);
		
		// call a method on bean 
		System.out.println(theCoach.getDailyWorkout());
		
		// call method to get the daily fortune
		System.out.println(theCoach.getDailyFortune());
		
		// call our new swim coach methods ... has the props value injected 
		System.out.println("email :  " + theCoach.getEmail());
		System.out.println("team :  " + theCoach.getTeam());
		
		// close the context 
		context.close();
	}
}

```

5. `OUTPUT`
```
Swim 1000 m as a warmup
Today is a sad day
email :  kumar.navnit4175@gmail.com
team :  Awesome Java Coders
```

6. There could also be another variation for injecting values directly, instead of properties file. 

`SwimCoach.java`
```Java
package com.luv2code.springdemo;

import org.springframework.beans.factory.annotation.Value;

public class SwimCoach implements Coach {
	
	private FortuneService fortuneService; 
	
	@Value("kumar.navnit4175@gmail.com")
	private String email; 
	
	@Value("CSK")
	private String team; 
	
	
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
	
	public String getEmail() {
		return email;
	}
	public String getTeam() {
		return team;
	}

}
```

`OUTPUT`
```
Swim 1000 m as a warmup
Today is a sad day
email :  kumar.navnit4175@gmail.com
team :  CSK
```