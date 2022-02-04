## Parameter pattern wildcard 
- **()**: matches a method with no args. 
- **(*)**: matches a method with argument of any type
- **(..)**: matches a method with 0 or more arguments of any type

## Examples 
- Match addAccount methods with no arguments. 
```Java
@Before("execution(* addAccount())")
```

- Match addAccount method with **Account** param
```Java
@Before("execution(* addAccount(com.luv2code.aopdemo.Account))")
```
- Match addAccount method with any number of args. 
```Java
@Before("execution(* addAccount(..))")
```

- Match addAccount method in our DAO package: `com.luv2code.aopdemo.dao`
```Java
@Before("execution(* com.luv2code.aopdemo.dao.*.*(..))")
```
- Second star is for `Class`
- Third star for `Method`

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
                │   └── MyDemoLoggingAspect.java
                └── dao
                    ├── AccountDAO.java
                    └── MembershipDAO.java
```
## Account.java
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

## AccountDAO.java
```Java
package com.luv2code.aopdemo.dao;

import org.springframework.stereotype.Component;

import com.luv2code.aopdemo.Account;

@Component
public class AccountDAO {

	public void addAccount(Account theAccount) {
		
		System.out.println(getClass() + ": DOING MY DB WORK: ADDING AN ACCOUNT");

	}
}
```

## MyDemoLoggingAspect.java
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {

	// this is where we add all of our related advices for logging
	
	// let's start with an @Before advice

	@Before("execution(* add*(com.luv2code.aopdemo.Account))")
	public void beforeAddAccountAdvice() {
		
		System.out.println("\n=====>>> Executing @Before advice on method");
		
	}
}
```

## Output
```
=====>>> Executing @Before advice on method
class com.luv2code.aopdemo.dao.AccountDAO: DOING MY DB WORK: ADDING AN ACCOUNT
class com.luv2code.aopdemo.dao.MembershipDAO: DOING STUFF: ADDING A MEMBERSHIP ACCOUNT
```

---
## Incrementing the number of arguments
* `AccountDAO.java`
```Java
package com.luv2code.aopdemo.dao;

import org.springframework.stereotype.Component;

import com.luv2code.aopdemo.Account;

@Component
public class AccountDAO {

	public void addAccount(Account theAccount, boolean vipFlag) {
		
		System.out.println(getClass() + ": DOING MY DB WORK: ADDING AN ACCOUNT");

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
		
		// call the membership business method
		theMembershipDAO.addSillyMember();

				
		// close the context
		context.close();
	}

}
```

* `Output`
```
class com.luv2code.aopdemo.dao.AccountDAO: DOING MY DB WORK: ADDING AN ACCOUNT
class com.luv2code.aopdemo.dao.MembershipDAO: DOING STUFF: ADDING A MEMBERSHIP ACCOUNT
```

---
* `MyDemoLoggingAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {

	// this is where we add all of our related advices for logging
	
	// let's start with an @Before advice

	@Before("execution(* add*(com.luv2code.aopdemo.Account,..))")
		public void beforeAddAccountAdvice() {
		
		System.out.println("\n=====>>> Executing @Before advice on method");
		
	}
}
```
- Above pointcut matches any method name starts with add, with any return type and having first parameters as Account, later parameters we dont care. 

- `Output`
```
=====>>> Executing @Before advice on method
class com.luv2code.aopdemo.dao.AccountDAO: DOING MY DB WORK: ADDING AN ACCOUNT
class com.luv2code.aopdemo.dao.MembershipDAO: DOING STUFF: ADDING A MEMBERSHIP ACCOUNT
```

---
## Match on ANY parameters 
* `MyDemoLoggingAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {

	// this is where we add all of our related advices for logging
	
	// let's start with an @Before advice

	@Before("execution(* add*(..))")
		public void beforeAddAccountAdvice() {
		
		System.out.println("\n=====>>> Executing @Before advice on method");
		
	}
}
```
--- 
## Match methods in a Package 
```
└── src
    └── com
        └── luv2code
            └── aopdemo
                ├── Account.java
                ├── DemoConfig.java
                ├── MainDemoApp.java
                ├── aspect
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
		
		// call the membership business method
		theMembershipDAO.addSillyMember();
		theMembershipDAO.goToSleep();

				
		// close the context
		context.close();
	}

}

```
- `MyDemoLoggingAspect.java`
```Java
package com.luv2code.aopdemo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class MyDemoLoggingAspect {

	// this is where we add all of our related advices for logging
	
	// let's start with an @Before advice

	@Before("execution(* com.luv2code.aopdemo.dao.*.*(..))")
		public void beforeAddAccountAdvice() {
		
		System.out.println("\n=====>>> Executing @Before advice on method");
		
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