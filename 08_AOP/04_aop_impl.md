## Code
```
───src
    └───com
        └───luv2code
            └───aopdemo
                │   DemoConfig.java
                │   MainDemoApp.java
                │
                ├───aspect
                │       MyDemoLoggingAspect.java
                │
                └───dao
                        AccountDAO.java
                        

```

## DemoConfig.java
```Java
package com.luv2code.aopdemo;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@EnableAspectJAutoProxy
@ComponentScan("com.luv2code.aopdemo")
public class DemoConfig {

}
```

## MainDemoApp.java
```Java
package com.luv2code.aopdemo;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.luv2code.aopdemo.dao.AccountDAO;
import com.luv2code.aopdemo.dao.MembershipDAO;

public class MainDemoApp {

	public static void main(String[] args) {

		// read spring config java class
		AnnotationConfigApplicationContext context =
				new AnnotationConfigApplicationContext(DemoConfig.class);
		
		// get the bean from spring container
		AccountDAO theAccountDAO = context.getBean("accountDAO", AccountDAO.class);
		
		// get the bean from spring container
		MembershipDAO theMembershipDAO = context.getBean("membershipDAO",MembershipDAO.class);
		
		// call the business method
		theAccountDAO.addAccount();
		
		
		
				
		// close the context
		context.close();
	}

}

```

## MainDemoLoggingAspect.java
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {
	
	// this is where we add of our related advices for logging
	// lets start with an @Before advice 
	
	@Before("execution(public void addAccount())")
	public void beforeAddAccountAdvice() {
		System.out.println("\n======>>>> Executing @Before advice on method");
	}
	
}
```

## AccountDAO.java
```Java
package com.luv2code.aopdemo.dao;

import org.springframework.stereotype.Component;

@Component
public class AccountDAO {

	public void addAccount() {
		
		System.out.println(getClass() + ": DOING MY DB WORK: ADDING AN ACCOUNT");
		
	}
}
```