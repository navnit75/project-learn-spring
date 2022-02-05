## Problem
- How to control the order of advices being applied .
- `ORDER IS UNDEFINED`
```
MyLoggingDemoAspect
    - beforeAddAccountAdvice
    - performApiAnalyticsAdvice
    - logToCloudAdvice  
```

## To control order
- `Refactor`: Place advices in separate Aspects 
- Control order on aspect using `@Order` annotation. 
- Gurantee order of when Aspects are applied. 

## Process to be followed
- Refactor: Place advices in separate Aspects. 
```
MyLoggingDemoAspect
    - beforeAddAccountAdvice
    - performApiAnalyticsAdvice
    - logToCloudAdvice
           |
           |
           |
           v
MyLoggingDemoAspect
    - beforeAddAccountAdvice

MyApiAnalyticsAspect
    - performApiAnalyticsAdvice

MyCloudLogAspect
     - logToCloudAdvice
```

- Add @Order annotations to Aspects. 
    - We can control the order on Aspects using the @Order annotation
    - Gurantees order of when Aspects are applied 
    - lower number have higher precedence 

```Java
@Order(1)
public class MyCloudAspect{
    ...
}
```

## Order we want 
- MyCloudLogAspec
- MyLoggingDemoAspect
- MyApiAnalyticsAspect

```
Main -----> AOP ------ @Order(1)---------- @Order(2)-------------- @Order(3)--------------> Target Object
App        Proxy     MyCloudLogAspect    MyLoggingDemoAspect     MyApiAnalyticsAspect
```

## `@Order` Annotation
- Lower numbers have higher precedence 
    - Range: Integer.MIN_VALUE to Integer.MAX_VALUE
    - Negative numbers are allowed
    - Does not have to be consecutive
- FAQ: What if aspects have the exact same @Order annotation? 
```
@Order(1)
public class MyCloudAspect {...}

====================================
@Order(6)                           
public class MyShowAspect {...}    
                                    -------------> The order at this point is UNDEFINED (will still run AFTER MyCloudLogAspect and BEFORE                                              MyLoggingDemoAspect)
@Order(6)
public class MyFunnyAspect {...}
=====================================
@Order(123)
public class MyLoggingDemoAspect {...}
```

## Implementation 
```
src
└── com
    └── luv2code
        └── aopdemo
            ├── Account.java
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
		theAccountDAO.addAccount(myAccount,true);
		theAccountDAO.doWork();
		
		// call the accountDao getter/setter methods
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

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Aspect
@Component
@Order(2)
public class MyDemoLoggingAspect {
	
	// They will only be applied which are not getter or setter
	@Before("com.luv2code.aopdemo.aspect.LuvAopExpressions.forDaoPackageNoGetterSetter()")
	public void beforeAddAccountAdvice() {
			System.out.println("\n====>>> Executing @Before advice on method");
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