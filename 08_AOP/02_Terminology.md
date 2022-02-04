## AOP Terminology
- **Aspect** : module of code for a cross cutting concern(logging,security...)
- **Advice** : What action is taken and when it should be applied. 
- **Join Point** : When to apply code during program execution. 
- **Pointcut** : A predicate expression for where advice should be applied. 

## Advice Type 
- **Before advice**: run before the method 
- **After finally advice**: run after the method (finally)
- **After returning advice**: run after the method(success execution)
- **After throwing advice**: run after method(if exception thrown)
- **Around advice**: run before and after method

## Weaving 
- Connecting aspect to target objects to create an advised object. 
- Different types of weaving 
    - Compile time, load time or run time 
- Regarding performance: run-time weaving is the slowest. 

## AOP Frameworks
- Two leading AOP frameworks for Java. 
    - Spring AOP
    - AspectJ

## Spring AOP 
- Spring provides AOP support. 
- Key components of Spring 
    - Security, transactions, caching etc. 
- Uses run-time weaving of aspects. 
#### Advantages 
- Simpler to use than AspectJ
- Uses Proxy pattern
- Can migrate to AspectJ when using @Aspect annotation. 

#### Disadvantages
- Only support method-level join points 
- Can only apply aspects to beans created by Spring app context 
- Minor performance cost for aspect execution(run-time weaving)

## Aspect J 
- Original AOP framework, released in 2001
    - [](www.eclipse.org/aspectj)
- Provide complete support for AOP 
- Rich support for 
    - Join points: method-level, constructor, field 
    - code weaving: compile-time, post compile-time and load-time

#### Advantages 
- Support all join points 
- Works with any POJO, not just bans from app context. 
- Faster performance compared to Spring AOP. 
- Complete AOP support. 

#### Disadvantages
- Compile time weaving requires extra step . 
- AspectJ pointcut synctax can become complex. 

## Summary 
- **Sprig AOP**
- Method level join points, Run time code weaving (slower than AspectJ)
- Spring AOP is a light implementation of AOP 
- Solved common problems in enterprise applications 
- *Recommendation*:
    - Start with Spring AOP...easy to get started with
    - If you have complex requirement then move to AspectJ

- **AspectJ**
- join points : method level and constructor level, weaving: compile time, post compile time, and load time

## Addittional Resources. 
- Spring reference manual: www.spring.io
- Aspect J in action
    - by Raminvas Laddad
- Aspect Oriented Developement with Use Cases
    - by Ivar Jacobson and Pan-Wei Ng


## Roadmap 
- Create Aspects
- Develop Advices 
    - Before , after returning , after throwing 
    - After finally, around
- Create pointcut expressions
- Apply it to our big CRM project ( Spring MVC + Hibernate)