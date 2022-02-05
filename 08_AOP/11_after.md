## After advice type
- Runs after successful(i.e without any exception) execution of some TargetObj. 
- @AfterReturning 

## Popular Usecases
- Most Common
    - logging, security, transaction
- Audit Logging 
    - who, what, when, where
- Post Processing Data 
 - Post process data before returning to the caller 
 - Format the data or enrich the data ( really cool but be careful )

## Example 
- Create an advice that will run after a method call (success execution)
```


[Logging Aspect]     Target Object 
@AfterReturning      AccountDAO
<--------------------List<Account> findAccounts()
```

## Format is similar to what we have seen earlier
```Java
@AfterReturning("execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))")
public void afterReturningFindAccountAdvice(){
    System.out.println("Executing @AfterReturning advice");
}
```

## Accessing the Return Value
```Java
@AfterReturning(
    pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
    returning="result")
    /*
        ^
        |                      (Has to be Same)
        +---------------------------------------------------------------------------+
                                                                                    |
                                                                                    v
    */
public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, List<Account> result){

    // print out the results of the method call 
    System.out.println("\n=====>>> result is: "+result);
}
```

## Developement Process - `@AfterReturning`
- Prep Work: Add constructor to Account class 
- Add new method: findAccounts() in AccountDAO
- Update main app to call the new method: findAccounts()
- Add @AfterReturning advice. 

## Implementation Result: 
```
src
└── com
    └── luv2code
        └── aopdemo
            ├── Account.java
            ├── AfterReturningDemoApp.java
            ├── DemoConfig.java
            ├── MainDemoApp.java
            ├── aspect
            │   ├── LuvAopExpressions.java
            │   ├── MyApiAnalyticsAspect.java
            │   ├── MyCloudLogAsyncAspect.java
            │   └── MyDemoLoggingAspect.java
            └── dao
                ├── AccountDAO.java
                └── MembershipDAO.java
```

**Account.java**
```Java
package com.luv2code.aopdemo;

public class Account {
	private String 	name; 
	private String level;
	
	public Account() {}
	
	public Account(String name, String level) {
		this.name = name;
		this.level = level;
	}

	public String getName() {
		return name;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	
	public String getLevel() {
		return level;
	}
	
	public void setLevel(String level) {
		this.level = level;
	}


	@Override
	public String toString() {
		return "Account [name=" + name + ", level=" + level + "]";
	}

}
```

**AfterReturningDemoApp.java**
```Java
	package com.luv2code.aopdemo;

import java.util.List;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.luv2code.aopdemo.dao.AccountDAO;
import com.luv2code.aopdemo.dao.MembershipDAO;

public class AfterReturningDemoApp {

	public static void main(String[] args) {

		// read spring config java class
		AnnotationConfigApplicationContext context =
				new AnnotationConfigApplicationContext(DemoConfig.class);
		
		// get the bean from spring container
		AccountDAO theAccountDAO = context.getBean("accountDAO", AccountDAO.class);
		
		// call method to find the accounts 
		List<Account> theAccounts = theAccountDAO.findAccounts();
		
		// display the accounts 
		System.out.println("\n\nMain Program: AfterReturningDemoApp");
		System.out.println("---");
		System.out.println(theAccounts);
		System.out.println("---\n");
		
		// close the context
		context.close();
	}

}
```

**DemoConfig.java**
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

**MyDemoLoggingAspect.java**
```Java
package com.luv2code.aopdemo.aspect;

import java.util.List;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

import com.luv2code.aopdemo.Account;

@Aspect
@Component
@Order(2)
public class MyDemoLoggingAspect {
	//add a new advice for @AfterReturning 
	@AfterReturning(
			pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
			returning="result"
			)
	public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, List<Account> result) {
		// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		System.out.println("\n=====>>> Executing @AfterReturning on method: "+method);
		
		// print out the results of the method call 
		System.out.println("\n=====>>> Result is : "+result);
	}
	
	
	// They will only be applied which are not getter or setter
	@Before("com.luv2code.aopdemo.aspect.LuvAopExpressions.forDaoPackageNoGetterSetter()")
	public void beforeAddAccountAdvice(JoinPoint theJoinPoint) {
			System.out.println("\n====>>> Executing @Before advice on method");
			
			// display the signature 
			MethodSignature methodSig = (MethodSignature) theJoinPoint.getSignature();
			
			System.out.println("Method: "+methodSig);
			
			// display the method arguments 
			
			// get the args 
			 Object[] args = theJoinPoint.getArgs();
			 
			 // loop thru args 
			   for(Object tempArg : args){
			        System.out.println(tempArg);
			        
			        if(tempArg instanceof Account) {
			        	// downcast and print all account related stuff
			        	Account theAccount = (Account)tempArg; 
			        	System.out.println("Account Name: "+theAccount.getName());
			        	System.out.println("Account Level: "+theAccount.getLevel());
			        	
			        }
			   }
			
	}
}
```
**Output**
```

====>>> Logging to Cloud in async fashion

====>>> Executing @Before advice on method
Method: List com.luv2code.aopdemo.dao.AccountDAO.findAccounts()

====>>> Performing API analytics

=====>>> Executing @AfterReturning on method: AccountDAO.findAccounts()

=====>>> Result is : [Account [name=John, level=Silver], Account [name=Madhu, level=Platinum], Account [name=Luca, level=Gold]]


Main Program: AfterReturningDemoApp
---
[Account [name=John, level=Silver], Account [name=Madhu, level=Platinum], Account [name=Luca, level=Gold]]
---
```
- Other files remain same as Previous code variants 