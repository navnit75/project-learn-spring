## Problem
- When we are in the aspect (i.e for logging)
- How can we access the method parameter

## Developement process
- Access and display method signatures
```Java
@Before("...")
public void beforeAddAccountAdvice(JoinPoint theJoinPoint){
    // display the method signature 
    MethodSignature methodSig = (MethodSignature)theJoinPoint.getSignature(); 
    System.out.println("Method:"+methodSig);
}
```
- Access and display method parameters
```Java
@Before("...")
public void beforeAddAccountAdvice(JoinPoint theJoinPoint){
    // display method arguments

    // get args 
    Object[] args = theJoinPoint.getArgs(); 

    // loop thru args 
    for(Object tempArg : args){
        System.out.println(tempArg);
    }
}
```
## Implementation 

- `Account.java`

```Java
package com.luv2code.aopdemo;

public class Account {
	private String 	name; 
	private String level;
	
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
	
	
}
```
- `DemoConfig.java`
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
- `MainDemoApp.java`
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
		Account myAccount = new Account();
		myAccount.setName("Madhu");
		myAccount.setLevel("Platinum");
		
		// call the accountDao getter/setter methods
		theAccountDAO.addAccount(myAccount,true);
		theAccountDAO.doWork();
		
		theAccountDAO.setName("foobar");
		theAccountDAO.setServiceCode("silver");
		
		
		String name = theAccountDAO.getName();
		String code = theAccountDAO.getServiceCode();
		
		
		// call the membership business method
		theMembershipDAO.addSillyMember();
		theMembershipDAO.goToSleep();

				
		// close the context
		context.close();
	}

}
```
- `LuvAopExpressions.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public class LuvAopExpressions {
	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.*(..))")
	public void forDaoPackage() {}
	
	// create pointcut for getter methods
	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.get*(..))")
	public void getter() {}
	
	// create pointcut for setter methods
	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.set*(..))")
	public void setter() {}
	
	// create pointcut: include package ... exclude getter/setter
	@Pointcut("forDaoPackage() && !(getter() || setter())")
	public void forDaoPackageNoGetterSetter() {}
}
```
- `MyApiAnalyticsAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Aspect
@Component
@Order(3)
public class MyApiAnalyticsAspect {
	
	@Before("com.luv2code.aopdemo.aspect.LuvAopExpressions.forDaoPackageNoGetterSetter()")
	public void performApiAnalytics() {
			System.out.println("====>>> Performing API analytics");
	}
}
```
- `MyCloudLogAsyncAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Aspect
@Component
@Order(1)
public class MyCloudLogAsyncAspect {
	
	// They will only be applied which are not getter or setter
	@Before("com.luv2code.aopdemo.aspect.LuvAopExpressions.forDaoPackageNoGetterSetter()")
	public void logToCloudAsync() {
			System.out.println("\n====>>> Logging to Cloud in async fashion");
	}
}
```
- `MyDemoLoggingAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.JoinPoint;
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
- `AccountDAO.java`
```Java
package com.luv2code.aopdemo.dao;

import org.springframework.stereotype.Component;

import com.luv2code.aopdemo.Account;

@Component
public class AccountDAO {
	
	private String name; 
	private String serviceCode; 
	
	public String getName() {
		System.out.println(getClass() + ": in getName()");
		return name;
	}

	public void setName(String name) {
		System.out.println(getClass() + ": in setName()");
		this.name = name;
	}

	public String getServiceCode() {
		System.out.println(getClass() + ": in getServiceCode()");
		return serviceCode;
	}

	public void setServiceCode(String serviceCode) {
		System.out.println(getClass() + ": in setServiceCode()");
		this.serviceCode = serviceCode;
	}
	
	public void addAccount(Account theAccount, boolean vipFlag) {
			System.out.println(getClass() + ": DOING MY DB WORK: ADDING AN ACCOUNT");
	}
	
	public boolean doWork() {
		System.out.println(getClass() + ": doWork()");
		return false; 
	}
}
```
- `MembershipDAO.java`
```Java
package com.luv2code.aopdemo.dao;

import org.springframework.stereotype.Component;

@Component
public class MembershipDAO {

	// adding the return type
	public boolean addSillyMember() {
			
			System.out.println(getClass() + ": DOING STUFF: ADDING A MEMBERSHIP ACCOUNT");
			return true; 
	}
	
	public void goToSleep() {
		System.out.println(getClass() + ": I am going to sleep now...");
		
	}
}
```

- `Output`
```
====>>> Logging to Cloud in async fashion

====>>> Executing @Before advice on method
Method: void com.luv2code.aopdemo.dao.AccountDAO.addAccount(Account,boolean)
com.luv2code.aopdemo.Account@3da30852
Account Name: Madhu
Account Level: Platinum
true

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.AccountDAO: DOING MY DB WORK: ADDING AN ACCOUNT

====>>> Logging to Cloud in async fashion

====>>> Executing @Before advice on method
Method: boolean com.luv2code.aopdemo.dao.AccountDAO.doWork()

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.AccountDAO: doWork()
class com.luv2code.aopdemo.dao.AccountDAO: in setName()
class com.luv2code.aopdemo.dao.AccountDAO: in setServiceCode()
class com.luv2code.aopdemo.dao.AccountDAO: in getName()
class com.luv2code.aopdemo.dao.AccountDAO: in getServiceCode()

====>>> Logging to Cloud in async fashion

====>>> Executing @Before advice on method
Method: boolean com.luv2code.aopdemo.dao.MembershipDAO.addSillyMember()

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.MembershipDAO: DOING STUFF: ADDING A MEMBERSHIP ACCOUNT

====>>> Logging to Cloud in async fashion

====>>> Executing @Before advice on method
Method: void com.luv2code.aopdemo.dao.MembershipDAO.goToSleep()

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.MembershipDAO: I am going to sleep now...
```