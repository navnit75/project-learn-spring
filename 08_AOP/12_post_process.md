## Post Process data using @AfterReturning
- Use case 
```Java
@AfterReturning(
			pointcut="execution(* com.luv2code.aopdemo.dao.AccountDAO.findAccounts(..))",
			returning="result"
			)
	public void afterReturningFindAccountAdvice(JoinPoint theJoinPoint, List<Account> result) {
		
        /// modify "Result" list: add, remove ,update etc
        if(!result.isEmpty()){
            Account tempAccount = result.get(0);
            tempAccount.setName("Daffy Duck");
        }
	}
```


- Calling Program
```Java
/// call method to find the accounts 
List<Account> theAccounts = theAccountDAO.findAccounts(); 
                 ^                             |
                 |                             |
                 |      @AfterReturning        |  
                 +-----------------------------+
                            Logging
                            Aspect
```
- Be careful about using `LoggingAspect` 
- Imagine of a situation being one of the Junior Developer , who is working on this project and Doesn't know about AOP. 
- You may get confuse with the output produced by functions. 
- You may think that there is something wrong with the application , which can lead to uneccessary changes in the application. 
- Which will lead to confusion among the team. 
- Hence all the developers needs to notified about the ASPECTS in the application. 


## Modification Impl
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
	
	/// add a new advice for @AfterReturning 
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
		
		/// lets post process the data ... lets modify it 
		
		/// convert the account names to upper case 
		convertAccountNamesToUpperCase(result);
		
		/// print out the results
		System.out.println("\n=====>>> Result is : "+result);
	}
	
	
	private void convertAccountNamesToUpperCase(List<Account> result) {
		
		for(Account theAccount: result) {
			theAccount.setName(theAccount.getName().toUpperCase());
		}
		
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

=====>>> Result is : [Account [name=JOHN, level=Silver], Account [name=MADHU, level=Platinum], Account [name=LUCA, level=Gold]]


Main Program: AfterReturningDemoApp
---
[Account [name=JOHN, level=Silver], Account [name=MADHU, level=Platinum], Account [name=LUCA, level=Gold]]
---

```