## Intro
- For an exception thrown from proceeding join point. 
    - You can handle / swallow / stop the exception. 
    - Or you can simply rethrow the exception. 
- This gives you fine grained control over how the target method is called. 

## SDD
![](./Around_advice.png)
- Main never know exception happened.

## Implementation 
```Java
@Around("execution(* com.luv2code.aopdemo.service.*.getFortune(..))")
public Object afterGetFortune(
        ProceedingJoinPoint theProceedingJoinPoint) throws Throwable{
            Object result = null; 

            try{
                /// let's execute the method
                result = theProceedingJoinPoint.proceed();
            }
            catch(Exception exc){

                /// log exception
                System.out.println("@Around advice : We have a problem" + exc);

                /// handle and give default fortune .... use this approach with caution. 
                /// other use cases also involve rethrowing the exception
                result = "Nothing exciting here. Move along!";
            }

            /// Calling program will never know that exception happened 
            return result; 
        }

```

## Implementation 
```
src
│   .DS_Store
│
└───com
    │   .DS_Store
    │
    └───luv2code
        │   .DS_Store
        │
        └───aopdemo
            │   Account.java
            │   AroundDemoApp.java
            │   AroundHandleExceptionDemoApp.java
            │   AroundWithLoggerDemoApp.java
            │   DemoConfig.java
            │   MainDemoApp.java
            │
            ├───aspect
            │       LuvAopExpressions.java
            │       MyApiAnalyticsAspect.java
            │       MyCloudLogAsyncAspect.java
            │       MyDemoLoggingAspect.java
            │
            ├───dao
            │       AccountDAO.java
            │       MembershipDAO.java
            │
            └───service
                    TrafficFortuneService.java
```
**AroundHandleExceptionDemoApp.java**
```Java
package com.luv2code.aopdemo;


import java.util.logging.Logger;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import com.luv2code.aopdemo.service.TrafficFortuneService;

public class AroundHandleExceptionDemoApp {
	
	private static Logger myLogger = 
						Logger.getLogger(AroundHandleExceptionDemoApp.class.getName());
	
	public static void main(String[] args) {

		// read spring config java class
		AnnotationConfigApplicationContext context =
				new AnnotationConfigApplicationContext(DemoConfig.class);
		
		// get the bean from spring container
		TrafficFortuneService theFortuneService = context.getBean("trafficFortuneService", TrafficFortuneService.class);
		
		myLogger.info("\nMain program: AroundDemoApp");
		
		myLogger.info("Calling getFortune");
		
		boolean tripWire = true; 
		String data = theFortuneService.getFortune(tripWire);
				
		myLogger.info("\nMy fortune is: "+data);
		
		myLogger.info("Finished");
		
		// close the context
		context.close();
	}

}
```
**MyDemoLoggingAspect.java**
```Java
package com.luv2code.aopdemo.aspect;

import java.util.List;
import java.util.logging.Logger;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
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
	private Logger myLogger = Logger.getLogger(getClass().getName());
	
	
	@Around("execution(* com.luv2code.aopdemo.service.*.getFortune(..))")
	public Object aroundGetFortune(ProceedingJoinPoint theProceedingJoinPoint) throws Throwable {
		/// print out the method we are advising on 
		String method = theProceedingJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @Around on method: "+method);
		
		/// get begin timestamp
        long begin = System.currentTimeMillis(); 

        /// now let's execute the method
        Object result = null; 
       
        try {
			result = theProceedingJoinPoint.proceed();
		} catch (Exception exc) {
			
			// log the exception
			myLogger.warning(exc.getMessage());
			
			// give the user a custom message
			result = "Major accident! But no worries, "
					+ "your private AOP helicopter is on the way!";
		}
      
        /// get end timestamp
        long end = System.currentTimeMillis();

        /// compute duration and display it. 
        long duration = end - begin; 
        myLogger.info("\n====>>> Duration: "+duration/1000.0 +"seconds");
        
        
        /// propagate the result to the 
        return result; 
	}
	
	@After("execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))")
	public void afterFinallyFindAccountAdvice(JoinPoint theJoinPoint) {
		/// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @After (finally) on method: "+method);
		
	}
	
	/// add a new advice for @AfterThrowing
	@AfterThrowing(
			pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
			throwing="theExc"
			)
	public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, Throwable theExc) {
		
		/// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @AfterThrowing on method: "+method);
		
		/// Display the exception
		myLogger.info("\n=====>>> The exeption is "+ theExc);
	}
	
	/// add a new advice for @AfterReturning 
	@AfterReturning(
			pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
			returning="result"
			)
	public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, List<Account> result) {
	
		// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @AfterReturning on method: "+method);
		
		// print out the results of the method call 
		myLogger.info("\n=====>>> Result is : "+result);
		
		/// lets post process the data ... lets modify it 
		
		/// convert the account names to upper case 
		convertAccountNamesToUpperCase(result);
		
		/// print out the results
		myLogger.info("\n=====>>> Result is : "+result);
	}
	
	
	private void convertAccountNamesToUpperCase(List<Account> result) {
		
		for(Account theAccount: result) {
			theAccount.setName(theAccount.getName().toUpperCase());
		}
		
	}


	// They will only be applied which are not getter or setter
	@Before("com.luv2code.aopdemo.aspect.LuvAopExpressions.forDaoPackageNoGetterSetter()")
	public void beforeAddAccountAdvice(JoinPoint theJoinPoint) {
			myLogger.info("\n====>>> Executing @Before advice on method");
			
			// display the signature 
			MethodSignature methodSig = (MethodSignature) theJoinPoint.getSignature();
			
			myLogger.info("Method: "+methodSig);
			
			// display the method arguments 
			
			// get the args 
			 Object[] args = theJoinPoint.getArgs();
			 
			 // loop thru args 
			   for(Object tempArg : args){
			        myLogger.info(tempArg.toString());
			        
			        if(tempArg instanceof Account) {
			        	// downcast and print all account related stuff
			        	Account theAccount = (Account)tempArg; 
			        	myLogger.info("Account Name: "+theAccount.getName());
			        	myLogger.info("Account Level: "+theAccount.getLevel());
			        	
			        }
			   }
			
	}
}
```

