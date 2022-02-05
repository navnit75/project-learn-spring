## Reusing pointcut expressions
- How can we `REUSE` a pointcut expression? 
    - Want to apply to multiple advice. 
    - Possible solution: Copy paste the same pointcut expression every where else
    - But i.e not ideal. 

## Solution
- Create a pointer declaration
```Java
@Pointcut("execution(* com.luv2code.aopdemo.dao.*.*(..))")
private void forDaoPackage() {}
```

- Apply pointcut declaration to advice(s). 
```Java
@Before("forDaoPackage())")
private void beforeAddAccountAdvice() {
    ...
}
```

## Benefits
- Easily reuse pointcut expressions. 
- Update pointcut in one location
- can also share and combine pointcut expressions (coming up later)

## Implementation
- `MyDemoLoggingAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {

	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.*(..))")
	private void forDaoPackage() {}
	
	@Before("forDaoPackage()")
	public void beforeAddAccountAdvice() {
			System.out.println("\n====>>> Executing @Before advice on method");
	}
	
	@Before("forDaoPackage()")
	public void performApiAnalytics() {
			System.out.println("\n====>>> Performing API analytics");
	}
}
```

## Output 
```
====>>> Executing @Before advice on method

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.AccountDAO: DOING MY DB WORK: ADDING AN ACCOUNT

====>>> Executing @Before advice on method

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.AccountDAO: doWork()

====>>> Executing @Before advice on method

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.MembershipDAO: DOING STUFF: ADDING A MEMBERSHIP ACCOUNT

====>>> Executing @Before advice on method

====>>> Performing API analytics
class com.luv2code.aopdemo.dao.MembershipDAO: I am going to sleep now...

```

## Pointcut Combining
- How to apply multiple pointcut expression to single advice? 
- Execute an advice only if certain conditions are met 
- For example : 
    - All methods in a package EXCEPT getter/setter methods. 

## Combining the pointcut using logic operators
- **AND(&&)**
- **OR(||)**
- **NOT(!)**

## Examples 
- Works like an if statement 
- Execution happens only if it evaluates to true
```Java
@Before("expressionOne() && expressionTwo()")
@Before("expressionOne() || expressionTwo()")
@Before("expressionOne() && !expressionTwo()")
```

## Implementing 
- Adding addittional methods in the AccountDAO for the example to be relevant
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

- `MyDemoLoggingAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {

	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.*(..))")
	private void forDaoPackage() {}
	
	// create pointcut for getter methods
	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.get*(..))")
	private void getter() {}
	
	// create pointcut for setter methods
	@Pointcut("execution(* com.luv2code.aopdemo.dao.*.set*(..))")
	private void setter() {}
	
	// create pointcut: include package ... exclude getter/setter
	@Pointcut("forDaoPackage() && !(getter() || setter())")
	private void forDaoPackageNoGetterSetter() {}
	
	// They will only be applied which are not getter or setter
	@Before("forDaoPackageNoGetterSetter()")
	public void beforeAddAccountAdvice() {
			System.out.println("\n====>>> Executing @Before advice on method");
	}
	
	@Before("forDaoPackageNoGetterSetter()")
	public void performApiAnalytics() {
			System.out.println("====>>> Performing API analytics");
	}
	
	
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

- `Output`
```

====>>> Executing @Before advice on method
====>>> Performing API analytics
class com.luv2code.aopdemo.dao.AccountDAO: DOING MY DB WORK: ADDING AN ACCOUNT

====>>> Executing @Before advice on method
====>>> Performing API analytics
class com.luv2code.aopdemo.dao.AccountDAO: doWork()
class com.luv2code.aopdemo.dao.AccountDAO: in setName()
class com.luv2code.aopdemo.dao.AccountDAO: in setServiceCode()
class com.luv2code.aopdemo.dao.AccountDAO: in getName()
class com.luv2code.aopdemo.dao.AccountDAO: in getServiceCode()

====>>> Executing @Before advice on method
====>>> Performing API analytics
class com.luv2code.aopdemo.dao.MembershipDAO: DOING STUFF: ADDING A MEMBERSHIP ACCOUNT

====>>> Executing @Before advice on method
====>>> Performing API analytics
class com.luv2code.aopdemo.dao.MembershipDAO: I am going to sleep now...

```