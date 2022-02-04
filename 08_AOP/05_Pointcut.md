## Introduction
- **Pointcut**: A predicate expression for where advice should be applied 
- Spring AOP uses AspectJ's pointcut expression language
- We will start with `execution` pointcuts 
    - Applies to `execution` of methods 

## Pointcut expression language
```
execution(modifiers-pattern? return-type-pattern declaring-type-pattern? method-name-pattern(param-pattern) throws-pattern?)
```
- **modifiers-pattern**: (Spring AOP only supports public) [OPT]
- **return-type-pattern**: void,boolean,String, List<Customer>...
- **declaring-type-pattern**: the class name [OPT]
- **method-name-pattern**: method name to match 
- **param-pattern**: parameters types to match
- **throws-pattern**: exception types to match[OPT]
- PS: The pattern is optional if it has `?` 

## Matching method names 
- Match only `addAccount()` method in AccountDAO class
```Java
@Before("execution(public void com.luv2code.aopdemo.dao.AccountDAO.addAccount())")
```
- `public` : Modifier
- `void` : Return Type
- `com.luv2code.aopdemo.dao.AccountDAO` : Declaring Type 
- `addAccount()` : method

- Match any `addAccount()` method in any class
```Java
@Before("execution(public void addAccount())")
```

- Match methods starting with `add` in any class using `WILDCARDS`
```Java
@Before("execution(public void add*())")
```

- Match methods starting with `processCreditCard` in any class using `WILDCARDS`
```Java
@Before("execution(public VerificationResult processCreditCard*())")
```

- Match methods with any return type 
```Java
@Before("execution(public * processCreditCard*())")
```
    - as modifier is optional ... so we can drop it 
```Java
@Before("execution(* processCreditCard*())")
```