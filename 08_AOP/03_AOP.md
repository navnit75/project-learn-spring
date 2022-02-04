## Before Advice
```
main <------ AOP ------> logging <------ security------> Target  
app         Proxy         aspect     |    aspect          object
                                     |
                                     |
MainApp()                            v                     TargetObject 
                                Run custom code       public void doSomeStuff(){
// Call Target object----->     BEFORE the Target       ...
targetObj.doSomeStuff();        object method call    }
```
## Advice Interaction
```
Target Object
@Before-------------+ [Run Custom code BEFORE the target object method call]
                    |
                    V
            public void doSomeStuff(){
                ...
            }
                          ^
                          |
@AfterReturning<----------+[Run Custom code AFTEr the target object method call]
```

## Use cases
- Most Common
    - logging, security, transaction
- Audit Logging 
    - who, what, when, where
- API Management
    - how many times has a method been called user. 
    - analytics: what are peak times? what is average load? who is top user? 

## Adding AspectJ Jar file 
- Need to download AspectJ JAR file
- Even though we are using Spring AOP ... still need AspectJ JAR file
- Why? 
    - Spring AOP uses some of the AspectJ annotations
    - Spring AOP uses some of the AspectJ classes. 

## Development Process 
1. Create target object: AccountDAO
2. Create Spring Java Config class
3. Create Main app
4. Create an Aspect with @Before advice

## Dev work at `spring-demo-aop`

