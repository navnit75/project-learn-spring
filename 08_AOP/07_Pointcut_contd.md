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