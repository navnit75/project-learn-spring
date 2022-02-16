## The Problem
- We saw how to create a DAO for Employee
- What if we need to create a DAO for another entity?
- Customer, Student, Product, Book …
- Do we have to repeat all of the same code again???

## Creating DAO
- You may have noticed a pattern with creating DAOs
- Most of the code is same 
```java
@Override
public Employee findById(int theId) {
    // get data
    Employee theData = entityManager.find(Employee.class, theId);
    // return data
    return theData;
}
```

## My Wish
- I wish we could tell Spring:
- Create a DAO for me
- Plug in my entity type and primary key
- Give me all of the basic CRUD features for free

## My Wish Diagram 

```
------------------------------------
Entity: Customer , Product, Employee
Primary Key : Integer 
-------------------------------------
findAll()
findById(…)
save(…)
deleteById(…)
… others …
```

## Spring Data JPA - Solution
- Spring Data JPA is the solution!!!!
- Create a DAO and just plug in your entity type and primary key
- Spring will give you a CRUD implementation for FREE …. like MAGIC!!
- Helps to minimize boiler-plate DAO code … yaaay!!!

## JpaRepository
- Spring Data JPA provides the interface: JpaRepository
- Exposes methods (some by inheritance from parents)
```
findAll()
findById(…)
save(…)
deleteById(…)
… others …
```

## Development Process Step-By-Step
1. Extend JpaRepository interface
2. Use your Repository in your app

## Step 1: Extend JpaRepository interface
- No need for implementation class
```java
/// Here Employee is Entity Type
/// Integer is Primary Key Type
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
// that's it ... no need to write any code LOL!
}
```

## JpaRepository Docs
- Full list of methods available … see JavaDoc for JpaRepository
`www.luv2code.com/jpa-repository-javadoc`

## Step 2: Use Repository in your app
```java
@Service
public class EmployeeServiceImpl implements EmployeeService {
    private EmployeeRepository employeeRepository;
    
    @Autowired
    public EmployeeServiceImpl(EmployeeRepository theEmployeeRepository) {
        employeeRepository = theEmployeeRepository;
    }

    @Override
    public List<Employee> findAll() {
        return employeeRepository.findAll();
    }
    …
}
```

## Advanced Features
- Advanced features available for
- Extending and adding custom queries with JPQL
- Query Domain Specific Language (Query DSL)
- Defining custom methods (low-level coding)
- [Advanced Features ](http://www.luv2code.com/spring-data-jpa-defining-custom-queries)
- [Java Optional(Data Type) Tutorials ](www.luv2code.com/java-optional-tutorial)