**TrafficFortuneService.java**
```Java
package com.luv2code.aopdemo.service;

import java.util.concurrent.TimeUnit;

import org.springframework.stereotype.Component;

@Component
public class TrafficFortuneService {
	
	public String getFortune() {
		try {
		// simulate a delay 
		TimeUnit.SECONDS.sleep(5);
		}
		catch(InterruptedException exc) {
			exc.printStackTrace();
		}
		
		
		// return a fortune
		return "Expect Heavy traffic this morning";
		
	}

	/// simulating an exception
	public String getFortune(boolean tripWire) {
		if(tripWire) {
			throw new RuntimeException("Major accident! Highway is closed!");
		}
		return getFortune();
	}
}
```

## Rethrowing Exception
```Java
@Around("execution(* com.luv2code.aopdemo.service.*.getFortune(..))")
public Object afterGetFortune(
        ProceedingJoinPoint theProceedingJoinPoint) throws Throwable{
            Object result = null; 

            try{
                /// let's execute the method
                result = theProceedingJoinPoint.proceed();
            }
            catch(Exception exc){

                /// log exception
                System.out.println("@Around advice : We have a problem" + exc);

				/// rethrow it
                throw exc; 
            }

            /// Calling program will never know that exception happened 
            return result; 
        }

```

## Implementation
**MyDemoLoggingAspect.java**
```Java
package com.luv2code.aopdemo.aspect;

import java.util.List;
import java.util.logging.Logger;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
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
	private Logger myLogger = Logger.getLogger(getClass().getName());
	
	
	@Around("execution(* com.luv2code.aopdemo.service.*.getFortune(..))")
	public Object aroundGetFortune(ProceedingJoinPoint theProceedingJoinPoint) throws Throwable {
		/// print out the method we are advising on 
		String method = theProceedingJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @Around on method: "+method);
		
		/// get begin timestamp
        long begin = System.currentTimeMillis(); 

        /// now let's execute the method
        Object result = null; 
       
        try {
			result = theProceedingJoinPoint.proceed();
		} catch (Exception exc) {
			
			// log the exception
			myLogger.warning(exc.getMessage());
			
			/// rethrow exception
			throw exc; 
		}
      
        /// get end timestamp
        long end = System.currentTimeMillis();

        /// compute duration and display it. 
        long duration = end - begin; 
        myLogger.info("\n====>>> Duration: "+duration/1000.0 +"seconds");
        
        
        /// propagate the result to the 
        return result; 
	}
	
	@After("execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))")
	public void afterFinallyFindAccountAdvice(JoinPoint theJoinPoint) {
		/// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @After (finally) on method: "+method);
		
	}
	
	/// add a new advice for @AfterThrowing
	@AfterThrowing(
			pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
			throwing="theExc"
			)
	public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, Throwable theExc) {
		
		/// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @AfterThrowing on method: "+method);
		
		/// Display the exception
		myLogger.info("\n=====>>> The exeption is "+ theExc);
	}
	
	/// add a new advice for @AfterReturning 
	@AfterReturning(
			pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
			returning="result"
			)
	public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, List<Account> result) {
	
		// print out the method we are advising on 
		String method = theJoinPoint.getSignature().toShortString();
		myLogger.info("\n=====>>> Executing @AfterReturning on method: "+method);
		
		// print out the results of the method call 
		myLogger.info("\n=====>>> Result is : "+result);
		
		/// lets post process the data ... lets modify it 
		
		/// convert the account names to upper case 
		convertAccountNamesToUpperCase(result);
		
		/// print out the results
		myLogger.info("\n=====>>> Result is : "+result);
	}
	
	
	private void convertAccountNamesToUpperCase(List<Account> result) {
		
		for(Account theAccount: result) {
			theAccount.setName(theAccount.getName().toUpperCase());
		}
		
	}


	// They will only be applied which are not getter or setter
	@Before("com.luv2code.aopdemo.aspect.LuvAopExpressions.forDaoPackageNoGetterSetter()")
	public void beforeAddAccountAdvice(JoinPoint theJoinPoint) {
			myLogger.info("\n====>>> Executing @Before advice on method");
			
			// display the signature 
			MethodSignature methodSig = (MethodSignature) theJoinPoint.getSignature();
			
			myLogger.info("Method: "+methodSig);
			
			// display the method arguments 
			
			// get the args 
			 Object[] args = theJoinPoint.getArgs();
			 
			 // loop thru args 
			   for(Object tempArg : args){
			        myLogger.info(tempArg.toString());
			        
			        if(tempArg instanceof Account) {
			        	// downcast and print all account related stuff
			        	Account theAccount = (Account)tempArg; 
			        	myLogger.info("Account Name: "+theAccount.getName());
			        	myLogger.info("Account Level: "+theAccount.getLevel());
			        	
			        }
			   }
			
	}
}
```